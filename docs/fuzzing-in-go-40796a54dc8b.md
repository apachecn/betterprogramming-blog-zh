# 理解围棋中的模糊

> 原文：<https://betterprogramming.pub/fuzzing-in-go-40796a54dc8b>

## 动作中的模糊

![](img/ae0cb6229222f460c53a4e10eeffac81.png)

照片由 [Tim Hüfner](https://unsplash.com/@huefnerdesign?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

"模糊测试是发现软件应用程序中安全漏洞或缺陷的一种新方法."

模糊化的概念是在 1988 年提出的，当时巴顿·米勒教授和他的学生发现，一个操作系统“在受到随机的意外输入时通常会崩溃。”

模糊测试通过使用提供的种子数据为函数生成输入。这是从代码中发现意外行为的好方法。当我第一次看到 fuzzing 的概念时，我认为这将是一个自动化测试的好方法。

为了将这个想法放入上下文中，我发现在过去很难想到测试输入案例，fuzzing 似乎是一个自动化的好方法。模糊化不是不实现`Unit`和其他形式测试的借口，因为模糊化会为函数生成输入。

Fuzzing 是在 Go 1.18 中引入的，所以我有点迟到了。在这篇文章中，我将尝试描述模糊化的一个基本用例。

# CSV 行解析器

对于我的用例，我将编写一个解析 CSV 行的函数。

该函数将采用逗号分隔的字符串，并处理一些数据。

该函数将尝试将第一列转换为 int，并将`Senegal`追加到第三列的值中。

第三列代表没有指定国家的地址。

下面是该函数的样子:

```
package fuzzingimport (
 "strings"
 "strconv"
)func ParseString(s string) error {
  parts := strings.Split(s, ",") _, err := strconv.Atoi(parts[0]) if err != nil {
  return err
 } parts[2] += ", Senegal" return nil
}
```

现在我有了我的函数，我将继续定义模糊测试。与单元测试不同，我必须为 fuzzer 提供种子数据。

在我的函数`ParseString`中，我提供了 2 个逗号分隔的 CSV 行，3 列。

在编写模糊测试时，用下面的格式命名测试是很重要的`FuzzXxx`。我让测试检查来自函数`ParseString`的错误。

以下是测试文件:

```
package fuzzingimport (
 "testing"
)func **FuzzParseString**(f *testing.F){
 f.**Add**("1, Name, 23rd Fifth Street")
 f.**Add**("2, Name2, 1 Main street") f.Fuzz(func(t *testing.T, s string){
    err := **ParseString**(s) if err != nil {
     t.Errorf("%v", err)
    }
 })
}
```

作为一名程序员，我最大的弱点是无法理解边缘情况。所以当我运行测试时，第一次运行就失败了。

当我检查提供的数据时，提供了一个空字符串。

下面是测试的输出:

```
cheikh@cheikh-s5-1110:~/go/src/fuzzing$ go test -fuzz FuzzParseString
fuzz: elapsed: 0s, gathering baseline coverage: 0/8 completed
fuzz: minimizing 41-byte failing input file
fuzz: elapsed: 0s, gathering baseline coverage: 2/8 completed
--- FAIL: FuzzParseString (0.01s)
    --- FAIL: FuzzParseString (0.00s)
        testing.go:1349: panic: runtime error: index out of range [2] with length 1
            goroutine 33 [running]:
            runtime/debug.Stack()
             /usr/lib/go-1.18/src/runtime/debug/stack.go:24 +0x90
            testing.tRunner.func1()
             /usr/lib/go-1.18/src/testing/testing.go:1349 +0x1f2
            panic({0x5b3700, 0xc000116210})
             /usr/lib/go-1.18/src/runtime/panic.go:838 +0x207
            fuzzing.ParseString({0x6dcc20?, 0xc00008ea50?})
             /home/cheikh/go/src/fuzzing/lib.go:17 +0x105
            fuzzing.FuzzParseString.func1(0xc000130680, {0x6dcc20, 0x1})
             /home/cheikh/go/src/fuzzing/lib_test.go:12 +0x45
            reflect.Value.call({0x594a60?, 0x5cfb08?, 0x13?}, {0x5c17a6, 0x4}, {0xc00008f050, 0x2, 0x2?})
             /usr/lib/go-1.18/src/reflect/value.go:556 +0x845
            reflect.Value.Call({0x594a60?, 0x5cfb08?, 0x514?}, {0xc00008f050, 0x2, 0x2})
             /usr/lib/go-1.18/src/reflect/value.go:339 +0xbf
            testing.(*F).Fuzz.func1.1(0x0?)
             /usr/lib/go-1.18/src/testing/fuzz.go:337 +0x231
            testing.tRunner(0xc000130680, 0xc0000e8990)
             /usr/lib/go-1.18/src/testing/testing.go:1439 +0x102
            created by testing.(*F).Fuzz.func1
             /usr/lib/go-1.18/src/testing/fuzz.go:324 +0x5b8
```

fuzzer 检测到运行时问题。考虑到这个错误，我将再次编辑我的函数以支持空的或不完整的字符串。新函数将如下所示:(新增加的内容以粗体显示)

```
func ParseString(s string) error {
  parts := strings.Split(s, ",") **if(len(parts) < 3){
    return errors.New("Invalid string")
  }** _, err := strconv.Atoi(parts[0]) if err != nil {
    return err
  } parts[2] += ", Senegal" return nil
}
```

我的程序将返回一个错误的错误字符串，但我不希望我的 fuzzer 就此打住。为此，如果返回的错误是`Invalid string`，我将添加一个提前返回。下面是更新后的测试函数:

```
func FuzzParseString(f *testing.F){
  f.Add("1, Name, 23rd Fifth Street")
  f.Add("2, Name2, 1 Main street") f.Fuzz(func(t *testing.T, s string){
    err := ParseString(s) if err != nil { **if err.Error() == "Invalid string" {
        return
      }** t.Errorf("%v", err)
    }  
  })
}
```

重新运行后，fuzzer 通过提供以下输入`string(",,")`检测到另一个问题。以下是终端的输出:

```
cheikh@cheikh-s5-1110:~/go/src/fuzzing$ go test -fuzz FuzzParseString
fuzz: elapsed: 0s, gathering baseline coverage: 0/8 completed
fuzz: elapsed: 0s, gathering baseline coverage: 8/8 completed, now fuzzing with 2 workers
fuzz: minimizing 61-byte failing input file
fuzz: elapsed: 0s, minimizing
--- FAIL: FuzzParseString (0.02s)
    --- FAIL: FuzzParseString (0.00s)
        lib_test.go:20: strconv.Atoi: parsing "": invalid syntax

    Failing input written to testdata/fuzz/FuzzParseString/74498b2a8282174de6870175ab847ba882cd8356506d60b9a28f23ec8e721c76
    To re-run:
    go test -run=FuzzParseString/74498b2a8282174de6870175ab847ba882cd8356506d60b9a28f23ec8e721c76
FAIL
exit status 1
FAIL fuzzing 0.025s
```

假设我设法找到了一个绕过丢失的 ID bug 的方法，fuzzer 会找到其他 bug 吗？

fuzzer 发现的下一个问题是给第一列分配一个非数字值。我可以不断重复这个过程，直到代码稳定。

这就是起毛的好处。下面是我的测试的另一个输出:

```
cheikh@cheikh-s5-1110:~/go/src/fuzzing$ go test -fuzz FuzzParseString
fuzz: elapsed: 0s, gathering baseline coverage: 0/10 completed
fuzz: elapsed: 0s, gathering baseline coverage: 10/10 completed, now fuzzing with 2 workers
fuzz: minimizing 46-byte failing input file
fuzz: elapsed: 0s, minimizing
--- FAIL: FuzzParseString (0.02s)
    --- FAIL: FuzzParseString (0.00s)
        lib_test.go:20: strconv.Atoi: parsing "A": invalid syntax

    Failing input written to testdata/fuzz/FuzzParseString/215375fdfbd654b9fd5b654b45a8676d2b81e538d2a852773673a3713deb50a6
    To re-run:
    go test -run=FuzzParseString/215375fdfbd654b9fd5b654b45a8676d2b81e538d2a852773673a3713deb50a6
FAIL
exit status 1
FAIL fuzzing 0.025s
```

# 结论

测试是围棋的核心部分。有没有可能不经过测试就写出一个程序？是的，如果你想牺牲你的节目质量。

Fuzzing 使开发人员能够测试意外情况。它不会取代其他类型的测试，而是对其进行补充。这是增加测试覆盖率和识别测试用例的好方法。

在编写了漂亮的惯用代码后，我们可能会有点精疲力尽地生成测试用例，这就是 fuzzing 的亮点。

值得注意的是，测试包不支持复杂类型，这是我希望在未来看到的。

# 参考

[](https://about.gitlab.com/topics/devsecops/what-is-fuzz-testing/) [## 什么是模糊测试？

### 模糊测试是发现软件应用程序中安全漏洞或错误的一种新方法。不同于传统的…

about.gitlab.com](https://about.gitlab.com/topics/devsecops/what-is-fuzz-testing/) [](https://github.com/cheikhshift/medium_examples/tree/main/fuzzing) [## medium _ examples/在主 cheikhshift/medium_examples 处模糊化

### 中型文章的代码示例。在 GitHub 上创建一个帐户，为 cheikhshift/medium_examples 开发做贡献。

github.com](https://github.com/cheikhshift/medium_examples/tree/main/fuzzing) [](https://go.dev/doc/fuzz/#glos-fuzz-test) [## Go Fuzzing-Go 编程语言

### 从 Go 1.18 开始，Go 在其标准工具链中支持模糊化。OSS-模糊支持本地 Go 模糊测试。尝试…

go.dev](https://go.dev/doc/fuzz/#glos-fuzz-test)