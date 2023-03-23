# Django 4.1:在哪里应用异步

> 原文：<https://betterprogramming.pub/django-4-1-where-to-apply-async-c05f29ca2041>

## 异步和同步进程的执行时间比较

![](img/038acd7b3411bd5014c6e87ff605e7d1.png)

瑞兰德·迪恩在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在 Django 框架的未来发展中，一些激动人心的变化即将到来。一个显著的变化是更新了对异步数据访问操作和 HTTP 方法处理程序的支持。本文将回顾我创建的一些测试，并比较异步和同步流程的执行时间。

# Django 中同步和异步的区别

同步编程遵循一组严格的序列(或者，换句话说，它按顺序执行)，其中操作按照严格的顺序一次执行一个。下面这段来自 mendix.com 大学的 David Bevans 的话很好地描述了同步编程:

> 为了说明同步编程是如何工作的，想象一下电话。打电话时，一个人在说话，另一个人在听。当第一个人结束时，第二个人往往会立即回应。

异步编程允许多个相关操作同时运行，而无需等待前面的任务完成。我们将其描述为实现非阻塞架构的多线程模型。如果一个线程是空闲的并且可以执行一个操作，那么它会在暂停正在等待的操作的同时执行该操作。David Bevans 对异步编程的另一个精彩描述是:

> 发短信是一种异步的交流方式。一个人可以发送一条短信，收件人可以在空闲时回复。与此同时，发送方在等待响应时可能会做其他事情。

也就是说，我们可以看出，如果正确利用，异步应该会带来显著的性能提升。

Django 的核心是同步工作和执行，大约在 3.0 版本左右(当“twisted”、“channels”和“asyncio”等扩展开始流行时)，Django 可以异步工作的想法开始显现。

根据我的研究，决定在哪里使用 sync 或 async 的最好方法是辨别视图(以及，通过扩展，我们希望通过该视图实现的目标)是 I/O 受限还是 CPU 受限。当处理 I/O 绑定问题时，应该使用异步。

## 环境和先决条件

测试之前，让我们回顾一下设置和一些重要注意事项。首先，一个显而易见的注意事项(但很重要):结果可能因 IDE 而异，每个人在他们的项目、后台进程和第三方软件中相关/使用的包的设置也不同。因此，如果您决定重新创建这些测试，您在这里看到的执行时间可能与您实现的时间相似，也可能不相似。

对于这些测试，我们使用 Python 3.9 作为解释器，PostgreSQL 13.6 作为数据库系统。已安装的 Python 包如下:

```
Django~=4.1
psycopg2~=2.9.3
hypercorn~=0.14.1
```

psycopg2 充当我们的数据库适配器，hypercorn 将为我们的环境模拟 ASGI 测试服务器。

你可能会想，“是的，但是当我们的项目中已经有了 asgi.py，为什么还要使用 hypercorn 呢？”的确，我们项目中的 asgi.py 包含了一个可调用的应用程序，可以被开发或生产中的任何 asgi 服务器使用。但是开发服务器不使用它(我们用`runserver`命令调用它)，这就是 hypercorn 的用武之地。通过调用`myproject.asgi:application`，服务器启动并准备在本地使用。

**重要提示**:测量性能和执行时间的正确方法是通过`TestCase`(或者功能更丰富的`TransactionTestCase`)和测试模块/单元。但是我对测试中使用的`AsyncRequestFactory`和`AsyncClient()`有些担心。通读文档，似乎部分模拟了“异步”行为，其中异步函数被执行，然后过程回复到同步行为。

最真实/现实的测试方法是做一个简单的视图，并通过 hypercorn 的模拟 ASGI 服务器的 URL 调用它。参考文献[3]和[4]包含了更多关于“测试异步代码”和“高级测试主题”的信息，但是我可能遗漏了一些重要的上下文。

## **型号**

对于我们的测试，我们将尽可能保持我们的模型轻。我们使用的字段各不相同，但是模型中每个对象的数据大小即使不相同，也是相似的。

