# 在 Go 中实现“地图”

> 原文：<https://betterprogramming.pub/implementing-map-in-go-40c55d34dbb4>

## 使用 Go 和 Go 例程将一个数组通用映射到另一个数组

![](img/e80673d96598215e64857eb77b3ada6e.png)

图片由[https://www.flaticon.com/](https://www.flaticon.com/)提供

# `'Map'?`有什么好处

想象一个有 n 个条目的数组。您希望处理数组中的每个条目，但是每个过程需要三秒钟。你必须开始写代码。

```
arr := [...]interface{..., ..., ..., ...}
for i, entry := range arr {
    doSomething(i, entry)
}
```

现在`doSomething`是一名举重运动员。如果处理每个强制完成的条目需要大约三秒钟，那么处理*(t)*n 个数组所需的总时间为:

`t = N * 3s`

通过使用一个`Map`函数，我们可以尝试在一个`goroutine`中分别处理每个条目。

# 平行地图在 Go 中

想象一个叫做`ParallelMap`的函数。它需要两个参数:

*   任何种类的对象。姑且称之为`source`。
*   一个`Function`，它接受`source`的一个条目，并在处理该条目后返回一个新的值/类型。姑且称之为`transform`。

它返回:

*   另一个`array`对象是`function`转换`source`条目的结果。

```
*// ParallelMap an array of something into another thing using go routine*
*// Example:*
*//  Map([]int{1,2,3}, func(num int) int { return num+1 })*
*//  Results: []int{2,3,4}*
func ParallelMap(source interface{}, transform interface{}) (interface{}, error) {
    *// TODO:*
}
```

# 1.确保源的类型

```
sourceV := reflect.ValueOf(source)
kindOf := sourceV.Kind()
if kindOf != reflect.Slice && kindOf != reflect.Array {
    return nil, errors.New("Source value is not an array")
}
```

# 2.确保`Transform`不为零并且是一个函数

```
if transform == nil {
    return nil, errors.New("Transform function cannot be nil")
}tv := reflect.ValueOf(transform)
if tv.Kind() != reflect.Func {
    return nil, errors.New("Transform argument must be a function")
}
```

# 3.准备结果容器

这里我们遇到一个困难:我们想制作结果容器，但是我们不知道结果的数组入口类型是什么。

调用者知道结果类型，所以我们将`ParallelMap`函数签名改为:

```
func ParallelMap(source interface{}, transform interface{}, T reflect.Type) (interface{}, error) {
```

让我们添加一些验证并继续创建结果容器。

```
if T == nil {
    return nil, errors.New("Map result type cannot be nil")
}*// kinda equivalent to = make([]T, srcV.Len())*
result := reflect.MakeSlice(reflect.SliceOf(T), srcV.Len(), srcV.Cap())
```

# 4.形成循环

```
*// create a waitgroup with length = source array length*
*// we'll reduce the counter each time an entry finished processing*
wg := &sync.WaitGroup{}
wg.Add(srcV.Len())*// for each entry in source array*
for i := 0; i < srcV.Len(); i++ {
    *// one go routine for each entry*
    go func(idx int, entry reflect.Value) {
        *//Call the transformation and store the result value*
        tfResults := tv.Call([]reflect.Value{entry})*//Store the transformation result into array of result*
        resultEntry := result.Index(idx)
        if len(tfResults) > 0 {
            resultEntry.Set(tfResults[0])
        } else {
            resultEntry.Set(reflect.Zero(T))
        }*//this go routine is done*
        wg.Done()
    }(i, srcV.Index(i))
}
```

# 5.等待并返回

```
wg.Wait()
return result.Interface(), nil
```

# 6.将它们缝合在一起

```
*// Map Error Collection*
var (
    ErrMapSourceNotArray   = errors.New("Input value is not an array")
    ErrMapTransformNil     = errors.New("Transform function cannot be nil")
    ErrMapTransformNotFunc = errors.New("Transform argument must be a function")
    ErrMapResultTypeNil    = errors.New("Map result type cannot be nil")
)*// ParallelMap an array of something into another thing using go routine*
*// Example:*
*//  Map([]int{1,2,3}, func(num int) int { return num+1 }, reflect.Type(1))*
*//  Results: []int{2,3,4}*
func ParallelMap(source interface{}, transform interface{}, T reflect.Type) (interface{}, error) {
    srcV := reflect.ValueOf(source)
    kind := srcV.Kind()
    if kind != reflect.Slice && kind != reflect.Array {
        return nil, ErrMapSourceNotArray
    }if transform == nil {
        return nil, ErrMapTransformNil
    }tv := reflect.ValueOf(transform)
    if tv.Kind() != reflect.Func {
        return nil, ErrMapTransformNotFunc
    }if T == nil {
        return nil, ErrMapResultTypeNil
    }*// kinda equivalent to = make([]T, srcv.Len())*
    result := reflect.MakeSlice(reflect.SliceOf(T), srcV.Len(), srcV.Cap())*// create a waitgroup with length = source array length*
    *// we'll reduce the counter each time an entry finished processing*
    wg := &sync.WaitGroup{}
    wg.Add(srcV.Len())
    for i := 0; i < srcV.Len(); i++ {
        *// one go routine for each entry*
        go func(idx int, entry reflect.Value) {
            *//Call the transformation and store the result value*
            tfResults := tv.Call([]reflect.Value{entry})*//Store the transformation result into array of result*
            resultEntry := result.Index(idx)
            if len(tfResults) > 0 {
                resultEntry.Set(tfResults[0])
            } else {
                resultEntry.Set(reflect.Zero(T))
            }*//this go routine is done*
            wg.Done()
        }(i, srcV.Index(i))
    }wg.Wait()
    return result.Interface(), nil
}
```

# 通过单元测试确保“ParallelMap”行为

```
func TestParallelMap(t *testing.T) {
    type args struct {
        arr       interface{}
        transform interface{}
        t         reflect.Type
    }
    tests := []struct {
        name    string
        args    args
        want    interface{}
        wantErr bool
    }{
        {
            name:    "Argument is not an array",
            args:    args{arr: 1, transform: nil, t: nil},
            want:    nil,
            wantErr: true,
        },
        {
            name:    "Transform function is nil",
            args:    args{arr: []int{1, 2, 3}, transform: nil, t: nil},
            want:    nil,
            wantErr: true,
        },
        {
            name:    "Transform is not a function",
            args:    args{arr: []int{1, 2, 3}, transform: 1, t: nil},
            want:    nil,
            wantErr: true,
        },
        {
            name:    "T is not supplied",
            args:    args{arr: []int{1, 2, 3}, transform: 1, t: nil},
            want:    nil,
            wantErr: true,
        },
        {
            name: "Valid transform",
            args: args{arr: []int{1, 2, 3}, transform: func(num int) int {
                return num + 1
            }, t: reflect.TypeOf(1)},
            want:    []int{2, 3, 4},
            wantErr: false,
        },
        {
            name: "Valid transform",
            args: args{arr: []int{1, 2, 3}, transform: func(num int) string {
                return strconv.Itoa(num)
            }, t: reflect.TypeOf("")},
            want:    []string{"1", "2", "3"},
            wantErr: false,
        },
    }
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            got, err := ParallelMap(tt.args.arr, tt.args.transform, tt.args.t)
            if (err != nil) != tt.wantErr {
                t.Errorf("Map() error = %v, wantErr %v", err, tt.wantErr)
                return
            }
            if !reflect.DeepEqual(got, tt.want) {
                t.Errorf("Map() = %v, want %v", got, tt.want)
            }
        })
    }
}
```

测试结果表明:

```
Running tool: /usr/local/opt/go/libexec/bin/go test -timeout 30s github.com/bastianrob/arrayutil -run ^(TestParallelMap)$ok      github.com/bastianrob/arrayutil    0.005s
Success: Tests passed.
```

# 情况如何？

嗯，代码实际上仍然是一种命令式风格，包装在使用`goroutine`实现并发的`ParallelMap`函数中。(也许我们应该改名叫`ConcurrentMap`？)

但是这和这篇荒谬文章开头的假想情况相比如何呢？让我们来测试一下:

```
func BenchmarkParallelMap(b *testing.B) {
    source := [100]int{}
    for i := 0; i < len(source); i++ {
        source[i] = i + 1
    }
    transf := func(num int) int {
        *//fake, long running operations*
        time.Sleep(20 * time.Millisecond)
        return num + 1
    }b.ResetTimer()
    for n := 0; n < b.N; n++ {
        ParallelMap(source, transf, reflect.TypeOf(1))
    }
}func BenchmarkImperative(b *testing.B) {
    source := [100]int{}
    for i := 0; i < len(source); i++ {
        source[i] = i + 1
    }
    transf := func(num int) int {
        *//fake, long running operations*
        time.Sleep(20 * time.Millisecond)
        return num + 1
    }b.ResetTimer()
    for n := 0; n < b.N; n++ {
        for _, num := range source {
            transf(num)
        }
    }
}
```

`Benchmark`结果显示:

```
BenchmarkParallelMap            100      22908438 ns/op       13801 B/op         305 allocs/op
PASS
ok      github.com/bastianrob/arrayutil    2.321s
Success: Benchmarks passed.BenchmarkImperative               1      2251692199 ns/op      1240 B/op           7 allocs/op
PASS
ok      github.com/bastianrob/arrayutil    2.258s
Success: Benchmarks passed.
```

我们可以完成 100 个`ParallelMap`，而标准的命令式只能完成一个。

这些你都可以在我的 [GitHub](https://github.com/bastianrob/go-experiences/tree/master/parallelmap) 上找到。