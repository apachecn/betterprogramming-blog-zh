# 使用 TypeScript 来减少单元测试

> 原文：<https://betterprogramming.pub/use-typescript-to-reduce-your-unit-tests-834897e16d7b>

## 通过减少测试时间来加快开发时间

![](img/002cb586fc5462d61a38803c21e69b19.png)

照片由[卢西恩·阿利克夏](https://unsplash.com/@lucian_alexe?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

我从那些没有采用 TypeScript 的人那里听到的最常见的论点之一是“如果事情在 JavaScript 中工作，为什么我要用 TypeScript 编写它？”。当他们看到他们必须编写的额外代码时，他们的兴趣水平进一步下降。

但是我将向您展示一个小例子，说明 TypeScript 如何在保持项目质量的同时帮助减少测试用例的数量。

我们有一个`util`函数，它接受字符串或数字，但不接受数组:

```
const util = (val) => {
    if (Array.isArray(val)) {
        throw new Error("Array is not accept.");
    }
    return val
}
```

我们将编写的最小测试是:

```
describe("util", () => {
    it("Should return a string", () => {
        expect(util("abc")).toBeTypeof("string");
    });
    it("Should return a number", () => {
        expect(util(123)).toBeTypeof("number");
    });
    it("Should error", () => {
        expect(() => util([])).toThrow();
    });
});
```

如果我们在 TypeScript 中这样做:

```
const util = <T>(val: T extends any[] ? "array is not accepted" : T) => {
    return val;
}util('hello');
util(123)
util([1,2,3]) //error
util([]) //error
```

下面先睹为快[演示](https://www.typescriptlang.org/play?jsx=0&ts=4.5.5#code/MYewdgzgLgBArlAlgGxgXhgHgCoD4AUAbgIbIBcM2MApgB5TVgAmEMxYAngNoC6MA-DABExAE6jiHGIlZgQsYsGDUADgyZCYFbAEp0uGAG8AUDDMxR1KHFFgYJZAG5jAX2PGEKfAHIAFtWRkEG8dZ09kfABGACYAZh0PJAiuSIAaaNTYnj0AehzqcRBRRK9eXPzC0SA)。它将在运行时检查期间抛出错误，您可以安全地删除您的测试！

想象一下复合优势:

1.  您将获得错误的即时反馈。
2.  您将编写更少的测试用例，因此维护这些测试需要更少的时间。
3.  在推送代码之前，您节省了在本地运行较少测试的时间。
4.  ci 管道中的测试更少，这意味着构建时间更快。

现在，乘以你团队中开发人员的数量。

TypeScript 不错！