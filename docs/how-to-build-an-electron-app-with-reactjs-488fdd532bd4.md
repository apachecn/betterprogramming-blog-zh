# 如何用 ReactJS 构建一个电子 App

> 原文：<https://betterprogramming.pub/how-to-build-an-electron-app-with-reactjs-488fdd532bd4>

## 没有太多样板文件

![](img/4eb8348875430b4b358655aa78c8ea5b.png)

照片由[raphal Biscaldi](https://unsplash.com/@les_photos_de_raph?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/atom-electrons?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

如果你是第一次接触[电子](https://electronjs.org/)并且更喜欢使用 [ReactJS](https://reactjs.org/) ，你可能会在 GitHub 上偶然发现这个[样板](https://github.com/electron-react-boilerplate/electron-react-boilerplate)。

这个样板很棒。然而，它包含了太多我不需要的花哨东西。如果你和我一样，我只是想把我的 React 项目放到电子版上，看看它是如何工作的。

如果你想做到这一点，这里有几个要点。

# 是否将电子与 React 应用程序结合起来？

如果您决定在 CRA 内部合并 electron，您的项目看起来就像一个普通的 React 应用程序，其中`electron-starter.js`是 Electron 的入口点。

```
├── Procfile
├── package.json
├── public
│   ├── favicon.ico
│   └── index.html
└── src
    ├── App.css
    ├── App.js
    ├── App.test.js
    ├── electron-starter.js
    ├── electron-wait-react.js
    ├── index.css
    ├── index.js
    └── logo.svg
```

如果这是你正在寻找的，这里有一个完整的教程，教你怎么做:Christian Sepulveda 的《[用 create-react-app](https://www.freecodecamp.org/news/building-an-electron-application-with-create-react-app-97945861647c/) 构建电子应用程序》。

但是，如果您正在将 NodeJS 应用程序迁移到 Electron，并且希望将后端(现在是主流程代码)分开，您可以尝试这种结构。

```
├── Procfile
├── app
│   ├── config
│   ├── events
│   ├── libs
│   ├── models
│   └── views
│       ├── js
│       │   └── preload.js
│       └── react
│           ├── README.md
│           ├── build
│           ├── package.json
│           ├── public
│           └── src
├── assets
│   └── icon.png
├── electron-wait-react.js
├── main.js
└── package.json
```

在这种情况下，`main.js`将是入口点，电子的构建文件夹不会与 CRA 的构建文件夹冲突。

# 设置开发和生产环境

由于您不希望每次进行更改时都重新启动您的电子应用程序，因此您可以使用 React 的 webpack-dev-server 的 URL 来指定电子应用程序。

```
const { app, BrowserWindow } = require('electron');const path = require('path');const url = require('url');let mainWindowfunction createWindow () {// Create the browser window.mainWindow = new BrowserWindow({ width: 1024, height: 728});const startUrl = process.env.ELECTRON_START_URL || url.format({pathname: path.join(__dirname, 'app/views/react/build/index.html'),protocol: 'file:',slashes: true});mainWindow.loadURL(startUrl);
```

如果有一个`ELECTRON_START_URL`作为环境变量，它将使用该 URL 而不是 React 的内置版本。

感谢上面提到的 Christian 的教程，你也可以使用 [Foreman](https://www.npmjs.com/package/foreman) 来管理和记录电子和反应。

这就是`Procfile`派上用场的地方。在您的`Procfile`中，添加运行 React 应用程序和 Electron 应用程序的 npm 脚本:

```
react: npm run react-start
electron: npm run electron-start
```

而大部分的神奇都发生在`package.json`。

```
{"main": "main.js","scripts": {"start": "nf start -p 3000","electron-start": "node electron-wait-react","react-start": "cd ./app/views/react && react-scripts start"},"devDependencies": {"electron": "^6.0.9","electron-builder": "^21.2.0","foreman": "^3.0.1"},"directories": {"buildResources": "assets","output": "release"}}
```

当您运行`npm start`时，它会在端口 3000 执行`Procfile`中的两个命令。

你可能想知道，`electron-wait-react.js`是做什么的？

与 Christian 的版本类似，我做了一个小调整，以记录来自电子应用程序的消息。

在启动电子应用程序之前，它会等待 React 的 webpack-dev-server 启动。

```
const net = require('net');const port = process.env.PORT ? (process.env.PORT - 100) : 3000;process.env.ELECTRON_START_URL = `http://localhost:${port}`;const client = new net.Socket();let startedElectron = false;const tryConnection = () => client.connect({port: port}, () => { client.end(); if(!startedElectron) { console.log('starting electron'); startedElectron = true; const { spawn } = require('child_process'); const ls = spawn('npm run electron', [], { shell: true }); ls.stdout.on('data', (data) => { console.log(`${data}`); }); ls.stderr.on('data', (data) => { console.error(`err: ${data}`);
 }); ls.on('close', (code) => { console.log(`child process exited with code ${code}`); });}});tryConnection();client.on('error', (error) => { setTimeout(tryConnection, 1000);});
```

# 在 React 中设置主页`package.json`

如果您构建应用程序时看到一个空白页面，那是因为 React 找不到它的 js 和 CSS 文件。在你的 React app 的`package.json`中，一定要添加:

```
"homepage": "./"
```

# 使用节点集成

最后，您已经在电子中运行了 React，但是如果您想在 React 中使用电子模块，该怎么办呢？

创建窗口后，您可以在`webPreferences`中添加`nodeIntegration`。

```
mainWindow = new BrowserWindow({ width: 1024, height: 728, webPreferences: { nodeIntegration: true, preload: path.join(__dirname, 'app/views/js/preload.js') }});
```

在 React 应用程序中，您可以这样要求:

```
const { ipcRenderer } = window.require("electron");
```

# 其他潜在问题

React app 禁止访问 src 以外的文件。我在网上找到的大多数解决方案都涉及到弹出 React 应用程序并在 webpack 中禁用该规则。

但是在我们的用例中，我们只需要在 React 中显示图像，所以我们将图像存储为 base64 格式来绕过这个问题。

# 结论

感谢那些创建了电子教程和帖子的人。你的贡献让学习电子变得不那么令人畏惧。