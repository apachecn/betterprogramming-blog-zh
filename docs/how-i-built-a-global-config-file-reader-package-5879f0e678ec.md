# 我如何构建一个全局配置文件阅读器包

> 原文：<https://betterprogramming.pub/how-i-built-a-global-config-file-reader-package-5879f0e678ec>

## 按名称载入文件，不考虑扩展名

![](img/a46ad522f14d310bfc07c19b40906da8.png)

照片由[豪斯辛 Ncib](https://unsplash.com/@houcinencibphotography?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/reader?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

夜深了，我的孩子睡着了，我正在开发我最近构建的[打字稿覆盖工具](https://github.com/alexcanessa/typescript-coverage-report)(是的，我也写了一篇关于我如何构建它的[文章)。](https://medium.com/better-programming/how-did-i-build-a-ts-coverage-report-tool-af34e110d02c)

今晚我发现的一个问题是“[建议:添加配置文件](https://github.com/alexcanessa/typescript-coverage-report/issues/13)”

我去寻找我可以使用的选项，唯一可用的软件包是 [read-config](https://www.npmjs.com/package/read-config) 、 [read-config-file、](https://www.npmjs.com/package/read-config-file)和 [config](https://www.npmjs.com/package/config) 。但是没有一个能满足我的需求。

# 那么我需要什么？

我指定了一个名称(例如，`.eslintrc`)，工具会检查它的所有可能选项:

*   `.eslintrc`
*   `.eslintrc.json`
*   `.eslintrc.yml`
*   `.eslintrc.yaml`
*   `.eslintrc.js`
*   `eslint.config.js`(特殊情况)
*   并在`package.json`中检查一个 ESLint 对象

第二个参数可能是`options`，它允许您启用/禁用受支持的扩展——默认情况下都是活动的。

这就是我想的 API:

```
type Extension = "empty" | "json" | "yml" | "yaml" | "js" | "config.js" | "package";
type Options = {
  extensions: Record<Extension, boolean>;
};const getConfig(name: string, options: Options): object;
```

# 设置

这不会花太长时间，但是让我们把我必须要做的事情列一个清单。

1.  以打字打的文件
2.  笑话和巴别塔
3.  埃斯林特
4.  较美丽
5.  和哈士奇一起上钩
6.  问题模板
7.  编辑器设置(`.editorconfig`和`.vscode/`)

我利用这个机会为任何类型脚本通用样板文件创建了一个 GitHub 模板 repo。这就是[template-generic-typescript](https://github.com/alexcanessa/template-generic-typescript)看到光明的原因。

我不打算详细介绍我是如何研究单个技术的，因为有很多文章都是这样做的，但下面是最终的结构:

```
.
├── .github/
├── .vscode/
├── dist/ # generated
├── node_modules/ # generated
├── src/
├── .editorconfig
├── .eslintignore
├── .eslintrc.js
├── .gitignore
├── .npmignore
├── .prettierrc.js
├── README.md
├── babel.config.js
├── commitlint.config.js
├── jest.config.js
├── package.json
├── tsconfig.json
└── yarn.lock
```

# 让我们编码

我喜欢的类型和我一开始想的差不多。但是现在我使用默认选项来构造`Extensions`类型:

```
const defaultExtensions = {
  empty: true, // e.g. .eslintrc
  js: true,
  "config.js": true,
  json: true,
  yml: true,
  yaml: true,
  package: true // for package.json
};type Extensions = Record<keyof typeof defaultExtensions, boolean>;
type Options = {
  extensions: Extensions;
};
```

我必须创建的第一个函数是遍历可用的扩展名并返回第一个可用的文件。优先级可以通过对象顺序来设置，但这并不重要。

为此，我需要将扩展名映射到实际文件名的东西:

```
const getFilename = (name: string, extension: Extension): string => {
  if (extension === "package") {
    return "package.json";
  }if (extension === "empty") {
    return name;
  }if (extension === "config.js") {
    return `${name.replace(/^\./, "").replace(/rc$/, "")}.${extension}`;
  } return `${name}.${extension}`;
};
```

现在，我可以在我的`findConfigurationFile`函数中使用它:

```
const findConfigurationFile = (name: string, extensions: Extensions): string =>
  Object.entries(extensions).reduce(
    (filename, [extension, active]: [Extension, boolean]) => {
      // I found the configuration file, no need to keep looking.
      if (filename) {
        return filename;
      } // The extension has been "disabled" in the options.
      // Keep looking (filename is "" at this point).
      if (!active) {
        return filename;
      } // OK. Now I can look for it and, if exists I'll return it.
      const completeFilename = getFilename(name, extension); if (fs.existsSync(completeFilename)) {
        return completeFilename;
      } return "";
    },
    ""
  );
```

最后，我可以使用`getConfigFile`函数中的全部内容:

```
const getConfigFile = (name: string, options?: Options): object => {
  const extensions = Object.assign(
    {},
    defaultExtensions,
    (options || {}).extensions
  );
  const foundFile = findConfigurationFile(name, extensions); return {};
};
```

你可能已经注意到这个函数得到了`foundFile`，但是返回了一个空的`Object`。这是因为 JSON 和 JS 文件可能只是必需的，而 YML 和 YAML 文件有特殊的需求(所以这不是那么简单)。

我很确定我用了一个包解决了这个问题。

## YAML 问题

哦，是啊。现在我想起来了！这是必需的。但是现在，当我去寻找它时，我发现它上面有一个`Deprecated` 标签，因为它所基于的一种节点技术已经过时了。

但是我们不要气馁。现在有了一个新的软件包，名字叫做——你猜怎么着？— require-yml。

这非常好，因为无论您想加载 YML 还是 JSON 文件都没有关系:您可以使用带有任一扩展名的`req()`函数。然而，如果我加载一个无扩展的配置(例如`.eslintrc`)——可能是 JSON 或者 YAML——这个函数不理解并返回`undefined`。

好的。没问题。好像还有一个包:`[js-yaml](https://www.npmjs.com/package/js-yaml)`。就是这样！用法非常简单，我可以将它用于 JSON 和 YAML。

```
const getObjectFromConfigFile = (filename: string): object => {
  const pathToFile = path.join(process.cwd(), filename); if (path.extname(filename) === ".js") {
    return require(pathToFile);
  } return yaml.safeLoad(fs.readFileSync(pathToFile, "utf-8"));
};
```

这个软件包已经运行得相当好了；但是，我还是想改进一下。

我希望代码期待 ESLint，并用`.`和`rc`检查所有选项。但不是现在。稍后我会创建一个问题，明天再做。

## 快到了

我认为现在剩下的只是添加测试和一个 README 文件，然后我就可以开始使用我的全新工具了。

嗯，只有一个公共函数，因此只有一个`index.test.ts`文件。
在我开始写测试之前，我列出了所有我想覆盖的`it.todo`:

```
describe("globalConfigFile function", () => {
  it.todo("loads a .json file");
  it.todo("loads a .yml file");
  it.todo("loads a .yaml file");
  it.todo("loads an extension-free file");
  it.todo("loads a .js file");
  it.todo("loads a .config.js file");
  it.todo("loads the package.json");
  it.todo("throws an error if no files are found");
  it.todo("throws an error if empty extensions are passed to options");
});
```

我发誓以前从没发生过这种事。为上述内容编写测试一次就覆盖了整个项目的 100%。

我必须添加的一件事(测试指出了这一点)是一个`root`选项。

对于自述文件，这些天我按照[标准自述文件](https://github.com/RichardLitt/standard-readme)给它一点结构。

# 结论

所以在几个小时内，我们制作了一个可以跨不同项目使用的功能工具(哦！我差点忘了我为什么要开始这个！).

总的来说，我强烈反对多此一举。我建议使用已经被使用、测试和改进过的包。

然而，有时(很少)一部分功能或工具不在 npm 上，或者不适合我们的情况。也许使它工作所需的代码非常小，并被归入一个`utils`函数。在这种情况下，我建议挑战自己，让它成为一个开源项目。

现在，我可以用我可以使用的新工具更新我的问题。