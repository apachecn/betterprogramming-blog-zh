# 使用 Regex 从 SQL 表创建 Python 模型

> 原文：<https://betterprogramming.pub/creating-python-models-from-sql-tables-with-regex-1e1bfd95ece6>

## 从 SQL 表写几十个 Python 模型？你总是可以使用正则表达式

![](img/84566aaa0684cc40b323ef7fd990bfa0.png)

Photo by 李进: [https://www.pexels.com/photo/low-angle-photography-of-gray-spiral-building-3172740/](https://www.pexels.com/photo/low-angle-photography-of-gray-spiral-building-3172740/)

在本文中，我们将回顾读取 SQL 文件的步骤，并将所有的`CREATE TABLE`语句与一个非常简单的正则表达式模式进行匹配，然后从所述表中编写 Python 类/模型的字符串。最终代码[在这里。](https://github.com/AlbertoV5/psql-to-models)

注意，在这个练习中，我们将使用 Python 3.10。这些结果设计用于 FastAPI 0.86.0、SQLAlchemy 1.4 和 Pydantic 1.10。我们也在遵循[这种](https://github.com/faraday-academy/fast-api-lms)风格的 FastAPI，我们使用的是 PostgreSQL 14.5。

最后，我们将使用 [this](https://github.com/MIT-LCP/mimic-code/blob/main/mimic-iii/buildmimic/postgres/postgres_create_tables.sql) SQL 模式作为我们的用例，因此我们希望将所有 26 个没有分区的表转换为 SQLAlchemy/Pydantic 模型。

```
python --version
```

```
Python 3.10.7
```

# 从 main 开始

这将是一个回顾性的过程，所以我们将从主要功能开始，然后从那里往下。

我们想读取一个`.sql`文件，然后输出 2。py 文件，一个用于 SQLAlchemy 模型，另一个用于 Pydantic 模型，其中包括模式匹配和字符串处理。一旦我们有了实现细节，我们将回到主函数，但是现在，让我们从头开始看一下。

```
def main(argv: Arguments):
    """Read .sql file with CREATE TABLE queries and store models"""
    with open(Path(argv.input).resolve()) as file:
        sql_string = file.read()
    # Match pattern
    pattern = r"(?:CREATE TABLE )((.|\n*?));"
    tables: list[Table] = [
        Table.from_string(table[0]) for table in re.findall(patterb, sql_string)
    ]
    # String processing
    alchemy_data = ALCHEMY_HEADER
    pydantic_data = PYDANTIC_HEADER
    for model, pydantic in generate_strings(tables):
        alchemy_data += model
        pydantic_data += pydantic
    # Output
    with open(Path(argv.alchemy).resolve(), "w") as file:
        file.write(alchemy_data)
    with open(Path(argv.pydantic).resolve(), "w") as file:
        file.write(pydantic_data)
```

正则表达式模式说:“查找 CREATE TABLE，但不包括它”，然后“包括由任何东西或换行符组成的任何数量的子组”，然后“以非贪婪的方式重复该子组任何次数”，以及“以分号结束”。非贪婪部分意味着它将匹配尽可能少的重复，所以我们停在“；”。

既然我们已经有了大致的了解，我们可以进入具体的方法、类和常量。

# 标题常量

我们正在生成依赖于其他库的 python 文件，因此我们将创建与我们当前用例的导入相匹配的字符串。我们需要 SQLAlchemy 类型以及 Alchemy 文件的`declarative base`,以及 Pydantic 文件的 Pydantic BaseModel 和 datetime 类型。

```
ALCHEMY_HEADER = '''"""
SQLAlchemy Models
"""
from sqlalchemy import Column, Integer, String, CHAR, TIMESTAMP, SmallInteger
from sqlalchemy.dialects.postgresql import DOUBLE_PRECISION
from db.setup import Base
'''

PYDANTIC_HEADER = '''"""
Pydantic Models
"""
from pydantic import BaseModel
from datetime import datetime
'''
```

# 助手功能

回想我们的主过程，编写输出文件之前的最后一步是使用`generate_strings`函数创建两个模型字符串。这个函数将遍历一个表对象列表，这些表对象表示我们之前通过 Regex 匹配的每个 SQL 表字符串，然后调用创建模型字符串的方法(用 Python 语法)并将它们交给主过程。我们使用`is_valid`函数作为正则表达式模式的“拐杖”,因为我们不介意匹配所有的表，然后过滤掉不需要的表。

```
BANNED_TABLES = ["chartevents_"]

def is_valid(table: Table) -> bool:
    """Whether table is not in banned tables."""
    for banned in BANNED_TABLES:
        if banned in table.name:
            return False
    return True

def generate_strings(tables: list[Table]) -> Generator[tuple[str, str], None, None]:
    """Yields SQLAlchemy and Pydantic Models as strings for each table."""
    for table in tables:
        table.get_constraints()
        alchemy = f"\n{table.make_alchemy()}" if is_valid(table) else ""
        pydantic = f"\n{table.make_pydantic()}" if is_valid(table) else ""
        yield alchemy, pydantic
```

# 总管的顶部和底部

在查看“Table”类的实现之前，我们将在 main 函数和参数解析器中包含导入，以便向主文件添加上下文。

```
from typing import Generator, Protocol
from argparse import ArgumentParser
from pathlib import Path
import re

from .table import Table

class Arguments(Protocol):
    input: str
    alchemy: str
    pydantic: str
```

我们的论点是文件的位置。这个文件的名字是`__main__.py`，因为我们使用这个工具作为一个模块，我们可以从命令行调用。更多信息请点击这里:[https://github.com/AlbertoV5/psql-to-models](https://github.com/AlbertoV5/psql-to-models)

```
if __name__ == "__main__":
    default_sql_input = "./schema.sql"
    default_alchemy_output = "./models_alchemy.py"
    default_pydantic_output = "./models_pydantic.py"
    args = ArgumentParser(
        prog="Convert PostgreSQL schema to SQLAlchemy and Pydantic Models.",
        usage=f"python -m psql-to-models",
        description="Regex-match the .sql schema file and outputs SQLAlchemy and Pydantic models as .py files.",
    )
    args.add_argument(
        "-i",
        "--input",
        metavar="input",
        default=f"{default_sql_input}",
        help=f"PostgreSQL Schema input file. Defaults to {default_sql_input}.",
    )
    args.add_argument(
        "-a",
        "--alchemy",
        metavar="output_alchemy",
        default=f"{default_alchemy_output}",
        help=f"SQLAlchemy Models output. Defaults to {default_alchemy_output}",
    )
    args.add_argument(
        "-p",
        "--pydantic",
        metavar="output_pydantic",
        default=f"{default_pydantic_output}",
        help=f"Pydantic Models output. Defaults to {default_pydantic_output}",
    )
    main(args.parse_args())
```

# 表格类

现在我们已经有了程序的整体逻辑，我们将转到“Table”类，它表示整个 SQL 表语句，并具有处理它和创建模型字符串的方法。

```
"""
Table representation and processor.
Call any get_* functions before calling make_* functions.
"""
from dataclasses import dataclass
from .column import Column

@dataclass
class Table:
    name: str
    columns: list[Column]

    @classmethod
    def from_string(cls, table: str) -> "Table":
        """Create Table from string."""
        data = table.split("\n")
        return Table(
            name=data[0], columns=[Column.from_string(col) for col in data[2:-1]]
        )

    def get_constraints(self) -> None:
        """Use the CONSTRAINT columns to modify the rest of the columns."""
        constraints = [col.params for col in self.columns if col.name == "CONSTRAINT"]
        data = [c.replace(")", "").split("(") for c in constraints]
        data = {k.strip(): v.strip() for v, k in data}
        for column in self.columns:
            for k in data:
                if column.name == k:
                    column.primary_key = (
                        "primary_key=True" if data[k] == "PRIMARY KEY" else ""
                    )
                    column.unique = "unique=True" if data[k] == "UNIQUE" else ""

    def make_alchemy(self) -> str:
        """Make SQLAlchemy model string from this Table."""
        cols = "\n    ".join(
            col.make_alchemy_column()
            for col in self.columns
            if col.name != "CONSTRAINT"
        )
        return (
            f"class {self.name.capitalize()}(Base):\n\n"
            f'    __tablename__ = "{self.name.lower()}"\n\n'
            f"    {cols}\n\n"
        )

    def make_pydantic(self) -> str:
        """Make Pydantic model string from this Table."""
        params = "\n    ".join(
            col.make_pydantic_column()
            for col in self.columns
            if col.name != "CONSTRAINT"
        )
        return (
            f"class {self.name.capitalize()}(BaseModel):\n\n"
            f"    {params}\n\n"
            f"    class Config:\n"
            f"        orm_mode = True\n\n"
        )
```

注意，我们要使用的构造函数是一个类方法。我们可以选择一个 post-init 进程，或者我们可以将它创建为一个外部工厂函数，但是为了方便起见，我们选择将它保留为 Table 类的一部分。Table 类的设计是将处理分成处理列列表中信息的`get`方法和将列数据转换成字符串的`make`方法。

Column 类类似于 Table，但是专门解析列的 SQL 语句(“column_name INT NOT NULL”)，并且它具有为模型的属性创建字符串的方法。

我们使用`get_constraints`在列列表中查找`CONSTRAINT` SQL 语句，然后修改实际列的属性，这包括`primary_key` 属性、`unique`属性等。

# 列类

我们需要将单个 SQL 语句拆分成不同的属性，我们可以用它们来描述名称或类型，以及表达约束，比如在 SQLAlchemy 中将 unique 表示为“UNIQUE ”,或者在 Pydantic 中将 NOT NULL 表示为“optional”。

这个类可以命名为“Statement ”,因为它包括任何其他可能不是“column”的 SQL 语句，但是我们在表级别对它们进行归纳和过滤。

```
"""
Column representation and processor.
"""
from dataclasses import dataclass
from .types import TYPE_LOOKUP

@dataclass
class Column:
    name: str
    type: str
    params: str
    primary_key: str = ""
    unique: str = ""

    @classmethod
    def from_string(cls, col: str) -> "Column":
        """Create column from string."""
        data = col.strip().replace(",", "").split(" ", maxsplit=2)
        return Column(
            name=data[0], type=data[1], params=data[2] if len(data) > 2 else ""
        )

    def get_type(self, sqlalchemy: bool = True) -> str:
        """
        Lookup types and return either SQLAlchemy's or Pydantic's.
        """
        t = self.type.replace(")", "").split("(")
        index = 0 if sqlalchemy else 1
        if len(t) > 1:
            return (
                f"{TYPE_LOOKUP[t[0]][index]}({t[1]})"
                if sqlalchemy
                else TYPE_LOOKUP[t[0]][index]
            )
        return TYPE_LOOKUP[self.type][index]

    def make_alchemy_column(self) -> str:
        """Make SQLAlchemy Model column string."""
        return (
            f"{self.name.lower()} = Column("
            f"{self.get_type()}"
            f"{', nullable=False' if 'NOT NULL' in self.params else ''}"
            f"{'' if self.primary_key == '' else f', {self.primary_key}'}"
            f"{'' if self.unique == '' else f', {self.unique}'}"
            f")"
        )

    def make_pydantic_column(self) -> str:
        """Make Pydantic Model column string."""
        return (
            f"{self.name.lower()}: "
            f"{self.get_type(sqlalchemy=False)}"
            f"{'' if 'NOT NULL' in self.params else ' | None'}"
        )
```

我们重复了“类方法作为构造函数”模式以及 Table 类中的`get`然后`make` 模式，但这次我们包括了`TYPE_LOOKUP`常量，它将帮助我们将 SQL 类型“翻译”成我们感兴趣的其他类型。

请注意，可选模式被转换为“新的”Python 3.10 语法，因此应该对其进行修改，以支持以前 Python 版本的“可选或联合”。

# 类型查找表

`TYPE_LOOKUP`字典将作为一个查找表，我们可以在其中找到每种 SQL 类型的 Python 等价体(在本例中是 PostgreSQL ),并保存在它自己的`types.py`文件中。

```
"""Define constants for type lookup."""

TYPE_LOOKUP: dict[str, tuple[str, str]] = {
    "INT": ("Integer", "int"),
    "SMALLINT": ("SmallInteger", "int"),
    "VARCHAR": ("String", "str"),
    "TIMESTAMP": ("TIMESTAMP", "datetime"),
    "DOUBLE": ("DOUBLE_PRECISION", "float"),
    "CHAR": ("CHAR", "str"),
    "TEXT": ("String", "str"),
}
"""Values are tuples of SQLAlchemy Model Type and Pydantic/Python Type."""
```

这迫使我们使用元组而不是新级别的键来访问每个值，因为我们假设我们将只对两个目标模型使用该工具，另外任何其他模型都可以通过扩展元组来简单地包含。

# 返回主页

现在我们已经了解了处理字符串的方法，我们可以回过头来回顾一下主过程。它可以概括为:

1.  匹配正则表达式模式。
2.  用我们的数据类存储和处理字符串。
3.  创建一个新的“Python 字符串”, Python 理解为 SQLAlchemy 和 Pydantic 模型。

```
# Pattern Matching
pattern = r"(?:CREATE TABLE )((.|\n)*?);"
tables: list[Table] = [
    Table.from_string(table[0]) for table in re.findall(pattern, sql_string)
]
# String Processing
alchemy_data = ALCHEMY_HEADER
pydantic_data = PYDANTIC_HEADER
for model, pydantic in generate_strings(tables):
    alchemy_data += model
    pydantic_data += pydantic
```

在每一个用例中，我们需要改变的值是标题常量和我们的`LOOKUP_TYPE`字典，因为根据项目的性质，我们可能有不同的列集和需求。

在我的情况下，这个工具解决了我当时面临的问题，所以我让它保持现在的样子，并分享它，目的是在未来回到它身边，并激励任何其他用户可能会发现它对适应他们的需求很有用，所以你去吧，我希望这是有用的。

# 结果

您可以在这里找到这个用例[的结果。](https://github.com/AlbertoV5/psql-to-models/tree/main/example)

以下是预览:

## SQL 文件

```
CREATE TABLE DATETIMEEVENTS
(
  ROW_ID INT NOT NULL,
 SUBJECT_ID INT NOT NULL,
 HADM_ID INT,
 ICUSTAY_ID INT,
 ITEMID INT NOT NULL,
 CHARTTIME TIMESTAMP(0) NOT NULL,
 STORETIME TIMESTAMP(0) NOT NULL,
 CGID INT NOT NULL,
 VALUE TIMESTAMP(0),
 VALUEUOM VARCHAR(50) NOT NULL,
 WARNING SMALLINT,
 ERROR SMALLINT,
 RESULTSTATUS VARCHAR(50),
 STOPPED VARCHAR(50),
 CONSTRAINT datetime_rowid_pk PRIMARY KEY (ROW_ID)
) ;

DROP TABLE IF EXISTS DIAGNOSES_ICD CASCADE;
CREATE TABLE DIAGNOSES_ICD
(
  ROW_ID INT NOT NULL,
 SUBJECT_ID INT NOT NULL,
 HADM_ID INT NOT NULL,
 SEQ_NUM INT,
 ICD9_CODE VARCHAR(10),
 CONSTRAINT diagnosesicd_rowid_pk PRIMARY KEY (ROW_ID)
) ;
```

## SQLAlchemy 模型

```
class Datetimeevents(Base):

    __tablename__ = "datetimeevents"

    row_id = Column(Integer, nullable=False, primary_key=True)
    subject_id = Column(Integer, nullable=False)
    hadm_id = Column(Integer)
    icustay_id = Column(Integer)
    itemid = Column(Integer, nullable=False)
    charttime = Column(TIMESTAMP(0), nullable=False)
    storetime = Column(TIMESTAMP(0), nullable=False)
    cgid = Column(Integer, nullable=False)
    value = Column(TIMESTAMP(0))
    valueuom = Column(String(50), nullable=False)
    warning = Column(SmallInteger)
    error = Column(SmallInteger)
    resultstatus = Column(String(50))
    stopped = Column(String(50))

class Diagnoses_icd(Base):

    __tablename__ = "diagnoses_icd"

    row_id = Column(Integer, nullable=False, primary_key=True)
    subject_id = Column(Integer, nullable=False)
    hadm_id = Column(Integer, nullable=False)
    seq_num = Column(Integer)
    icd9_code = Column(String(10))
```

## Pydantic 模型

```
class Datetimeevents(BaseModel):

    row_id: int
    subject_id: int
    hadm_id: int | None
    icustay_id: int | None
    itemid: int
    charttime: datetime
    storetime: datetime
    cgid: int
    value: datetime | None
    valueuom: str
    warning: int | None
    error: int | None
    resultstatus: str | None
    stopped: str | None

    class Config:
        orm_mode = True

class Diagnoses_icd(BaseModel):

    row_id: int
    subject_id: int
    hadm_id: int
    seq_num: int | None
    icd9_code: str | None

    class Config:
        orm_mode = True
```

# 脚注

[https://stack overflow . com/questions/766372/python-non-greedy-regexes](https://stackoverflow.com/questions/766372/python-non-greedy-regexes)

[https://docs . sqlalchemy . org/en/14/ORM/mapping _ styles . html # ORM-declarative-mapping](https://docs.sqlalchemy.org/en/14/orm/mapping_styles.html#orm-declarative-mapping)

[https://pydantic-docs.helpmanual.io/usage/models/](https://pydantic-docs.helpmanual.io/usage/models/)

[https://docs.python.org/3/library/dataclasses.html](https://docs.python.org/3/library/dataclasses.html)

【https://docs.python.org/3/library/typing.html#typing. 可选

【https://github.com】最初发表于[](https://github.com/AlbertoV5/python-blog/blob/main/static/sql-converter.md)**。**