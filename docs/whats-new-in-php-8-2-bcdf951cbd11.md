# PHP 8.2 的新特性

> 原文：<https://betterprogramming.pub/whats-new-in-php-8-2-bcdf951cbd11>

## 只读类、新的“随机”扩展等等

![](img/ea00528de79c3cf7732f312dc5a5a0a2.png)

照片由[蒂姆·莫斯霍尔德](https://unsplash.com/@timmossholder?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在这篇文章中，我将快速解释 PHP 在圣诞节通过 PHP 8.2 版本提供给我们的新东西。

这是 PHP 语言的一个重大更新。它包含许多新功能，包括只读类、null、false 和 true 作为独立类型、不推荐使用的动态属性、性能改进等等。

# 只读类

PHP < 8.2

```
class BlogData
{
    public readonly string $title;

    public readonly Status $status;

    public function __construct(string $title, Status $status)
    {
        $this->title = $title;
        $this->status = $status;
    }
}
```

PHP ≥ 8.2

```
readonly class BlogData
{
    public string $title;

    public Status $status;

    public function __construct(string $title, Status $status)
    {
        $this->title = $title;
        $this->status = $status;
    }
}
```

As of PHP 8.2.0, a class can be marked with the 【 modifier. Marking a class as 【 will add the [readonly 修饰符](https://www.php.net/manual/en/language.oop5.properties.php#language.oop5.properties.readonly-properties)到每个声明的属性，并阻止[动态属性](https://www.php.net/manual/en/language.oop5.properties.php#language.oop5.properties.dynamic-properties)的创建。

此外，不可能通过使用 [AllowDynamicProperties](https://www.php.net/manual/en/class.allow-dynamic-properties.php) 属性来添加对它们的支持。试图这样做将触发编译时错误。

```
#[AllowDynamicProperties]
readonly class Foo {
}

// Fatal error: Cannot apply #[AllowDynamicProperties] to readonly class Foo
```

由于非类型属性和静态属性都不能用`readonly`修饰符标记，`readonly`类也不能声明它们。

```
readonly class Foo
{
    public $bar;
}

// Fatal error: Readonly property Foo::$bar must have type
?>
```

```
readonly class Foo
{
    public static int $bar;
}

// Fatal error: Readonly class Foo cannot declare static properties
```

记住，当且仅当子类也是一个`readonly`类时，一个`readonly`类才能被扩展。

# 析取范式(DNF)类型

PHP < 8.2

```
class Foo {
    public function bar(mixed $entity) {
        if ((($entity instanceof A) && ($entity instanceof B)) || ($entity === null)) {
            return $entity;
        }

        throw new Exception('Invalid entity');
    }
}
```

PHP ≥ 8.2

```
class Foo {
    public function bar((A&B)|null $entity) {
        return $entity;
    }
}
```

DNF *(psss，如果你想了解更多关于析取范式的知识，请看* [*本维基百科页面*](https://en.wikipedia.org/wiki/Disjunctive_normal_form) *)* 类型允许我们组合[并集](https://www.php.net/manual/en/language.types.type-system.php#language.types.type-system.composite.union)和[交集](https://www.php.net/manual/en/language.types.type-system.php#language.types.type-system.composite.intersection)类型，遵循一个严格的规则:当组合并集和交集类型时，交集类型必须用括号分组。

请看下面的一些例子来更好地理解它

```
// Accepts an object that implements both A and B,
// OR an object that implements D.
(A&B)|D

// Accepts an object that implements C, 
// OR a child of X that also implements D,
// OR null.
C|(X&D)|null

// Accepts an object that implements all three of A, B, and D, 
// OR an int, 
// OR null.
(A&B&D)|int|null
```

# 允许 null、false 和 true 作为独立类型

PHP < 8.2

```
class Falsy
{
    public function almostFalse(): bool { /* ... */ *}

    public function almostTrue(): bool { /* ... */ *}

    public function almostNull(): string|null { /* ... */ *}
}
```

PHP ≥ 8.2

```
class Falsy
{
    public function alwaysFalse(): false { /* ... */ *}

    public function alwaysTrue(): true { /* ... */ *}

    public function alwaysNull(): null { /* ... */ *}
}
```

See an example of why the definition of the 【 type does make sense indeed

```
class User {
    function isAdmin(): bool
}

class Admin extends User
{
    function isAdmin(): true
    {
        return true;
    }
}
```

# New “Random” extension

PHP ≥ 8.2

```
use Random\Engine\Xoshiro256StarStar;
use Random\Randomizer;

$blueprintRng = new Xoshiro256StarStar(
    hash('sha256', "Example seed that is converted to a 256 Bit string via SHA-256", true)
);

$fibers = [];
for ($i = 0; $i < 8; $i++) {
    $fiberRng = clone $blueprintRng;
    // Xoshiro256**'s 'jump()' method moves the blueprint ahead 2**128 steps, as if calling
    // 'generate()' 2**128 times, giving the Fiber 2**128 unique values without needing to reseed.
    $blueprintRng->jump();

    $fibers[] = new Fiber(function () use ($fiberRng, $i): void {
        $randomizer = new Randomizer($fiberRng);

        echo "{$i}: " . $randomizer->getInt(0, 100), PHP_EOL;
    });
}

// The randomizer will use a CSPRNG by default.
$randomizer = new Randomizer();

// Even though the fibers execute in a random order, they will print the same value
// each time, because each has its own unique instance of the RNG.
$fibers = $randomizer->shuffleArray($fibers);
foreach ($fibers as $fiber) {
    $fiber->start();
}
```

The “random” extension provides a new object-oriented API to random number generation. Instead of relying on a globally seeded random number generator (RNG) using the [Mersenne Twister 算法](https://en.wikipedia.org/wiki/Mersenne_Twister)面向对象的 API 提供了几个类(“引擎”)，这些类提供了对现代算法的访问，这些算法将它们的状态存储在对象中，以允许多个独立的可播种序列。

`\Random\Randomizer`类提供了一个高级接口，使用引擎的随机性来生成一个随机整数，打乱一个数组或字符串，选择随机数组键等等。

# 性状中的常数

PHP ≥ 8.2

```
trait Foo
{
    public const CONSTANT = 1;
}

class Bar
{
    use Foo;
}

var_dump(Bar::CONSTANT); // 1
var_dump(Foo::CONSTANT); // Error
```

Trait 常量可以以与类常量相同的方式定义，并以与 trait 中的属性和方法定义相同的方式平铺到组成类的定义中。

您不能通过特征的名称来访问常量，但是，您可以通过使用该特征的类来访问常量。

# 弃用动态属性

PHP < 8.2

```
class User
{
    public $name;
}

$user = new User();
$user->last_name = 'Doe';

$user = new stdClass();
$user->last_name = 'Doe';
```

PHP ≥ 8.2

```
class User
{
    public $name;
}

$user = new User();
$user->last_name = 'Doe'; // Deprecated notice

$user = new stdClass();
$user->last_name = 'Doe'; // Still allowed
```

不赞成创建动态属性，以帮助避免错误和打字错误，除非类通过使用`#[\AllowDynamicProperties]`属性选择加入。`stdClass`允许动态属性。

这个改动不会影响`__get` / `__set`魔法方法的使用。

移除对动态属性的支持构成了严重的向后兼容性破坏。虽然现代 PHP 代码始终声明使用的属性，但遗留代码不一定是这样。

当遇到动态属性弃用警告时，有许多事情可以避免它。最简单和最常见的方法是简单地添加属性声明:

```
class Test {
    public $value; // <-- Add property declaration.

    public function __construct($value) {
        $this->value = $value;
    }
}
```

那么，你还在等什么？查看升级到 PHP 8.2 的迁移指南并尝试一下吧！我希望你喜欢这篇文章，并在下一篇文章中看到你！

# 资源

*   [https://www.php.net/releases/8.2/en.php](https://www.php.net/releases/8.2/en.php)
*   https://wiki.php.net/rfc/dnf_types
*   【https://wiki.php.net/rfc/true-type 
*   [https://wiki.php.net/rfc/constants_in_traits](https://wiki.php.net/rfc/constants_in_traits)
*   [https://wiki.php.net/rfc/deprecate_dynamic_properties](https://wiki.php.net/rfc/deprecate_dynamic_properties)
*   [https://www.php.net/manual/en/migration82.php](https://www.php.net/manual/en/migration82.php)