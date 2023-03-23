# 用 JavaScript 从项目中删除 jQuery:第 1 部分

> 原文：<https://betterprogramming.pub/removing-jquery-from-your-project-with-javascript-part-1-76eefcc98cff>

## 学习用 JavaScript 替换 jQuery DOM 操作

![](img/8f2a4bace15ee828ebf62a6195d91bea.png)

照片由[萨加尔·帕蒂尔](https://unsplash.com/@sagarp7?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/baloons?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

删除 jQuery 的原因是:

*   获得自然的性能
*   了解 jQuery 函数内部发生了什么，这样我们就可以决定是使用 jQuery 还是 JavaScript

本系列的第 1 部分只关注在不使用 jQuery 的情况下获取元素。我为更常用的 jQuery 函数添加了可选的 JavaScript 方法。

# 1 .使用选择器选择元素

```
// jQuery
$('selector');

// JavaScript
document.querySelectorAll('selector'); //returns NodeList
```

如果我们想获得第一个元素——它匹配选择器——那么使用:

```
document.querySelector('selector');
```

# 2.通过类名获取元素

为了通过类名获取元素，我们可以使用带有前缀`.`的`getElementsByClassName`或`querySelector`

```
// jQuery
**$('.className');**

// JavaScript
**document.querySelectorAll('.className');**

// or
**document.getElementsByClassName('className');**
```

# 3.按 ID 获取元素

要通过 ID 获取元素，我们可以使用前缀为`#`的`getElementById`或`querySelector`。

```
// jQuery
$('#id');

// JavaScript
document.querySelector('#id');

// or
document.getElementById('id');//or
window['id']
```

# 4.通过标记名获取元素

为了通过标签名获取元素，我们可以使用`getElementsByTagName`，或者使用`querySelectorAll`函数**。**

```
//jQuery
$('tagName'); // Example : $('li');//JavaScript 
**document.getElementsByTagName('tagName');** // To get elements from another element using tag name **parentElement.querySelectorAll('li');**
```

# 5.按属性获取元素

```
// jQuery
**$('a[target=_blank]');**

// JavaScript
**document.querySelectorAll('a[target=_blank]');**
```

# 6.获取前一个元素

使用`previousElementSibling` 获取前一个元素。

```
// jQuery
$el.prev();

// JavaScript
**el.previousElementSibling;**
```

# 7.获取下一个元素

使用`nextElementSibling` 获取下一个元素。

```
// jQuery
$el.next();

// JavaScript
**el.nextElementSibling;**
```

# 8.获取所有兄弟姐妹

要获取所有同级:

*   获取父项
*   获取父代的子代
*   创建一个兄弟数组
*   如果子元素不等于当前元素，则将子元素推送到同级元素
*   返回兄弟姐妹

```
function getSiblings(el) {
  let parentNode = el.parentNode,
      children = parentNode.children,
      totalChildren = children.length,
      siblings = []; for(let i = 0; i < totalChildren; i++) {
     let currentChild = children[i];
     if(el != currentChild) {
        siblings.push(currentChild);
     }
   }

   return siblings;}
```

上面的代码也可以写成这样:

```
Array.from( **el.parentNode.children** ).**filter((child) =>
  child !== el
);**
```

# 9.获取所有以前的同级

我们可以用`previousElementSibling`来获取当前元素的前一个元素。然后我们可以循环直到第一个元素。

```
// jQuery
$el.prevAll();

// JavaScriptfunction getPreviousSiblings(elem) {
  var siblings = []; while ( **elem = elem.previousSibling** ) {     
      if ( elem.nodeType === 3) { // ignore texts
         continue; 
      }
      siblings**.push(elem);** } return siblings;
}
```

# 10.获取所有下一个兄弟姐妹

我们可以使用`nextElementSibling`来获取当前元素的下一个元素。然后我们可以循环直到最后一个元素。

```
// jQuery
$el.prevAll();

// JavaScriptfunction getNextSiblings(elem) {
  var siblings = []; while ( **elem = elem.**nextElementSibling) {     
      if ( elem.nodeType === 3) { // ignore texts
         continue; 
      }
      siblings**.push(elem);** } return siblings;
}
```

# 11.查找与选择器最匹配的元素

`el.matches`检查元素是否是选择器。

```
// jQuery
**$el.closest(selector);**

// Native - Only latest, NO IE
**el.closest(selector);**

// Native - IE10+
function closest(el, selector) {
  const matchesSelector = el.matches || 
                          el.webkitMatchesSelector ||    
                          el.mozMatchesSelector || 
                          el.msMatchesSelector;

  while (**el**) {
    if (**matchesSelector.call(el, selector)** ) {
      return el;
    } else {
 **el = el.parentElement;**
    }
  }
  return null;
}
```

# 12.获取元素的父元素

为了获得当前元素的父元素，我们可以使用`currentElement`上的`parentElement`属性。

```
// jQuery
$(el).parent();//JavaScript
**el.parentElement**
```

# 13.获取元素的子元素

为了获得当前元素的子元素，我们可以在`currentElement`上使用`children`属性。

```
//jQuery$(el).children();//JavaScript**el.children**
```

# 14.去找`the Body`

要获得`body`元素，我们可以使用:

```
// jQuery
$('body');

// JavaScript
**document.body;**
```

# 15.IFrame 文档

要获取 IFrame 的文档:

```
// jQuery
$iframe.contents();

// Native
iframe.contentDocument;
```

感谢阅读。希望你喜欢这个。