```
from django.db.models import Model, CharField, DateField, ForeignKey, CASCADE, ManyToManyField, TextField
from django.utils.timezone import now

class CarParts(Model):
    engine = TextField()

class Manufacturer(Model):
    name = CharField(max_length=150)

class Store(Model):
    location = CharField(max_length=150)

class Car(Model):
    created_at = DateField(default=now)
    store = ManyToManyField(Store)
    car_parts = ForeignKey(CarParts, on_delete=CASCADE, null=True)
    manufacturer = ForeignKey(Manufacturer, on_delete=CASCADE, null=True)
```

我们将对每个模型的 50、500 和 1，500 个对象进行查询和迭代，这样我们就可以跟踪它如何随着更多的数据而扩展。

## 测试

我们有两个从`django.views`扩展基类`View`的类视图，每个视图包含多个方法。Django 不允许混合使用同步和异步方法。记住，sync 实现了阻塞方法，而 async 实现了非阻塞方法。因此一个类视图将包含所有异步方法和其他同步方法。

测试将按以下顺序进行:

1.  加载 x 个对象
2.  执行三次连续测试(多次测试以补偿可能导致性能下降的潜在 IDE 后台进程)
3.  分析

## **测试 A:通过 get 和 post 方法使用 ORM 的 CRUD 循环**

要在视图中调用的全局函数(一个异步，另一个同步):

```
async def async_iteration():
    car_obj = Car.objects.all()
    async for i in car_obj:
        a_query = await i.store.afirst()
    return car_obj

def sync_iteration():
    car_obj = Car.objects.all()
    for i in car_obj:
        a_query = i.store.first()
    return car_obj
```

我们的同步类:

```
class PerformanceTestSync(View):
    def get(self, request):
        e_list = []
        start = time.time()
        for entry in Car.objects.all():
            try:
                a_query = entry.store.first()
                b_query = CarParts.objects.filter(
                    Q(engine__contains='engine') & 
                    Q(car__manufacturer_id__gt=20)).first()
            except (Exception, KeyError) as e:
                print('Error during sync iteration: ', e)
        co = sync_iteration()
        for v in co:
            e_list.append(v)
        end = time.time()
        print('Sync GET time: ', end - start)

    def post(self, request):
        start = time.time()
        engine = request.POST.get('engine')
        name = request.POST.get('name')
        location = request.POST.get('location')
        try:
            car_parts_obj = CarParts.objects.create(engine=engine)
        except (CarParts.DoesNotExist, ObjectDoesNotExist, 
                Exception) as e:
            print('CarParts failed to create: ', e)
            car_parts_obj = None
            pass
        try:
            manufacturer_obj = Manufacturer.objects.create(
                                                 name=name)
        except (Manufacturer.DoesNotExist, ObjectDoesNotExist,
                             Exception) as e:
            print('Manufacturer failed to create: ', e)
            manufacturer_obj = None
            pass
        try:
            store_obj = Store.objects.create(location=location)
        except (Store.DoesNotExist, ObjectDoesNotExist, 
                Exception) as e:
            print('Store failed to create: ', e)
            store_obj = None
            pass

        if car_parts_obj and manufacturer_obj:
            try:
                car_obj = Car.objects.create(
                            car_parts=car_parts_obj, 
                            manufacturer=manufacturer_obj
                          )
                car_obj.store.add(store_obj)

            except (Car.DoesNotExist, ObjectDoesNotExist, 
                    Exception) as e:
                print('Car failed to create: ', e)
                pass

        end = time.time()
        print('Sync POST time: ', end - start)
        return HttpResponse("ok", status=200)
```

我们的异步类:

