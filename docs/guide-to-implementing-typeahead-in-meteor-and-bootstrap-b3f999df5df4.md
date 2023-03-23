# 在 Meteor 和 Bootstrap 中实现 Typeahead 的指南

> 原文：<https://betterprogramming.pub/guide-to-implementing-typeahead-in-meteor-and-bootstrap-b3f999df5df4>

## 我将解释如何在 Meteor/Bootstrap 环境中简单地实现 typeahead

首先，在这里下载 [Bootstrap 3 Typeahead 库](https://github.com/bassjobsen/Bootstrap-3-Typeahead)。如果您阅读了他们的自述文件，您可能会想写这样的代码

```
<template name="myTemplate">
    <input data-provide="typeahead" id="blah" type="text" />
</template>
```

和

```
Meteor.startup(function () {
  $('#blah').typeahead(getTypeaheadData());
});
```

这是不好的做法。如果您有任何代码可以两次创建`myTemplate`模板，它将不再工作。同一个模板的两个渲染不指向同一个元素。例如:

```
{{#if flag}}
  {{>myTemplate}}
{{else}}
  {{>myTemplate}}
{{/if}}
```

在这个例子中，启动功能只应用于一个`{{>myTemplate}}`渲染。如果`flag`被翻转，另一个`myTemplate`将没有提前输入功能。

你可能会尝试用一个`Template.myTemplate.rendered`函数来破解它，但那太麻烦了。相反，本着反应式编程的精神，您应该使用数据属性。

**正确的方法**是用 JSON 形式的 typeahead 数据创建一个助手。

```
Template.myHelper.helper({
  typeahead : function(){
    return JSON.stringify(Session.get("typeahead"));
  }
});
```

不要忘记`Session.set("typeahead",getTypeaheadData());`你可以把它放在你的启动函数中，但是如果运行`getTypeaheadData()`需要时间，最好也把:`Session.setDefault("typeahead",[]);`

现在，在您的模板中，您只需执行以下操作:

```
<template name="myTemplate">
    <input data-source="{{typeahead}}" data-provide="typeahead" id="blah" type="text" />
</template>
```

恭喜你，你现在有了一个反应式提前输入字段！