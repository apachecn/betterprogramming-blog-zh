# 使用 Python 的 Glob 生成器

> 原文：<https://betterprogramming.pub/glob-generators-with-python-be9ad86ca682>

## Python 路径和 Git 忽略文件有什么共同点？

![](img/4a729e418c71ba237c07ddb786be29ba.png)

全局文件搜索

这是一个使用 [glob](https://en.wikipedia.org/wiki/Glob_(programming)) 模式查找目录中所有图像文件的练习。我们将使用`pathlib` Python [模块](https://docs.python.org/3/library/pathlib.html)，它在`Path`类中提供了`glob`方法。

Glob 模式也在`.gitignore` [文件](https://git-scm.com/docs/gitignore)中使用，所以在这里工作的任何模式也可能在 Git ignore 文件中工作，反之亦然。

最终代码[此处](https://github.com/AlbertoV5/python-blog/blob/main/src/glob/get_imgs.py)。

# 选择模式

我们将从这两种模式中的任何一种开始。

```
*.[jpJP][npNP][egEG]**.[jpJP]*[gG$]
```

我们的第一个模式意味着“文件扩展名以 j/J 或 p/P 开始，然后以 n/N 或 p/P 继续，然后以 e/E 或 g/G 继续，并以任何字符或无字符继续”。这将有效地匹配`jpg`、`jpeg`、`png`以及它们的大写版本。不过这个也会配`jpega`和`pngx`之类的。

如果我们想确保不匹配非预期的文件，我们可以使用第二种模式，即“扩展名以 j/J 或 p/P 开头，以 g/G 结尾的文件”。这个也匹配`jpg`、`jpeg`和`png`，不会匹配`jpega`或`pngx`，但是会匹配`jag`或`pxg`这样的图案。

如果我们将这两种模式结合起来，我们可以得到更严格的匹配，确保文件格式以 g/G 结尾，并且匹配所有三个目标扩展名。

```
*.[jpJP][npNP]*[gG$]
```

# Glob 函数

一旦我们用`pathlib`得到它们的路径，我们将使用`PIL` [模块](https://pillow.readthedocs.io/en/stable/)来处理图像。

```
from pathlib import Path
from PIL import Image
```

我们将定义一个函数，并在编写代码之前指定我们想要做的事情。在我们的参数中，我们希望接收一个字符串和几个布尔值来修改函数的行为。

当遍历一个目录时，结果将是无序的，所以我们可能希望使用`sorted`按字母顺序对它们进行排序。我们也可能想要递归地搜索所有目录，我们可以使用`rglob`来实现。

最后，我们想让它成为一个生成器函数，这样我们就可以在任何给定的迭代中停止这个过程，所以我们将对文件路径进行`yield`。

```
def get_imgs(directory: str, sort: bool = True, recursive: bool = True):
    """Yields all image files in given path. Args: directory (str): Relative directory name.
        sort (bool, optional): If generator is sorted. Default True.
        recursive (bool, optional): If glob is recursive. Default True. Yields: (Path): File path.
    """
```

让我们开始检查给定的路径是否存在。我们将使用`resolve`将任何相对路径转换为绝对路径，然后使用`is_dir`检查目录是否存在。

```
path = Path(directory).resolve()
if not path.is_dir():
    raise ValueError(f"'{path}' is not a directory.")
```

我们的`pattern`变量将保存 glob 模式，如果我们希望我们的搜索是递归的，那么`frec`将使用`rglob`，如果我们希望对结果进行排序，那么`fsort`将使用`sorted`。

```
pattern = '*.[jpJP][npNP]*[gG$]'
frec = lambda p, g: p.rglob(g) if recursive else p.glob(g)
fsort = lambda x: sorted(x) if sort else x
```

最后，我们将使用 lambda 函数和每个文件路径。

```
for file in fsort(frec(path, pattern)):
    yield file
```

这就是我们的功能，一旦我们把它放在一起。

```
from pathlib import Path
from PIL import Imagedef get_imgs(directory: str, sort: bool = True, recursive: bool = True):
    """Yields all image files in given path. Args: directory (str): Relative directory name.
        sort (bool, optional): If generator is sorted. Default True.
        recursive (bool, optional): If glob is recursive. Default True. Yields: (Path): File path.
    """
    path = Path(directory).resolve()
    if not path.is_dir():
        raise ValueError(f"'{path}' is not a directory.")
    pattern = '*.[jpJP][npNP]*[gG$]'
    frec = lambda p, g: p.rglob(g) if recursive else p.glob(g)
    fsort = lambda x: sorted(x) if sort else x
    for file in fsort(frec(path, pattern)):
        yield file
```

让我们在一个脚本中使用它，简单地打印相对于根目录的所有文件路径。

```
root = Path("..").resolve()
for f in get_imgs('../resources'):
    print(f.relative_to(root))
```

结果:

```
resources/data/Screen Shot 2022-09-05 at 12.41.33.png
resources/data/Screen Shot 2022-09-05 at 12.41.45.png
resources/final/007.jpeg
resources/final2/000.jpeg
resources/img/008.jpeg
resources/img/009.jpg
resources/img/Screen Shot 2022-09-29 at 13.20.27.png
resources/img/Screen Shot 2022-09-29 at 20.07.04.png
resources/img/Screen Shot 2022-09-29 at 20.08.21.png
resources/old/002.jpeg
resources/old/006.jpg
```

现在让我们继续用`PIL`处理文件。

# 处理文件

我们希望将所有的`png`文件转换成`jpeg`，并将`jpg`重命名为`jpeg`，然后调整它们的大小，使其具有给定的最大宽度。

我们将使用`PIL`中的`ImageOps`模块来简化尺寸调整操作并保持纵横比。过程如下:我们以图像的形式打开文件，如果`suffix`是`png`就转换成`'RGB'`，然后用`ImageOps.contain`调整大小，用`with_suffix`保存为`jpeg`。

在编写我们的主进程之前，我们将创建一个函数来替换文件后缀`jpeg`并改变它的父目录。我们还将为打印目的创建一个`root`变量。

```
from PIL import ImageOpsmax_width = 1280
with_path = lambda f: Path('../converted').resolve() / f.with_suffix('.jpeg').name
root = Path("..").resolve()
for f in get_imgs('../resources'):
    with Image.open(f) as img:
        if f.suffix == '.png':
            img = img.convert('RGB')
        if img.size[0] > max_width:
            img = ImageOps.contain(img, (max_width, max_width))
        fout = with_path(f)
        img.save(fout, quality=80)
        print(fout.relative_to(root))
```

结果:

```
converted/Screen Shot 2022-09-05 at 12.41.33.jpeg
converted/Screen Shot 2022-09-05 at 12.41.45.jpeg
converted/007.jpeg
converted/000.jpeg
converted/008.jpeg
converted/009.jpeg
converted/Screen Shot 2022-09-29 at 13.20.27.jpeg
converted/Screen Shot 2022-09-29 at 20.07.04.jpeg
converted/Screen Shot 2022-09-29 at 20.08.21.jpeg
converted/002.jpeg
converted/006.jpeg
```

请注意，我们将结果放在一个不同于我们用来搜索的父目录中，因为默认情况下我们是递归进行搜索的。这是因为我们不想在第二次运行脚本时将结果作为输入。

# 结论

Python 中的生成器是处理数据最有用的工具之一。如果我们想在不使用 bash 脚本的情况下自动完成一些任务(不管出于什么原因)，我们可以从一个 glob pattern 函数开始，它在一个无序的目录中生成我们想要的文件类型。

使用`glob`的好处是我们不必使用 Python 来检查每个文件后缀是否匹配一系列文件格式，因此我们可以更容易地扩展我们的过程。

例如，在下面的代码中，我们可以用第二个理解替换第一个理解。

```
[f for f in path.iterdir() if f.suffix in ('.jpeg', '.jpg', '.png', 'JPG', 'JPEG', 'PNG')][f for f in path.glob('*.[jpJP][npNP]*[gG$]')]
```

我们也可以用`rglob`递归地做这件事，或者把`**/`加到我们模式的开头。缺点是，如果我们的模式不够好，我们可能会匹配非预期的文件，并且 globs 不如正则表达式强大。另外，我们可以在我们的。gitignore 文件。

# 脚注

[https://en . Wikipedia . org/wiki/Glob _(编程)](https://en.wikipedia.org/wiki/Glob_(programming))

[https://docs.python.org/3/library/pathlib.html](https://docs.python.org/3/library/pathlib.html)

[https://git-scm.com/docs/gitignore#_pattern_format](https://git-scm.com/docs/gitignore#_pattern_format)

[https://pillow.readthedocs.io/en/stable/](https://pillow.readthedocs.io/en/stable/)

[https://unsplash.com/photos/2JIvboGLeho?utm_source=unsplash&UTM _ medium = referral&UTM _ content = creditShareLink](https://unsplash.com/photos/2JIvboGLeho?utm_source=unsplash&utm_medium=referral&utm_content=creditShareLink)

*原载于*[*https://github.com*](https://github.com/AlbertoV5/python-blog/blob/main/docs/blog.md)*。*