```
class PerformanceTestAsync(View):
    async def get(self, request):
        e_list = []
        start = time.time()
        async for entry in Car.objects.all():
            try:
                a_query = await entry.store.afirst()
                b_query = await CarParts.objects.filter(
                        Q(engine__contains='engine') & 
                        Q(car__manufacturer_id__gt=20)).afirst()
            except (Exception, KeyError) as e:
                print('Error during async iteration: ', e)
        co = await async_iteration()
        async for v in co:
            e_list.append(v)
        end = time.time()
        print('Async GET time: ', end - start)

        return render(request, 'demo_template.html')

    async def post(self, request):
        start = time.time()
        engine = request.POST.get('engine')
        name = request.POST.get('name')
        location = request.POST.get('location')
        try:
            car_parts_obj = await CarParts.objects.acreate(
                                 engine=engine
                                )
        except (CarParts.DoesNotExist, ObjectDoesNotExist, 
                Exception) as e:
            print('CarParts failed to create: ', e)
            car_parts_obj = None
            pass
        try:
            manufacturer_obj = await Manufacturer.objects.acreate(
                                        name=name
                                      )
        except (Manufacturer.DoesNotExist, ObjectDoesNotExist, 
                Exception) as e:
            print('Manufacturer failed to create: ', e)
            manufacturer_obj = None
            pass

        if car_parts_obj and manufacturer_obj:
            try:
                car_obj = await Car.objects.acreate(
                                  car_parts=car_parts_obj, 
                                  manufacturer=manufacturer_obj
                                )
                if car_obj:
                    try:
                        await car_obj.store.acreate(
                               location=location
                              )
                    except Exception as e:
                        print('Couldnt add store object 
                               relations for m2m: ', e)
            except (Car.DoesNotExist, ObjectDoesNotExist, 
                    Exception) as e:
                print('Car failed to create: ', e)
                pass

        end = time.time()
        print('Async POST time: ', end - start)
        return HttpResponse("ok", status=200)
```

在`get()`方法中，我们遍历`Car`模型中的对象，在循环中，我们用 Q 表达式进行一个简单的查询和一个有点复杂的查询。所有这些都包装在 4.1 中引入的新 ORM 接口中。最后，我们调用一个全局函数，该函数将迭代并返回`queryset`，其条目将被追加到一个空列表中。

同时，在`post()`方法中，我们为我们的模型创建一些对象，并更新现有的数据。最后，我们会删除它们。通过请求从客户端接收 post 中的数据。删除本身可以是一种方法，更新也可以，但是为了简单起见，`get`和`post`将完成这个任务。

你会注意到在异步类中，我没有在 m2m 字段更新中使用`.add`。这是因为(遗憾的是)我在文档中找不到它的异步改编版本，Django 将其视为同步操作。因此，我做了一点变通，直接创建了`Store`对象。

# **测试结果**

50 个对象的方法`get()`(显示三次测试的平均值):

*   异步:~0.39832 秒
*   同步:~0.29502 秒

为所有模型创建单个对象的方法`post()`:

*   异步:~0.07699 秒
*   同步:~0.08299 秒

用于 500 个对象的方法`get()`:

*   异步:~3.67973 秒
*   同步:~2.39427 秒

1500 个对象的方法`get()`:

*   异步:~8.79098 秒
*   同步:~6.26344 秒

这个测试的结论是这个测试多边形是 CPU 受限的，我们的异步没有被充分利用

# **测试 B: API 通信**

对于这个测试，您可能需要安装 Python 的“httpx”包。我第一次尝试 Python 的“requests”包，认为它有异步功能，但遗憾的是，它没有，所以“httpx”作为一个很好的替代物出现了。此外，还需要 Python 的内置“asyncio”包来从等待的函数调用中收集数据。最后，我们正在从“asyncio”导入“shield ”,以防止潜在的取消。

```
pip install httpx
# Then in your view
import httpx
import asyncio
from asyncio import shield
```

使用 GET 请求，我们将联系 apilayer.com 汇率的外部 API。

