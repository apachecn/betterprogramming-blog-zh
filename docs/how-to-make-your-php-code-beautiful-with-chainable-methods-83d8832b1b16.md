# 如何用可链接的方法让你的 PHP 代码变得漂亮

> 原文：<https://betterprogramming.pub/how-to-make-your-php-code-beautiful-with-chainable-methods-83d8832b1b16>

## 函数式编程，认识 PHP

![](img/cb6eab9db28ac0fad6aacdc272d1844e.png)

照片由[威廉张](https://unsplash.com/@zhangyuwei_cyclens?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/dock?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

PHP 有一个丑陋的坏名声。这种语言随着时间的推移逐渐演变，这是显而易见的。

但它也有许多可取之处。PHP 开发团队在每一个版本中都不断改进这种语言。尤其是 PHP 7，在特性和性能上都取得了长足的进步。

这种语言的其他好处包括友好的文档，无处不在的主机，以及大量的在线资源(栈溢出等)。

是的，所有最大的 CMS 平台(Wordpress，Drupal，Joomla)都是基于 PHP 的，在这个领域还有很多工作要做。

那么，对于我们这些使用 PHP 并想写出漂亮代码的人来说，如何实现呢？

反对者可能会声称这是不可能的，但我不同意。像[Laravel](https://laravel.com/)(“web 工匠的 PHP 框架”)这样的框架已经证明他们错了。

Laravel 和其他受尊敬的 PHP 框架经常实现的一种模式是*方法链接。*

下面是一个例子，Laravel 的查询构建器 API:

```
$users = DB::table('users')
    ->where('votes', '>', 100)
    ->orWhere('name', 'John')
    ->get();
```

参考:[https://laravel.com/docs/5.6/queries](https://laravel.com/docs/5.6/queries)。

这种“链接”语法很好，因为它允许开发人员编写代码，以表达性和可读性强的方式一次做一件事。在上面的例子中，很容易看出发生了什么:

1.  查看`users`表中的行。
2.  票数超过 100 的地方。
3.  或者用户的名字是约翰。
4.  得到结果。

很圆滑，对吧？让我们深入探讨如何将这种模式应用到生活中。

# 具体的例子

为了说明方法链是如何工作的，让我们以数组操作为例。PHP 有很多有用的数组函数，如`array_map`、`array_filter`、`usort`等。但是在一个数组上执行多个操作会很难看。

假设我们有一个 Simpson 角色名数组，我们希望应用以下转换:

1.  过滤掉所有不以“Simpson”结尾的名字。
2.  将每个全名映射到一个名字。例如，我们将“丽莎·辛普森”替换为简单的“丽莎”。
3.  按字母顺序排列我们的结果。

这里有一种方法可以解决我们的问题:

```
$characters = [
    'Maggie Simpson',
    'Edna Krabappel',
    'Marge Simpson',
    'Lisa Simpson',
    'Moe Szyslak',
    'Waylon Smithers',
    'Homer Simpson',
    'Bart Simpson'
];// Look for full names that end in Simpson
// Filter out items that don't match
$simpsons = array_filter($characters, function ($character) {
    return preg_match('/^.+\sSimpson$/', $character);
});// Replace " Simpson" with an empty string for each item
$simpsons = array_map(function ($character) {
    return str_replace(' Simpson', '', $character);
}, $simpsons);// Sort the items using PHP's "strcasecmp"
usort($simpsons, function ($a, $b) {
    return strcasecmp($a, $b);
});var_dump($simpsons); // ['Bart', 'Homer', 'Lisa', 'Maggie', 'Marge']
```

这为我们提供了正确的结果，但眼尖的人可能会发现一些细节:

1.  `array_filter`接受数组和回调，但`array_map`接受回调和数组。为什么这些函数会以不同的顺序接受参数？
2.  过滤和映射操作被保存到`$simpsons`变量中，但是`usort`通过引用访问我们的数组。为什么不一致？
3.  这看起来有点难看。

作为比较，让我们看看如何用 JavaScript 解决这个问题:

```
const simpsons = characters
  .filter(character => character.match(/^.+\sSimpson$/))
  .map(character => character.replace(' Simpson', ''))
  .sort((a, b) => b < a)console.log(simpsons)
```

JavaScript 解决方案要优雅得多。数组是 JavaScript 中的一种对象，这允许数组操作被链接起来。

旁白:为了简洁起见，上面的 JavaScript 使用了[箭头函数](https://laravel-news.com/short-arrow-functions)。PHP 还没有箭头功能，但是我们可能很快就会有了！

JavaScript 中的数组方法链接是免费的，但是我们可以通过编写自己的自定义类在 PHP 中模拟这一点。

# 用可链接的方法创建类

姑且称之为`Collection`。可以通过将数组传递到构造函数中来创建该类的实例。

```
class Collection
{
    private $array; public function __construct($array)
    {
        $this->array = $array;
    }
}$characters = new Collection([
    'Maggie Simpson',
    'Edna Krabappel',
    'Marge Simpson',
    'Lisa Simpson',
    'Moe Szyslak',
    'Waylon Smithers',
    'Homer Simpson',
    'Bart Simpson'
]);
```

到目前为止，这个类并没有做很多事情——它只是保存了作为私有属性传递的数组。让我们着眼于添加一个公共的`filter`方法。

```
public function filter($callback)
{
    $this->array = array_filter($this->array, $callback);
    return $this;
}
```

现在只需要回调函数，而不是像以前一样必须同时传递数组和回调函数作为参数。一旦实例的 array 属性被转换，就返回实例本身。这使得方法链接成为可能。

接下来，让我们为`map`和`sort`添加方法:

```
public function map($callback)
{
    $this->array = array_map($callback, $this->array);
    return $this;
}public function sort($callback)
{
    usort($this->array, $callback);
    return $this;
}
```

现在我们的方法已经准备好被链接了，但是我们需要一种方法以数组的形式获取最终结果。这类似于 Laravel 查询构建器如何使用`get()`来执行已经用条件构建好的查询。

```
public function execute()
{
    return $this->array;
}
```

综上所述，我们的类如下所示:

```
class Collection
{
    private $array; public function __construct($array)
    {
        $this->array = $array;
    } public function filter($callback)
    {
        $this->array = array_filter($this->array, $callback);
        return $this;
    } public function map($callback)
    {
        $this->array = array_map($callback, $this->array);
        return $this;
    } public function sort($callback)
    {
        usort($this->array, $callback);
        return $this;
    } public function execute()
    {
        return $this->array;
    }
}
```

而我们最后可以如下链数组方法！

```
$characters = new Collection([
    'Maggie Simpson',
    'Edna Krabappel',
    'Marge Simpson',
    'Lisa Simpson',
    'Moe Szyslak',
    'Waylon Smithers',
    'Homer Simpson',
    'Bart Simpson'
]);$simpsons = $characters
    ->filter(function ($character) {
        return preg_match('/^.+\sSimpson$/', $character);
    })
    ->map(function ($character) {
        return str_replace(' Simpson', '', $character);
    })
    ->sort(function ($a, $b) {
        return strcasecmp($a, $b);
    })
    ->execute();var_dump($simpsons); // ['Bart', 'Homer', 'Lisa', 'Maggie', 'Marge']
```

非常 JavaScript 风格！参数顺序和引用访问的问题仍然存在，但是我们的`Collection`类在幕后处理这个问题。结果是代码更加干净，可读性更好！

# 摘要

PHP 因混乱而臭名昭著，但 Laravel 和其他框架正在使用可链接的方法(以及其他方法)来鼓励漂亮的代码并挑战这种观念。

PHP 中的数组操作不像在 JavaScript 中那样是可链接的，而是一个基本的实用类(就像我们的`Collection`类！)可以用来模拟这一点，并将 PHP 的一些小怪癖隐藏起来。