# 使用 SQLAlchemy 作为后端，在 Python 中实现存储库模式

> 原文：<https://betterprogramming.pub/implementing-the-repository-pattern-in-python-using-sqlalchemy-as-a-backend-9f04c89fdf11>

## Python 中的领域驱动设计

![](img/3c90aabd1a778c22816dcb88a002e0ec.png)

由[拍摄的科尔顿鲟鱼](https://unsplash.com/@coltonsturgeon?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

> 本文是关于用 Python 实现领域驱动设计战术模式的系列文章的一部分。这里的目标是建立一个 web 应用程序，允许卖家列出待售物品，买家对他们愿意购买的物品出价(又名易贝克隆)——关于该项目的更多信息可以在这里找到。

在[上一篇文章](https://dddinpython.com/index.php/2022/09/23/implementing-the-repository-pattern/)中，我们实现了一个简单的基于文件的存储库，它使用了`pickle`模块。由于我们没有使用数据库，所以我们不必创建表和列，所有的域模型都直接从内存序列化为字节流(反之亦然)。这种方法可以引入存储库的概念，但是在生产系统中，我们需要一些数据库来存储应用程序状态。

顺便提一下，我上次写道，存储库用于加载和保存实体。存储库使用集合(实体的组合)，但是为了简单起见，我坚持使用实体。

这次我们将使用 [SQL 炼金术](https://www.sqlalchemy.org/)作为实体的持久化机制。我们将保留原来的接口，但是所有的实现细节都将封装在`SqlAlchemyListingRepository`中。让我们从修改后的`ListingRepository`接口开始，它额外支持添加、删除和持久化`Listing`域实体:

```
# somewhere in the domain layer

class ListingRepository(metaclass=abc.ABCMeta):
    """An interface to listing repository"""

    @abc.abstractmethod
    def add(self, entity: Listing):
        """Adds new entity to a repository"""
        raise NotImplementedError()

    @abc.abstractmethod
    def remove(self, entity: Listing):
        """Removes existing entity from a repository"""
        raise NotImplementedError()

    @abc.abstractmethod
    def get_by_id(id: ListingId) -> Listing:
        """Retrieves entity by its identity"""
        raise NotImplementedError()

    def __getitem__(self, index) -> Listing:
        return self.get_by_id(index)
```

在领域驱动的设计中，存储库的目标是封装访问领域对象所需的逻辑。因此，存储库的接口是域层的一部分，而该接口的实际实现属于基础设施层。因此，域代码保持干净——它与任何技术问题隔离开来，并且是数据库不可知的。

# 一点理论

在我们开始实施 SQL Alchemy 存储库之前，我想介绍和讨论一些概念:

## 1.ORM 模型≠域对象

实体是 [Python 类](https://docs.python.org/3/tutorial/classes.html)——这并不奇怪。它们包含属性:基本类型、值对象、枚举、对其他实体的引用以及以上所有内容的集合。在内存中，这形成了嵌套字段的图形结构。提醒一下，这是我们的`Listing`实体的样子(此时，我们不关心逻辑):

```
@dataclass
class Listing(Entity):
    id: int
    name: str
    min_price: Money
    ....
```

另一方面，数据库组织数据是平面的，无格式的。Python 类和关系数据模型不能一起工作，ORM 的引入就是为了解决这个问题。然而，由于模型属性和表列之间的[映射](https://docs.sqlalchemy.org/en/14/orm/tutorial.html#declarea-mapping)，ORM 模型仍然与数据库和特定的 ORM 实现紧密耦合:

```
class ListingModel(ModelBase):
    __tablename__ = "listing"
    id = Column(Integer, primary_key=True)
    name = Column(String)
    min_price__amount = Column(Integer)
    min_price__currency = Column(String(3))
```

这引发了两个问题:

1.ORM 模型不应该用在领域层。与领域对象相比，ORM 模型更难测试(它们需要一个数据库存在)。此外，我们希望业务层保持干净，不依赖于底层存储机制。`ListingModel`与 SQL Alchemy 紧密耦合，在业务层有这样的依赖是不可行的。然而，在某些情况下使用 SQLA 模型仍然很好，例如，如果我们做不需要业务逻辑的基本 CRUD 操作。

2.没有简单的方法将多字段值对象(即由`amount`和`currency`组成的`min_price`T3)分配给模型中的多个列。理论上，我们可以为值对象添加一些 getters 和 setters:

```
class ListingModel(ModelBase):
    ...
    min_price__amount = Column(Integer)
    min_price__currency = Column(String(3))

    def get_min_price(self) -> Money:
        return Money(self.min_price__amount, self.min_price__currency)

    def set_min_price(self, value: Money):
        self.min_price__amount = value.amount
        self.min_price__currency = value.currency
```

…但是这与值对象不可变的想法相矛盾(您仍然可以更改逻辑上属于值对象的模型的单个属性)。

为了克服这些问题，我们需要一种在业务层之间传输数据的机制——数据映射器。

## 2.数据映射器

我们已经知道我们不想在领域层使用模型。模型是 SQL Alchemy 特有的，由列、外键、关系等组成。我们不希望任何基础设施细节泄露到域中。我们仍然可以使用模型来查询数据(因为可以通过 repos 之外的渠道查询数据库)，但这超出了本文的范围。

我们需要做的是:
-当从 repo 中读取数据时，将模型映射到实体(即`get_by_id(…)`调用返回一个域实体)，
-当将要保存任何数据更改时，将实体映射到模型。

这可以通过两种方式实现:

1.我们可以使用 SQL 炼金术[命令式映射](https://docs.sqlalchemy.org/en/20/orm/mapping_styles.html#imperative-mapping)将数据从 SQL 表映射到纯 Python 类。[宇宙 Python](https://www.cosmicpython.com/book/chapter_02_repository.html#_inverting_the_dependency_orm_depends_on_model) 遵循这种方法。在这里，模型的所有属性都通过`mapper_registry.map_imperatively()`自动地从/到域对象进行转换。然而，这种技术仅限于只包含基本类型的模型。如果我们的领域模型有多属性值对象，我们需要一个更复杂的方法。

2.我们可以实现我们自己的数据映射器，在我们的领域模型之间进行转换。为了进行映射，我们需要两个函数:`XYZ_model_to_entity`和`XYZ_entity_to_model`。存储库将使用数据映射器来完成所有的翻译。这是我们在实施`SqlAlchemyListingRepository`时将要采取的方法。

## 3.身份地图

存储库模式中的一个基本元素是[身份映射](https://en.wikipedia.org/wiki/Identity_map_pattern)，它通过提供特定于上下文的内存缓存来防止在单个事务期间从数据库中重复检索相同的对象数据，从而提高性能。这里有另一个定义:

> 身份映射记录了在单个业务事务中从数据库中读取的所有对象。每当您想要一个对象时，您首先检查身份映射以查看您是否已经拥有它。
> —马丁·福勒

这非常简单:当我们查询实体的存储库时，我们将首先检查身份映射。如果实体已经存在于地图中，存储库将返回对缓存对象的引用。否则，我们将从数据库中读取模型，用数据映射器转换它，将它存储在身份映射中，并返回一个引用。在保存实体时，我们将使用数据映射器将其转换为模型实例，然后使用内置的 SQL Alchemy 会话机制持久化它。

# 存储库模式实现

现在我们知道了存储库的构建块是什么，让我们看看实现 SQL Alchemy 清单存储库所需的所有部分。

## 域对象

这是我们的领域模型的数据部分。为了清楚起见，所有的逻辑都从域对象中删除了。

```
from uuid import UUID
from dataclasses import dataclass

# some type aliases
ListingId = UUID
BidderId = UUID

@dataclass
class Money:
    """A value object that represents money"""
    amount: int
    currency: str

@dataclass
class Bid:
    """A value object that represents a bid placed on a listing by a buyer"""
    bidder_id: BidderId
    price: Money

@dataclass
class Listing(Entity):
    """An entity that represents a listing with an ask price and all the bids already placed on this item"""
    id: int
    name: str
    min_price: Money
    bids: List[Bid] = field(default_factory=list)
```

这里有一些有趣的事情需要注意。一个`Listing`由一些原语类型(即`id`、`name`)、一个单值对象(`min_price`)和一个值对象列表(`bids`)组成。出于某种未公开的原因，让我们假设我们有意使用一个列表，这样我们就可以保持出价的顺序(否则，我们可以使用一个无序的`set`)。

## 数据模型

上述域对象反映为数据库模型，如下所示:

```
import uuid
from infrastructure.sqlalchemy_common import Base, Column, UUID, String, Integer, ForeignKey, relationship

UniqueIdentifer = UUID(as_uuid=True)

class BidModel(Base):
    """ Stores Bid value object"""
    __tablename__ = "bid"
    # composite primary key
    listing_id = Column(UniqueIdentifier,
                        ForeignKey("listing.id"),
                        primary_key=True)

    # since bids are stored in an ordered collection (list), an index column is required
    idx = Column(Integer, primary_key=True)

    bidder_id = Column(UniqueIdentifier)
    price__amount = Column(Integer)
    price__currency = Column(String(3))

    # parent relationship
    listing = relationship("ListingModel", back_populates="bids")

class ListingModel(Base):
    __tablename__ = "listing"

    id = Column(UniqueIdentifier, primary_key=True, default=uuid.uuid4)
    name = Column(String(30))

    min_price__amount = Column(Integer)
    min_price__currency = Column(String(3))

    bids = relationship("BidModel",
                        order_by="BidModel.idx.asc()",
                        cascade="save-update, merge, delete, delete-orphan")
```

如你所见，我们没有单独的桌子；这些作为原始字段存储在相应的模型中(即`price__amount`、`price__currency`和`min_price__amount`、`min_price__currency`)。然而，我们将`bids`存储在单独的`BidModel`中，因为`ListingModel`和`BidModel`之间存在一对多的关系。

此外，出价按`idx`字段排序，以保持`Listing.bids`列表中元素的顺序，并且设置了一个`cascade`选项，以在从列表中移除`Bid`值对象时保持`Listing.bids`和表格行同步。另外，请注意，它使用由`listing_id`和`idx`组成的复合主键，因为它足以在数据库级别识别一个出价。

## 数据映射器

数据映射器的逻辑并不奇特——只是从一种类型到另一种类型的相当乏味的映射。需要注意的一个有趣的小细节是打包/解包值对象。

```
from infrastructure.models import ListingModel, BidModel
from domain.entities import Listing
from domain.value_objects import Money, Bid

def listing_model_to_entity(instance: ListingModel) -> Listing:
    def map_bid_to_value_object(bid: BidModel) -> Bid:
        return Bid(
            bidder_id=bid.bidder_id, 
            price=Money(amount=bid.price__amount, currency=bid.price__currency)
        )

    return Listing(
        id=instance.id,
        name=instance.name,
        min_price=Money(amount=instance.min_price__amount, currency=instance.min_price__currency),
        bids=[map_bid_to_value_object(bid) for bid in instance.bids]
    )

def listing_entity_to_model(listing: Listing, existing=None) -> ListingModel:
    def map_bid_to_model(idx: int, bid: Bid) -> BidModel:
        return BidModel(bidder_id=bid.bidder_id, price__amount=bid.price.amount, price__currency=bid.price.currency, idx=idx)

    return ListingModel(
        id=listing.id,
        name=listing.name,
        min_price__amount=listing.min_price.amount,
        min_price__currency=listing.min_price.currency,
        bids=[map_bid_to_model(idx, bid) for idx, bid in enumerate(listing.bids)]
```

## 列表存储库

最后，让我们看看存储库的实现:

```
from sqlalchemy.orm import Session
from domain.repositories import ListingRepository
from domain.entities import Listing
from infrastructure.models import ListingModel
from infrastructure.data_mappers import listing_model_to_entity, listing_entity_to_model

# a sentinel value for keeping track of entities removed from the repository
REMOVED = object()

class SqlAlchemyListingRepository(ListingRepository):
    """SqlAlchemy implementation of ListingRepository"""

    def __init__(self, session: Session, identity_map=None):
        self.session = session
        self._identity_map = identity_map or dict()

    def add(self, entity: Listing):
        self._identity_map[entity.id] = entity
        instance = listing_entity_to_model(entity)
        self.session.add(instance)

    def remove(self, entity: Listing):
        self._check_not_removed(entity)
        self._identity_map[entity.id] = REMOVED
        listing_model = self.session.query(ListingModel).get(entity.id)
        self.session.delete(listing_model)

    def get_by_id(self, id):
        instance = self.session.query(ListingModel).get(id)
        return self._get_entity(instance, listing_model_to_entity)

    def get_by_name(self, name):
        instance = self.session.query(ListingModel).filter_by(name=name).one()
        return self._get_entity(instance, listing_model_to_entity)

    def _get_entity(self, instance, mapper_func):
        if instance is None:
            return None
        entity = listing_model_to_entity(instance)
        self._check_not_removed(entity)

        if entity.id in self._identity_map:
            return self._identity_map[entity.id]

        self._identity_map[entity.id] = entity
        return entity

    def __getitem__(self, key):
        return self.get_by_id(key)

    def _check_not_removed(self, entity):
        assert self._identity_map.get(entity.id, None) is not REMOVED, f"Entity {entity.id} already removed"

    def persist(self, entity: Listing):
        self._check_not_removed(entity)
        assert entity.id in self._identity_map, "Cannon persist entity which is unknown to the repo. Did you forget to call repo.add() for this entity?"
        instance = listing_entity_to_model(entity)
        merged = self.session.merge(instance)
        self.session.add(merged)

    def persist_all(self):
        for entity in self._identity_map:
            if entity is not REMOVED:
                self.persist(entity)
```

这里有几件事值得注意:

*   我们正在通过`__init__`传递 SQL 炼金术`Session`和身份映射实例。每个业务事务应该有一个会话和一个身份映射。
*   当向存储库添加一个新实体时，我们将实体存储在身份映射中，并将相应的模型存储在 SQL Alchemy 会话中。
*   当实体状态被改变时
    ——为了保持对实体所做的改变，使用`persist()`或`persist_all()`方法。当一个实体被持久化时，它的状态使用`listing_entity_to_model`数据映射器被转换回模型。
    - `persist()`变更和`commit()`会话不是存储库的责任，这也是我们的目的。协调对数据库的写入应该由一个工作单元来处理。
    -这个存储库实现在内存消耗方面并不理想(实际上，我们在这里使用了两个身份映射:一个由回购协议使用，另一个是 SQLA 会话的一部分)，但是我认为它对于我们的目的来说已经足够好了。

# 使用案例

这就是我们如何通过`publish_listing_use_case` 函数发布清单:

```
engine = create_engine("sqlite+pysqlite:///:memory:",
                       echo=True,
                       future=True)
Base.metadata.create_all(engine)

def publish_listing_use_case(listing_id: ListingId, repository: ListingRepository):
    listing = repository.get_by_id(listing_id)
    listing.publish()

def execute_publish_listing_via_unit_of_work():
    identity_map = []
    with Session(engine) as session:
        repository = SqlAlchemyListingRepository(session, identity_map)
        publish_listing_use_case(listing_id=..., repository=repository)
        repository.persist_all()

execute_publish_listing_via_unit_of_work()
```

正如我们所看到的，存储库没有将实体状态显式保存在数据库中。所有繁重的工作都由负责创建会话、实例化存储库、调用用例函数和保存所有结果的`execute_publish_listing_via_unit_of_work`函数来处理。通常，这是一个[工作单元](https://martinfowler.com/eaaCatalog/unitOfWork.html)的职责。

# 结论

正如我们所看到的，实现存储库模式需要做大量的工作。我们必须为我们的域对象定义一个数据模型，配置这两者之间的映射，然后实现存储库。我们还需要一些逻辑来保持模型实例和实体的同步，这样，如果实体发生变化，它将自动保存在数据库中。另一层抽象无疑意味着更多的工作。那么，这一切值得吗？我们应该什么时候使用它？有什么好处？

首先，如果我们打算通过执行业务逻辑来改变实体的状态，我们应该使用存储库模式。如果我们只想查询数据(即搜索、排序、过滤)并将其显示在屏幕上，这将是大材小用。

关于好处:我们得到了关注点的分离——业务层不需要知道数据源，也不需要跟踪变化。存储库是可互换的——从长远的角度来看，代码更容易测试和维护。

***

*本文最初发表于*[*DDD Python 中的*](https://dddinpython.com/index.php/2022/11/09/implementing-the-repository-pattern-using-sqlalchemy/)