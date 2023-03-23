# 如何在 React 中动态加载第三方脚本

> 原文：<https://betterprogramming.pub/loading-third-party-scripts-dynamically-in-reactjs-458c41a7013d>

## 仅在需要时加载您需要的内容

![](img/eea5875227fcd8b2968da39a91195b4d.png)

照片由[戴克](https://unsplash.com/@elmeng?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/collections/8975469/focu%24?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在开发 React 应用程序时，我们有时不得不依赖第三方库。这些库大多可以通过 [npm](https://www.npmjs.com/) 包获得，但有时需要在我们的 HTML 内容的`<head></head>`或`<body></body>`中放置一个`script`标签。

对于要在整个站点中全局使用的库，将它们放在我们的主`<head></head>`中是绝对没问题的，但是对于只在一个或几个地方使用的库，将脚本放在我们的应用程序的`<head></head>`中会增加不必要的页面加载，从而导致更长的加载时间。

# 那我们该怎么办？

许多解决方案中的一个可以是创建一个函数，我们可以在需要第三方库的页面上调用它，并动态地创建`<script></script>`标签并将其注入到应用程序的`<body></body>`中。

假设我们想通过一个脚本集成 Google Maps API，并在我们的一个页面上显示地图。让我们看一个例子:

```
const loadGMaps = (callback) => { const existingScript = document.getElementById('googleMaps'); if (!existingScript) {
    const script = document.createElement('script');
    script.src = '[https://maps.googleapis.com/maps/api/js](https://maps.googleapis.com/maps/api/js?key=)'; script.id = 'googleMaps';
    document.body.appendChild(script); script.onload = () => { 
      if (callback) callback();
    };
  } if (existingScript && callback) callback();
};export default loadGMaps;
```

让我们试着理解我们的函数在做什么。我们首先通过寻找一个`<script></script>`标签，并将其`id`设置为`googleMaps`，来检测我们是否已经加载了谷歌地图。如果我们这样做了，我们就停止——因为没有必要加载库两次。

如果找不到一个`existingScript`；我们动态地创建脚本。我们首先在内存中创建一个空的`<script></script>`标签作为`script`，然后为它的`src`和`id`分配必要的属性，以便稍后识别脚本。最后，我们将脚本添加到我们的`<body></body>`标签中来实际加载它。

最后，我们调用`callback()`，如果它存在的话，无论是在我们第一次加载脚本时，还是在脚本已经存在的后续调用中。

# 使用

```
import React, { useEffect, useState } from 'react';
import GoogleMap from '../GoogleMap';
import loadGMaps from '../scripts/loadGMaps.js';const Maps = (props) => {
  const [loaded, setLoaded] = useState(false); useEffect(() => {
    loadGMaps(() => {
      setLoaded(true);
    });
  }); return (
    <div className="maps-component">
      {loaded ? <GoogleMap /> : ''}
    </div>
  );
}
```

当我们的组件开始挂载时，我们将调用我们的库来动态创建并将`<script></script>`注入到`<body></body>`中。一旦它被加载并调用了`script.onload`函数，我们在这里传递的回调将触发并更新我们组件的`setLoaded`值为`true`。

就是这样！不执行是不是太简单了？