**注意**:我使用的是免费订阅，作为测试目的的试用/演示，非常适合这次测试。具体来说，我在 https://apilayer.com/marketplace/exchangerates_data-api 上使用 API。此外，它非常易于使用，甚至为多种编程语言调用其 API 提供了模板代码。

在提供的 API 集中，我们只使用一个，那就是:

```
GET/timeseries
```

目标 API 允许我们从过去(最大)提取汇率。365 天的周期)，这将导致冗长的响应，需要一些时间来汇集/发送。apilayer.com 速度快，反应快，所以这次测试用了一整年的汇率。

和以前一样，我们创建了一个同步和异步版本来执行 API 调用。如前所述，将进行三次连续测试，并显示平均值。

以下是基于 API 函数的视图:

```
def api_exchange_sync():
    url = "https://api.apilayer.com/exchangerates_data/timeseries"

    payload = {}
    headers = {
        "apikey": "[Insert your API key]"
    }

    r = httpx.get(url, params={"start_date": "2016-01-02", 
                               "end_date": "2017-01-01"},
                               headers=headers)

    return r.json()

async def api_exchange_async():
    url = "https://api.apilayer.com/exchangerates_data/timeseries"

    payload = {}
    headers = {
        "apikey": "[Insert your API key]"
    }

    async with httpx.AsyncClient(timeout=50.0) as client:
        r = await client.get(url, params={"start_date": "2016-01-
                             02", "end_date": "2017-01-01"},
                             headers=headers)

    return r.json()
```

我们在 AsyncClient 上设置超时来防止`httpx.ReadTimeout`错误。如果您按下重启 API 调用的按钮:p。

以下是基于同步和异步函数的视图:

```
def append_data_sync(request):
    start = time.time()
    e_list = []
    data = [api_exchange_sync()]
    for k in data:
        e_list.append(k)
    end = time.time()
    print('read_sync time: ', end - start)
    return HttpResponse()

async def append_data_async(request):
    start = time.time()
    e_list = []
    data = await asyncio.gather(*[api_exchange_async()])
    for d in data:
        e_list.append(d)
    end = time.time()
    print('read_async time: ', end - start)
    return HttpResponse()
```

## **测试结果**

*   异步:~2.23437 秒
*   同步:~3.30023 秒

当我们遍历从 API 函数接收的数据列表时，我们将它们添加到一个列表中。来自`asyncio.gather()`的未来物体被证明是有利的，我相信如果我们使用数据流，我们会更快。Async 在这次测试中赢得了比赛。

# 结论

当我们等待 API 响应时，Async 派上了用场。我们设法更快地接收数据，并且更好地利用时间。当需要内部数据处理和通过 ORM 与 DB 通信时，Sync 表现出色。

没有必要等待，屏蔽，多线程，因此额外的速度同步得到。没有必要比较同步和异步哪个更快——因为这在很大程度上取决于我们想要做的事情的上下文。重要的是要认识到 CPU 受限和 I/O 受限的情况，以便正确地利用异步和同步。

我们不需要对遇到的每个小延迟都使用异步。我认为，在决定实现异步方法之前，必须注意到一种延迟模式(在我们的开发过程中)。

Django 4.1 异步更新不是一个速度更新——我更愿意把它看作是一个允许我们覆盖更好和更多情况的更新。

# 参考

1.  mendix.com，[https://www . mendix . com/blog/asynchronous-vs-synchronous-programming/](https://www.mendix.com/blog/asynchronous-vs-synchronous-programming/)，《异步和同步编程的区别》，大卫·贝文斯
2.  djangoproject.com，[https://docs.djangoproject.com/](https://docs.djangoproject.com/)，‘异步相关页面，相关 4.1 变更和站点’
3.  djangoproject.com，[https://docs.djangoproject.com/en/4.0/topics/testing/tools/](https://docs.djangoproject.com/en/4.0/topics/testing/tools/)
4.  djangoproject.com，[https://docs . django project . com/en/4.1/topics/testing/advanced/](https://docs.djangoproject.com/en/4.1/topics/testing/advanced/)