# 在 Go 中使用缓存的最简单方法

> 原文：<https://betterprogramming.pub/easiest-way-to-use-caching-in-golang-d3c639f9483a>

## 使用 Kubernetes 的 client-go 软件包

![](img/afcc67663e82bd9a47aa857c64d5a6e1.png)

照片由 [Arvind meena mina](https://unsplash.com/@perfectshot4u?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

缓存是任何程序中用来快速访问常用数据的重要概念之一。

在本文中，让我们了解如何轻松地使用 Kubernetes [client-go](https://github.com/kubernetes/client-go) 包中可用的缓存方法。

首先，让我们考虑一个简单的场景，我们希望在缓存中存储键和值。完整的程序可以在我的 [Github](https://github.com/Karthik-K-N/goCache) 库中找到。

```
// cacheTTL is the duration of time to hold the key in cache
const *cacheTTL* = 20 * time.*Second* // keyValue contains the fields to store the key and value
type keyValue struct {
   key   string
   value string
}

func main() {
   cacheStore := cache.NewTTLStore(cacheKeyFunc, *cacheTTL*)
}// cacheKeyFunc defines the key function required in TTLStore.
func cacheKeyFunc(obj interface{}) (string, error) {
   return obj.(keyValue).key, nil
}
```

在上面的代码片段中，在 main 函数中，我们使用以下代码初始化缓存存储:

`**cacheKeyFunc**`:告诉使用什么作为键来存储用户提供的对象到缓存中。

`**cacheTTL**`:缓存中保存密钥的持续时间。

完成基本初始化后，我们现在可以借助以下函数将值存储在缓存中:

```
func addToCache(cacheStore cache.Store, object keyValue) error {
   err := cacheStore.Add(object)
   if err != nil {
      klog.Errorf("failed to add key value to cache error", err)
      return err
   }
   return nil
}

func fetchFromCache(cacheStore cache.Store, key string) (string, error) {
   obj, exists, err := cacheStore.GetByKey(key)
   if err != nil {
      klog.Errorf("failed to add key value to cache error", err)
      return "", err
   }
   if !exists {
      klog.Errorf("object does not exist in the cache")
      return "", nil
   }
   return obj.(keyValue).value, nil
}

func deleteFromCache(cacheStore cache.Store, object keyValue) error {
   return cacheStore.Delete(object)
}
```

在上面的代码片段中，我们使用了 cachestore 方法

添加将值存储在缓存**中，添加** `**GetByKey**` 从提供的键中获取值，添加“删除”将值从缓存中删除。

在获取时，我们需要确保检查所需键的值是否存在于缓存中，如果它过期或从未被添加，它可能不存在于缓存中。

此外，为了添加、`GetByKey`和`Delete`方法，缓存存储还有许多其他方法，如 Get、Update、List 等——所有方法都可以在这里看到[。](https://github.com/kubernetes/client-go/blob/master/tools/cache/store.go#L39-L71)

最后，主函数看起来像这样

```
func main() {
   cacheStore := cache.NewTTLStore(cacheKeyFunc, *cacheTTL*)

   testKey := "myKey"
   key := keyValue{
      key:   testKey,
      value: "myValue",
   }

   klog.Infof("adding the key %v to cache", key)

   err := addToCache(cacheStore, key)
   if err != nil {
      klog.Fatalf("failed to add the key %v to cache error %v", key, err)
   }

   klog.Infof("fetching the value for key: %s from cache", testKey)

   value, err := fetchFromCache(cacheStore, "myKey")
   if err != nil {
      klog.Fatalf("failed to fetch value for key %S from cache error %v", testKey, err)
   }
   if value == "" {
      klog.Fatalf("the value for key %s is empty", testKey)
   }

   klog.Infof("successfully fetched the value for key %s from cache value: %s", testKey, value)

   klog.Infof("deleting the key %s from cache", testKey)
   err = deleteFromCache(cacheStore, key)
   if err != nil {
      klog.Fatalf("failed to delete key %s from cache error %v", testKey, err)
   }
}
```

在我们为上面的代码添加单元测试之前，我们的工作还没有完全完成。让我们为`addToCache`和`fetchFromCache`函数添加单元测试。

```
func TestAddToCache(t *testing.T) {

   cacheStore := cache.NewTTLStore(cacheKeyFunc, *cacheTTL*)
   testKey := keyValue{
      key:   "testKey",
      value: "testValue",
   }
   err := addToCache(cacheStore, testKey)
   if err != nil {
      t.Fatalf("expecting error to be nil but got err %v", err)
   }
}

func TestFetchFromCache(t *testing.T) {

   defaultCacheStoreFunc := func() cache.Store {
      return cache.NewTTLStore(cacheKeyFunc, *cacheTTL*)
   }
   testKey := keyValue{
      key:   "testKey",
      value: "testValue",
   }

   testCases := []struct {
      name          string
      expectedError error
      expectedValue string
      keyName       string
      sleep         bool
      cacheStore    func() cache.Store
   }{
      {
         name:          "exists in cache",
         expectedValue: "testValue",
         keyName:       "testKey",
         cacheStore:    defaultCacheStoreFunc,
      },
      {
         name:          "not exists in cache",
         expectedValue: "",
         keyName:       "notTestKey",
         cacheStore:    defaultCacheStoreFunc,
      },
      {
         name:          "key in cache expired",
         expectedValue: "",
         keyName:       "testKey",
         cacheStore: func() cache.Store {
            return cache.NewTTLStore(cacheKeyFunc, time.*Millisecond*)
         },
      },
   }
   for _, tc := range testCases {
      t.Run(tc.name, func(t *testing.T) {
         gs := NewWithT(t)
         cacheStore := tc.cacheStore()
         err := addToCache(cacheStore, testKey)
         if err != nil {
            t.Fatalf("failed to add key to cache error %v", err)
         }
         if tc.sleep {
            time.Sleep(time.*Second*)
         }
         value, err := fetchFromCache(cacheStore, keyValue{key: tc.keyName})
         if err != nil {
            if tc.expectedError != nil {
               gs.Expect(err).To(HaveOccurred())
               gs.Expect(err.Error()).To(Equal(tc.expectedError.Error()))
            } else {
               gs.Expect(err).ToNot(HaveOccurred())
            }
         }
         gs.Expect(value).To(Equal(tc.expectedValue))
      })
   }
}
```

完整的程序和测试可以在我的 [GitHub](https://github.com/Karthik-K-N/goCache) 库中找到。

# 参考

1.  [用 go 语言进行单元测试](/unit-testing-in-go-language-using-mocks-3b873ce1348d)
2.  [编写定制的 Kubernetes 控制器和 webhooks](/writing-custom-kubernetes-controller-and-webhooks-141230820e9)