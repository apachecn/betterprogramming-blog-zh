# 设计开发人员友好的 React 组件

> 原文：<https://betterprogramming.pub/designing-developer-friendly-react-components-8638e017d728>

## 设计开发人员友好的 React 组件的原则和实践

![](img/4da345eaff802ef373e5f34e561ce520.png)

由[萨法尔·萨法罗夫](https://unsplash.com/@codestorm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

设计一个可重用的 React 组件绝对是一个挑战，因为我们经常在一个团队中工作，我们组件的用户不仅仅是我们，还有其他开发人员。

此外，如果不妥善处理，糟糕的抽象有一天肯定会反咬我们一口。我喜欢在这里使用 word *design* 因为设计过程是在我们进行实际编码部分之前发生的思考和思考阶段。

理想情况下，我们希望在实际编码之前花时间进行设计。

本文将讨论我们可以应用于设计可维护和开发人员友好的 React 组件的常见实践和原则。

# 上下文无关组件

设计一个可重用的组件意味着我们需要确保这个组件尽可能的与上下文无关。

比如选择`Button`而不是`ReportPageConfirmationButton`。`Card`而不是`UserCard`。`Avatar`而不是`ProfileAvatar`*等等。这样，我们可以在项目中扩展组件的使用。此外，当组件在不同的上下文中使用时，它可以避免混淆。*

*例如，在`SettingPage` 中使用`ReportPageConfirmationButton` 可能在功能和 UI 方面是正确的，但在语义上是不正确的。*

*另一个优点是抽象出与特定用例相关的实现。看看下面的`Button`组件。*

```
*function Button ({ children }) {
 return <button data-testid="login-button">{children}<button/>
}*
```

*即使`data-testid` 属性只在登录按钮中使用，这个`Button`组件也可以在任何地方使用，并且是正确的 UI 方式。*

*但是，不知何故，它与登录按钮的实现联系在一起。所以，我们提取`data-testid`属性作为道具。*

```
*function Button ({ children, dataTestId }) {
 return <button data-testid={dataTestId}>{children}<button/>
}// Usage
<Button dataTestId="login-button">Login</Button>*
```

# *简单和语义的组件道具*

## *使用 JSX 的默认值和 JavaScript 强制*

*考虑下面的`Button`组件。*

```
*import React from 'react';
import Loader from '../Loader';function Button ({  loading }) {
 return <button>{ loading ? <Loader /> : 'Click me!'}<button/>
}*
```

*当你提供没有值的道具时，它将默认为`true`。这两个 JSX 表达式是等价的。*

```
*<Button loading={true} /><Button loading />*
```

*此外，我们可以使用 JavaScript 强制性质。这两个 JSX 表达式也是等价的。*

```
*<Button loading={false}/><Button />*
```

*这些可能看起来很明显，但是如果您在 React 项目中有类型检查，这可能会有所帮助。*

```
*type TButtonProps = {
 loading?: Boolean
}// So if you do this the type checking won't complain
<Button />*
```

*同样，让我们考虑一下为什么我在这里使用名称`loading`，而不是`isLoading`。`isLoading`如果我们每次都要提供价值，道具是有意义的。*

```
*<Button isLoading={true} /><Button isLoading={false} />*
```

*但事实并非如此。我们利用了 JSX 和 JavaScript 强制的默认值，因此我们最终会得到:*

```
*<Button isLoading/>*
```

*这可能看起来模糊不清，因为没有明确说明按钮是否显示加载程序。*

```
*// This is much better
<Button loading />*
```

# *考虑组件用例*

*我发现一个有用的思维过程是基于组件的用例来设计组件道具。*

*假设我们与团队中的设计师进行了讨论，我们决定我们需要一个具有显示加载状态功能的`Button`。*

*总的来说，我们有两种选择。我们可以实现`loading`道具或者`notLoading`道具。*

```
*// WITH LOADING PROPS// Button in normal state
<Button />// Button in loading state
<Button loading/>// WITH NOTLOADING PROPS// Button in normal state
<Button notLoading={false} />// Button in loading state
<Button />*
```

*显然，这里的`Button`的大多数用例不会使用加载状态。这种情况下，最好是用`loading` 道具，而不是`notLoading` **。***

*否则，我们的代码库中将会有很多这样的实现:*

```
*<Button notLoading={false} />*
```

*为了一个思维练习，如果我们有`Button` 组件的相反行为呢？*

*在与你的设计师交谈后，你发现我们需要另一个按钮，这个按钮大部分时间都处于加载状态。我们姑且称这个组件为`LoadingButton`。*

*使用与`Button`组件相同的 API，我们将不得不在大多数时候这样做:*

```
*<LoadingButton loading />*
```

*因为我们确信大多数用例将显示加载状态，所以我们可以改变实现，以便当我们想要显示带有`Loading`状态的`LoadingButton`时:*

```
*// Displaying the loader
<LoadingButton />// Not displaying the loader
<LoadingButton notLoading />*
```

# *样式属性名称与 CSS 属性名称相同*

*有时，我们希望添加功能来修改组件的 CSS 属性。最好使用与 CSS 属性相同的属性名。*

*原因是让它尽可能直观，因为作为前端开发人员，我们经常使用 CSS。*

*假设我们想改进我们的`Button`组件，这样它就可以接收道具来使用十六进制修改它的颜色和文本颜色。*

```
*<Button color=”#000” textColor=”#fff” />*
```

*我们可以更新组件的道具名称，因此我们有这样的名称:*

```
*<Button backgroundColor=”#000” color=”#fff” />*
```

# *事件处理程序属性*

*我们想给按钮添加点击功能。*

```
*function onClickHandler = () => console.log('Clicked!')<Button onClickButton={onClickHandler}/>
// vs
<Button onClick={onClickHandler}/>*
```

*在这种情况下，使用`onClick`道具而不是`onClickButton` 会更好，因为最好让`eventHandler`道具名称尽可能接近事件名称本身，除非我们需要将事件与特定的上下文联系起来，或者我们有多个类似的事件。*

*让我们考虑一个有取消和确认按钮的模态组件。*

*我们可以在这里实现`onCancel`和`onConfirm`，或者`onClickCancelButton`和`onClickConfirmButton` **。***

*在这种情况下，我更喜欢`onCancel` 和`onConfirm` **，**，因为`onClick`部分附属于`Button`组件，它们在字符上更短，并且仍然不失从用户角度取消或确认的上下文。*

```
*// Modal Component
function Modal ({ children, onCancel, onConfirm }) {
 return (
  <div>
   {children}
   <Button onClick={onCancel}>Cancel</Button>
   <Button onClick={onConfirm}>Confirm</Button>
  </div>
 )
}// Usage
<Modal 
 onCancel={() => console.log('Cancelled!')} 
 onConfirm={() => console.log('Confirmed!')} 
>
 Are you sure you want to delete this item?
</Modal>*
```

# *尽可能地抽象化*

*例如，我们想要实现一个`MultiSelect` 组件，它基本上是一个由可搜索的复选框选项列表组成的组件。显示的选项只是与搜索相关的选项。*

*这里我们有两个选项，在组件内部实现搜索功能(选项 A)或者将搜索的责任转移到父组件(选项 B)。*

*为了简单起见，让我们假设我们已经实现了`Option`组件。*

```
*// Option A
function MultiSelect ({ options, onSelectOption, searchInputValue, onSearch }) {
 return (
  <div>
   <input value={searchInputValue} onChange={onSearch} placeholder="Search here" />
   <ul>
    {
     options.map((option) => <Option value={option.value} label={option.label} onClick={onSelectOption} />)}
   </ul>
  </div>
 )
}// Option A Usage
function ParentComponent () {
 const options = ['Cat', 'Dog', 'Mouse', 'Elephant'] const [ searchInputValue, setSearchInputValue ] = useState('');
 const onSearchHandler = ({ target }) => {
  setSearchInputValue(target.value)
 } const [ selectedOptions, setSelectedOptions ] = useState([])
 // Implement the filter
 const filteredOptions = options.filter((option) => option.includes(searchInputValue)) return (
  <MultiSelect 
   options={filteredOptions} 
   searchInputValue={searchInputValue}
   onSearch={setSearchInputValue}
   onSelectOptions={setSelectedOptions}
  >
 )
}*
```

*同时，考虑选项 B 的实现:*

```
*// Option B 
function MultiSelect ({ options, onSelectOption }) {
 const [ search, setSearch ] = useState('');
 onChangeSearchHandler = ({ target }) => {
  setSearch(target.value);
 }const filteredOptions = options.filter((option) => option.includes(search))return (
  <div>
   <input value={searchInputValue} onChange={onChangeSearchHandler} placeholder="Search here" />
   <ul>
    {
     filteredOptions.map((option) => <Option value={option.value} label={option.label} onClick={onSelectOption} />)}
   </ul>
  </div>
 )
}// Option B Usage
function ParentComponent () {
 const options = ['Cat', 'Dog', 'Mouse', 'Elephant']const [ selectedOptions, setSelectedOptions ] = useState([])return (
  <MultiSelect 
   options={options} 
   searchInputValue={searchInputValue}
   onSearch={setSearchInputValue}
   onSelectOptions={setSelectedOptions}
  >
 )
}*
```

*其中在`ParentComponent` 端会有较少的实现。*

*假设我们将在多个地方使用这个组件，我们需要在这些地方实现搜索功能。`ParentComponent` 不需要知道任何关于搜索功能的实现。*

# *抽象但可定制的组件*

*将实现从用户和定制能力中抽象出来听起来是矛盾的，但这是可以做到的。*

## *使用修饰符函数修改组件的内部功能*

*对用户隐藏实现和定制能力听起来是矛盾的。*

*让我们回到我们的`MultiSelect`组件，让我们添加另一个功能来使用自定义算法进行搜索。*

```
*function MultiSelect ({ options, onSelectOption, searchModifier }) {
 const [ search, setSearch ] = useState('');
 onChangeSearchHandler = ({ target }) => {
  setSearch(target.value);
 }const filteredOptions = options.filter((option) => searchModifier instanceOf Function ? searchModifier(option, search) : option.includes(search))return (
  <div>
   <input value={searchInputValue} onChange={onChangeSearchHandler} placeholder="Search here" />
   <ul>
    {
     filteredOptions.map((option) => <Option value={option.value} label={option.label} onClick={onSelectOption} />)}
   </ul>
  </div>
 )
}*
```

## *使用渲染道具模式的自定义渲染器*

*在让组件的用户完全控制如何显示组件的情况下，我们可以使用 render props 模式。考虑这个实现:*

```
*function MultiSelect ({ options, onSelectOption, searchModifier, children }) {
 const [ search, setSearch ] = useState('');
 onChangeSearchHandler = ({ target }) => {
  setSearch(target.value);
 } const filteredOptions = options.filter((option) => searchModifier instanceOf Function ? searchModifier(option, search) :     option.includes(search)) if (children instanceOf Function) {
  return children(filteredOptions, search, onChangeSearchHandler)
 }return (
  <div>
   <input value={searchInputValue} onChange={onChangeSearchHandler} placeholder="Search here" />
   <ul>
    {
     filteredOptions.map((option) => <Option value={option.value} label={option.label} onClick={onSelectOption} />)}
   </ul>
  </div>
 )
}// Usage
function ParentComponent() {
 return (
  <MultiSelect>
   {
    (options, searchValue, onChangeSearchHandler) => {
    return 
     <div>
      <input value={searchValue} onChange={onChangeSearchHandler}/>             
      <span>Here are the option list!</span>
      {
       options.map(option => <span>{option}</span)
      }
     </div> }
  </MultiSelect> )
}*
```

*如果我们的目标更具体，我们可以为`MultiSelect`组件*的某些部分实现一个特定的呈现器。*例如，我们可以传递`searchInputRenderer` ，这样用户就可以控制如何显示搜索输入。*

```
*function MultiSelect ({ options, onSelectOption, searchModifier, children, searchInputRenderer }) {
...return (
  <div>
   {
    searchInputRenderer instanceOf Function ? 
    searchInputRenderer(searchInputValue, onChangeSearchHandler):
    <input value={searchInputValue} onChange={setSearch} placeholder="Search here" />
   }
   <ul>
    {
     filteredOptions.map((option) => <Option value={option.value} label={option.label} onClick={onSelectOption} />)}
   </ul>
  </div>
 )}*
```

# *关闭*

*感谢阅读我的文章！请留下评论，分享你对设计一个合适的 React 组件的想法。*