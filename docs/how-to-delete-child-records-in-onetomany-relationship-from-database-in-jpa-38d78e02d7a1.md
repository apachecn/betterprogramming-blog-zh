# 如何在 JPA 中从数据库中删除 OneToMany 关系中的子记录？

> 原文：<https://betterprogramming.pub/how-to-delete-child-records-in-onetomany-relationship-from-database-in-jpa-38d78e02d7a1>

## 如果我删除了子实体与父实体的关联，Hibernate 会自动删除子实体吗？让我们找出答案

![](img/bbc2528b063509eb463aa6fd501d37e4.png)

[挡块](https://unsplash.com/@blocks?utm_source=medium&utm_medium=referral)在[防溅板](https://unsplash.com?utm_source=medium&utm_medium=referral)上的照片

当我们对数据库建模时，我们最有可能定义几个多对一或一对多的关联。当然，当我们[对实体](https://thorben-janssen.com/ultimate-guide-association-mappings-jpa-hibernate/)建模时也是如此。

用 JPA 和 Hibernate 很容易做到这一点。您只需要一个表示关联的属性，并用`[@ManyToOne](https://docs.oracle.com/javaee/7/api/javax/persistence/ManyToOne.html)`或`[@OneToMany](https://docs.oracle.com/javaee/7/api/javax/persistence/OneToMany.html)`关联对其进行注释。

但不幸的是，尽管看起来很容易，却有几个陷阱。

让我们对`Vendor`和`Client`实体进行建模，并在它们之间创建双向的一对多关系。

`Vendor.class`的代码如下所示:

```
@Entity 
public class Vendor{ 

     @Id     
     @GeneratedValue
     private Long id;
     private String name;

     @OneToMany(mappedBy = "vendor", cascade = CascadeType.ALL)
     private List<Client> clients; // standard constructors
      // standard getters and setters
}
```

这里是`Client.class`:

```
@Entity
public class Client {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    private Vendor vendor;
}
```

我们还有两个存储库，`ClientRepository`和`VendorRepository`:

```
@Repository
public interface ClientRepository extends JpaRepository<Child, Long>{}@Repository
public interface VendorRepository extends JpaRepository<Child, Long>{}
```

现在让我们编写一个测试，如下所示:

```
public class VendorTest extends IntegrationTestSpec {

    @Autowired
    private VendorRepository vendorRepository;

    @Autowired
    private ClientRepository clientRepository;

    @Test
    public void test() {
        Vendor vendor = new Vendor();
        Client client = new Client();

        vendor.setClient(Set.of(client));
        vendorRepository.save(vendor);

        Client fetchClients = clientRepository.findAll().get(0);
        clientRepository.delete(fetchClients);

        assertEquals(1, vendorRepository.count());
        assertEquals(0, clientRepository.count()); // FAILS!!! 
         //clientRepository.count() returns 1  
    }
}
```

所以我们来了，在这里我们创建了`vendor`和`client`，将其保存到 DB，然后从 DB 获取`client`，移除它，最后，确保一切按预期工作。

但事实并非如此。

这是因为 JPA 实际上不知道是否应该删除从集合中移除的内容。但是等等。那么我们如何从数据库中删除子实体呢？

当您将`@OneToMany`注释的`orphanRemoval`属性设置为 true，并将级联属性设置为`CascadeType.ALL` *，* it 删除父实体时，Hibernate 会自动删除子实体。

让我们将它添加到我们的`Vendor`模型中:

```
@Entity 
public class Vendor{ 

     @Id     
     @GeneratedValue
     private Long id;
     private String name;

     @OneToMany(mappedBy = "vendor", cascade = CascadeType.ALL,          
     orphanRemoval = true) 
     private List<Client> clients; //Getters and setters omitted for brevity 

     public Vendor addClient(Client client)
     {
          clients.add(client);
          clients.setVendor(this);
     }
     public Vendor removeClient(Client client)
     {
          clients.remove(client);
          clients.setVendor(null);
          return this;
     }
}
```

`CascadeType.ALL`，表示所有 JPA 和 Hibernate [实体状态转换](https://vladmihalcea.com/a-beginners-guide-to-jpa-hibernate-entity-state-transitions/)(例如`persist`、`merge`、`remove`)都从父`Vendor`实体传递到`Client`子实体。

当一个`Client`实体不再被其父`Vendor`实体引用时，`orphanRemoval`属性将指示 JPA 提供者触发一个`remove`实体状态转换。

因为我们有一个双向的一对多关联，我们需要确保关联的两端是同步的，因此，我们创建了`addClient`和`removeClient`方法，以便在添加或删除新的客户端实体时同步两端。

# 结论

那都是我这边的。我希望你能从这篇文章中学到一些东西。我尽力让事情尽可能简单。感谢阅读。