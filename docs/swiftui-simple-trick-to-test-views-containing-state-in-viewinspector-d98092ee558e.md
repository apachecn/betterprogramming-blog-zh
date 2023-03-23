# 如何在 ViewInspector 中测试包含@State 的 SwiftUI 视图

> 原文：<https://betterprogramming.pub/swiftui-simple-trick-to-test-views-containing-state-in-viewinspector-d98092ee558e>

## 一个简单而强大的 SwiftUI 技巧

![](img/16f2c31ad571f6939023b45c24c98e1c.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

为了很好的开始 SwiftUI 测试，使用`ViewInspector`框架，你可以阅读[这个](https://www.raywenderlich.com/30227776-swiftui-testing-with-viewinspector-for-ios#toc-anchor-013)或者[这个](/how-to-test-swiftui-views-smartly-6c6b13f9edb1)。

在第一篇教程中(以及在 ViewInspector [GitHub](https://github.com/nalexn/ViewInspector/blob/master/guide.md#views-using-state-environment-or-environmentobject) 页面上)描述的项目之一是`[@State](http://twitter.com/State)`在您想要测试的视图中的用法。

基本上，如果你的观点是这样的:

```
struct ContentView: View {
   [@State](http://twitter.com/State) var numClicks:Int = 0

   var body: some View {
      VStack{
         Button("Click me"){
            numClicks += 1
         }.id("Button1")
         Text("\(numClicks)")
           .id("Text1")
           .padding()

      }
   }
}
```

实际上无法测试点击`numClicks`上的按钮的动作。
可接受的解决方法是向视图添加一些代码，基本上转换如下:

```
struct ContentView: View {
   [@State](http://twitter.com/State) var numClicks:Int = 0
   internal let inspection = Inspection<Self>()

   var body: some View {
      VStack{
         Button("Click me"){
            numClicks += 1
         }.id("Button1")
         Text("\(numClicks)")
           .id("Text1")
           .padding()

       }.onReceive(inspection.notice) { 
            self.inspection.visit(self, $0) }
    }
}
```

检验地点:

```
internal final class Inspection<V> {
   let notice = PassthroughSubject<UInt, Never>()
   var callbacks: [UInt: (V) -> Void] = [:] func visit(_ view: V, _ line: UInt) {
      if let callback = callbacks.removeValue(forKey: line) {
         callback(view)
      }
   }
}**extension** Inspection: InspectionEmissary {}
```

正如您所看到的,`ContentView`获得了一个新的检查属性，当数据被发布到注意到的发布者时，它的主体的接收方被指示运行检查属性的访问方法。

这就解决了这个问题，一个功能测试用例可能是这样的:

```
func testContentView() throws{
   let sut = ContentView()
    _ = sut.inspection.inspect { view in
       let button = try view.find(viewWithId: “Button1”).button()
       try button.tap()
       XCTAssertEqual(try view.actualView().numClicks, 1)
       let text = try view.find(viewWithId: “Text1”).text()
       let value = try text.string()
       XCTAssertEqual(value, “1”)
    }
 }
```

然而，在生产代码中添加测试“特性”看起来有点难看。它可以使一个复杂的视图变得更加复杂，并且它有许多冗余的代码需要被复制/粘贴到你想要测试的每个视图中。

因此，我试图找到一些更干净的东西，这会给测试代码增加一些开销，但不会影响实际的视图实现。

首先，我实现了一个简单的包装器视图，它可以添加`ViewInspector`所需的检查功能:

```
public let TEST_WRAPPED_ID: String = “wrapped”struct TestWrapperView<Wrapped: View> : View{
   internal let inspection = Inspection<Self>()
   var wrapped: Wrapped

   init( wrapped: Wrapped ){
       self.wrapped = wrapped
   }

   var body: some View {
      wrapped
        .id(TEST_WRAPPED_ID)
        .onReceive(inspection.notice) { 
           self.inspection.visit(self, $0) 
        }
    }
}extension TestWrapperView: Inspectable{}
```

有了这个包装器视图，就可以使用最初的`ContentView`实现来测试视图:

```
func testContentView() throws{
   let sut = TestWrapperView(wrapped: ContentView())
   _ = sut.inspection.inspect { view in
       let wrapped = try view.find(viewWithId: TEST_WRAPPED_ID)
       let button = try wrapped.find(viewWithId: “Button1”).button()
       try button.tap()
       let numClicks = try wrapped
                         .view(ContentView.self)
                         .actualView()
                         .numClicks
       XCTAssertEqual(numClicks, 1)
       let text = try wrapped.find(viewWithId: “Text1”).text()
       let value = try text.string()
       XCTAssertEqual(value, “1”)
    }
 }
```

我希望你喜欢这个小技巧，它让 SwiftUI 视图的测试变得更加容易。