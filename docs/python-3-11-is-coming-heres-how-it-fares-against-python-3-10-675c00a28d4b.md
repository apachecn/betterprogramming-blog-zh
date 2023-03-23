# Python 3.11 来了！下面是它与 Python 3.10 相比的情况

> 原文：<https://betterprogramming.pub/python-3-11-is-coming-heres-how-it-fares-against-python-3-10-675c00a28d4b>

## 了解 Python 3.11 的新特性

![](img/6088c2bbca9e277cf15e6a6bd1495b8c.png)

弗朗西斯科·加拉罗蒂在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

根据 Python 软件基金会(PSF)的消息，Python 3.11 已经是第 7 个 alpha 版本，计划于 2022 年 10 月[发布](https://peps.python.org/pep-0664/#schedule)。

即将到来的版本包括哪些变化？

# 序言

为了评估 3.10 和 3.11 版本之间的差异，我设置了两个 docker 容器。

3.10 版的第一个容器:

```
docker run -t -d python:3.10.4-bullseye
```

3.11 版的第二个容器:

```
docker run -t -d python:3.11-rc-bullseye
```

让两个容器运行，我可以使用 [vs 代码远程容器](https://code.visualstudio.com/docs/remote/containers)来连接到运行的容器。

然后，我可以在这两种环境中执行我的 Python 代码，并看到不同之处。

在接下来的部分中，我将首先展示一个代码示例，然后展示两个版本之间的区别。

# 1:错误位置

3.10 版中的输出:

```
1
100
Traceback (most recent call last):
  File "/usr/local/lib/python3.10/runpy.py", line 196, in _run_module_as_main
    return _run_code(code, main_globals, None,
  File "/usr/local/lib/python3.10/runpy.py", line 86, in _run_code
    exec(code, run_globals)
  File "/root/py310/myapp/__main__.py", line 4, in <module>
    print(d["key_11"])
KeyError: 'key_11'
```

3.11 版中的输出

```
1
100
Traceback (most recent call last):
  File "<frozen runpy>", line 198, in _run_module_as_main
  File "<frozen runpy>", line 88, in _run_code
  File "/root/py311/myapp/__main__.py", line 4, in <module>
    print(d["key_11"])
          ~^^^^^^^^^^
KeyError: 'key_11'
```

Python 3.11 向开发人员公开了一个更好的错误位置，为开发人员提供了一个极好的体验。

# 2:‘`self’` 型

在`typing extensions` [模块](https://pypi.org/project/typing-extensions/)中已经引入了`self`类型，现在它被提升为标准类型库。

上面的代码代表了一个目录的结构。目录有子目录，所以定义是递归的。

Python 3.10 中的输出:

```
Traceback (most recent call last):
  File "/usr/local/lib/python3.10/runpy.py", line 196, in _run_module_as_main
    return _run_code(code, main_globals, None,
  File "/usr/local/lib/python3.10/runpy.py", line 86, in _run_code
    exec(code, run_globals)
  File "/root/py310/myapp/__main__.py", line 2, in <module>
    from typing import List, Tuple, Self
```

Python 3.11 中的输出:

```
{'content': (['a.txt', 'b.txt'],
             [{'content': (['file1', 'file2'], None), 'name': 'dir1'}]),
 'name': 'dir2'}
```

注意:您可以通过类本身的名称来注释`subdir`的类型。但是如果父类名改变了，你必须相应地改变所有的注释引用。

为了使此代码在 3.11 和 3.10 中工作，您可以按如下方式执行导入:

```
try:
    from typing import Self
except ImportError:
    from typing_extensions import Self
```

# 3:异常注释

`BaseException`类现在有了一个默认为`None`的`__note__`类属性。
您可以用自己选择的任何字符串覆盖它，并提供进一步的信息。

Python 3.10 中的输出:

```
Traceback (most recent call last):
  File "/usr/local/lib/python3.10/runpy.py", line 196, in _run_module_as_main
    return _run_code(code, main_globals, None,
  File "/usr/local/lib/python3.10/runpy.py", line 86, in _run_code
    exec(code, run_globals)
  File "/root/py310/myapp/__main__.py", line 6, in <module>
    raise MyException("some exception")
__main__.MyException: some exception
```

Python 3.11 中的输出:

```
Traceback (most recent call last):
  File "<frozen runpy>", line 198, in _run_module_as_main
  File "<frozen runpy>", line 88, in _run_code
  File "/root/py311/myapp/__main__.py", line 6, in <module>
    raise MyException("some exception")
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
MyException: some exception
this is my note :)
```

我们看到注释已经被添加到输出中，它允许开发人员在他们的异常中进行更多的交流。

# 4:例外组

3.11 版本引入了一个新的异常类型名`ExceptionGroup`，以抛出一堆异常并在一个`except`子句中处理它们。

除此之外，还引入了一种新的`except*`语法。

Python 3.10 中的输出:

```
File "/usr/local/lib/python3.10/runpy.py", line 189, in _run_module_as_main
    mod_name, mod_spec, code = _get_main_module_details(_Error)
  File "/usr/local/lib/python3.10/runpy.py", line 223, in _get_main_module_details
    return _get_module_details(main_name)
  File "/usr/local/lib/python3.10/runpy.py", line 157, in _get_module_details
    code = loader.get_code(mod_name)
  File "<frozen importlib._bootstrap_external>", line 1017, in get_code
  File "<frozen importlib._bootstrap_external>", line 947, in source_to_code
  File "<frozen importlib._bootstrap>", line 241, in _call_with_frames_removed
  File "/root/py310/myapp/__main__.py", line 18
    except * (ToYoungException, EmailIsInvalidException) as exception_group_1:
```

Python 3.11 中的输出:

```
validations failed
  + Exception Group Traceback (most recent call last):
  |   File "<frozen runpy>", line 198, in _run_module_as_main
  |   File "<frozen runpy>", line 88, in _run_code
  | ExceptionGroup:  (1 sub-exception)
  +-+---------------- 1 ----------------
    | Exception Group Traceback (most recent call last):
    |   File "/root/py311/myapp/__main__.py", line 14, in <module>
    |     raise ExceptionGroup(
    |     ^^^^^^^^^^^^^^^^^^^^^
    | ExceptionGroup: Data validations (2 sub-exceptions)
    +-+---------------- 1 ----------------
      | ToYoungException: Age must be over 18 - age is 11
      +---------------- 2 ----------------
      | EmailIsInvalidException: Email must be valid some_wannabe_email
      +------------------------------------
    | 
    | The above exception was the direct cause of the following exception:
    | 
    | Traceback (most recent call last):
    |   File "/root/py311/myapp/__main__.py", line 20, in <module>
    |     raise ValueError from exception_group_1
    |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    | ValueError
    +------------------------------------
```

正如我们所看到的，当我们有多个失败原因想要一次性披露时，这是非常有价值的。

# 5.嵌套异步理解

Python 3.10 中的输出:

```
Traceback (most recent call last):
  File "/usr/local/lib/python3.10/runpy.py", line 189, in _run_module_as_main
    mod_name, mod_spec, code = _get_main_module_details(_Error)
  File "/usr/local/lib/python3.10/runpy.py", line 223, in _get_main_module_details
    return _get_module_details(main_name)
  File "/usr/local/lib/python3.10/runpy.py", line 157, in _get_module_details
    code = loader.get_code(mod_name)
  File "<frozen importlib._bootstrap_external>", line 1017, in get_code
  File "<frozen importlib._bootstrap_external>", line 947, in source_to_code
  File "<frozen importlib._bootstrap>", line 241, in _call_with_frames_removed
  File "/root/py310/myapp/__main__.py", line 11
    return { n: [x async for x in elements(n)] for n in range(3)}
                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
SyntaxError: asynchronous comprehension outside of an asynchronous function
```

Python 3.11 中的输出:

```
{0: [1], 1: [1, 1], 2: [1, 2, 4], 3: [1, 3, 9, 27], 4: [1, 4, 16, 64, 256]}
```

代码进入理解模块的那一刻，它就意识到了对当前“函数上下文”的理解。
如果理解不是异步的，那么内部代码块不能有异步语句。

但是在 Python 3.11 中，如果理解包含允许内部`async`理解的内部`async` 语句，那么理解就会变成隐式异步的。

# 6: TOML 解析器

TOML 解析现在是标准库的一部分，就像 JSON 和 CSV 一样。

Python 3.10 中的输出:

```
Traceback (most recent call last):
  File "/usr/local/lib/python3.10/runpy.py", line 196, in _run_module_as_main
    return _run_code(code, main_globals, None,
  File "/usr/local/lib/python3.10/runpy.py", line 86, in _run_code
    exec(code, run_globals)
  File "/root/py310/myapp/__main__.py", line 2, in <module>
    import tomllib
ModuleNotFoundError: No module named 'tomllib'
```

Python 3.11 中的输出:

```
{'clients': {'data': [['gamma', 'delta'], [1, 2]], 'hosts': ['alpha', 'omega']},
 'database': {'connection_max': 5000,
              'enabled': True,
              'ports': [8000, 8001, 8002],
              'server': '192.168.1.1'},
 'owner': {'dob': datetime.datetime(1979, 5, 27, 7, 32, tzinfo=datetime.timezone(datetime.timedelta(days=-1, seconds=57600))),
           'name': 'Tom Preston-Werner'},
 'servers': {'alpha': {'dc': 'eqdc10', 'ip': '10.0.0.1'},
             'beta': {'dc': 'eqdc10', 'ip': '10.0.0.2'}},
 'title': 'TOML Example'}
```

# 7.性能优化

有人认为 Python 3.11 的运行速度比 python 3.10 快 10-60%。您可以在基准测试部分[阅读更多信息，点击](https://github.com/faster-cpython/ideas/blob/main/main-vs-310.rst)。

Python 3.11 带来了一系列改进，包括性能和开发者体验。

了解这些更新并升级您的编程游戏！