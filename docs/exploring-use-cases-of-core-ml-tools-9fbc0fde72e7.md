# 探索核心 ML 工具的用例

> 原文：<https://betterprogramming.pub/exploring-use-cases-of-core-ml-tools-9fbc0fde72e7>

## 评估、转换、可更新模型等等

![](img/46de4c7ea1bd62e3a1524314f2c617a8.png)

照片由 [Elena Rouame](https://unsplash.com/@roum?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

苹果的 Core ML 是一个强大的机器学习框架，具有易于使用的拖放界面。最新的迭代 [Core ML 3](https://heartbeat.comet.ml/whats-new-in-core-ml-3-d108d352e50a) 引入了许多新的层，并产生了[可更新的模型](https://medium.com/better-programming/how-to-create-updatable-models-using-core-ml-3-cc7decd517d5)。

随着这么多特性的发布，有一件事经常被忽略，那就是你可以用 Xcode 之外的模型做的事情。甚至在应用程序中部署核心 ML 模型之前，就有很多功能可以进行微调、定制和模型测试。

使用`coremltools` Python 包，您不仅可以转换模型，还可以使用实用程序类来调试图层、修改要素形状、设置超参数，甚至运行预测。

随着`coremltools 3.0`的出现，与 Core ML 2 相比，已经增加了大约 100 层。此外，现在可以将图层标记为可更新，以便进行设备上训练。

在接下来的章节中，我们将浏览不同的用例以及场景，在这些场景中`coremltools`对我们和我们的 ML 模型很方便。

在我们开始之前，继续使用以下命令安装`coremltools 3.0`:

```
pip install -U coremltools
```

# 预处理和模型转换

Core ML Tools 提供了转换器，可以将流行的机器学习库(如 Keras、Caffe、scikit-learn、LIBSVM 和 XGBoost)中的模型转换为 Core ML。

此外， [onnx-coreml](https://github.com/onnx/onnx-coreml) 和 [tf-coreml](https://github.com/tf-coreml/tf-coreml) 神经网络转换器建立在`coremltools`之上。

`tf-coreml`需要在`convert`功能中设置最小展开目标标志。这是因为 iOS 13 部署模型的底层实现与旧版本不同。

对于 iOS 13 及以上版本，需要在参数`input_name_shape_dict`中传递节点名称——而不是张量形状。

以下代码片段展示了如何将 Keras 模型转换为核心 ML:

使用上面显示的 Python 脚本，我们可以做各种事情，比如更改输入和输出名称、预处理等。

`image_input_names`参数表明输入类型可以被认为是一个图像。否则，默认情况下，输入由 Core ML 创建为多维数组。

`image_scale`用于指定输入缩放的数值。像素乘以这个数字。仅当设置了`image_input_names`时，此参数才适用。

`red_bias`、`green_bias`、`blue_bias`和`gray_bias`:这些值将 R、G、B 或灰度颜色值加到像素上。

对于分类器模型，我们可以传递一个参数`class_labels`，该参数带有一个包含类标签的数组或文件，这些标签被映射到神经网络的输出索引。

# 修改输入和输出类型

如果您手头有一个核心 ML 模型，但是没有想要的输入约束，`coremltools`是一个方便的工具。它不仅允许调整输入和输出的大小，还允许您更改类型。例如，如果您需要将输入类型`MLMultiArray`转换为具有特定颜色空间的图像类型，下面这段代码将为您完成这项工作:

```
import coremltools
import coremltools.proto.FeatureTypes_pb2 as ft 

spec = coremltools.utils.load_spec("OldModel.mlmodel")

input = spec.description.input[0]
input.type.imageType.colorSpace = ft.ImageFeatureType.RGB
input.type.imageType.height = 224 
input.type.imageType.width = 224

coremltools.utils.save_spec(spec, "NewModel.mlmodel")
```

> 使用`coremltools`中的`flexible_shape_utils`，我们可以进一步指定形状范围，甚至设置多个输入和输出形状。

# 量化

应用程序的大小很重要，某些核心 ML 模型会占用大量的存储空间。量化有助于减小模型大小，而不会显著降低精度。当减小模型的尺寸时，我们降低了权重的精度。

下面的代码展示了一个量化核心 ML 模型的例子。

```
from coremltools.models.neural_network.quantization_utils import quantize_weights

model = coremltools.models.MLModel('model.mlmodel')
quantized_model = quantize_weights(model, nbits=8, quantization_mode="linear")
```

目前支持的一些量化模式有`linear`、`kmeans`、`linear_symmetric`和`linear_lut`。

目前，Caffe 和 Keras 转换器支持全精度和半精度量化。这可以在转换器功能的`model_precision`参数中设置。默认为`float32`。

# 修改图层

核心 ML 工具允许我们检查、添加、删除或修改层。对于`coremltools`无法转换的图层，我们可以通过在 convert 函数中将参数`add_custom_layers`设置为`true`来设置一个占位符图层:

```
coreml_model = keras_converter.convert(keras_model, add_custom_layers=True)
```

此外，我们可以通过调用神经网络构建器实例上的`inspect_layers`来检查许多层。

```
spec = coremltools.utils.load_spec(MyModel.mlmodel) builder=coremltools.models.neural_network.NeuralNetworkBuilder(spec=spec)
builder.inspect_layers(last=2)
builder.inspect_input_features()
```

以下代码显示了在构建器规范中添加或删除图层的示例:

```
new_layer = neuralNetworkClassifier.layers.add()
new_layer.name = 'my_new_layer'//delete
del nn.layers[-1]
```

> 为了删除一系列层，您可以使用`layers[a:b]`

# 允许设备上的模型训练

设备上模型训练是 Core ML 3 最大的进步之一。它允许我们从设备本身个性化模型，而不必在服务器端重新训练。为了允许在设备上更新 ML 模型，我们需要:

*   将某些图层标记为可更新
*   设置损失函数和超参数
*   在构建器规格中添加培训输入规格。

在另一个场景中，如果您希望直接构建可更新的核心 ML 模型，而不是在以后修改它们，那么您可以在模型转换期间将`respect_trainable=True`参数传递给`coremltools.converters.keras.convert()`。

目前，只有神经网络和 KNN 模型可以使用`coremltools`进行更新。

```
builder.make_updatable(['layer_name_1', 'layer_name_2']) model_spec.description.trainingInput[0].shortDescription = 'Image for training and updating the model'
model_spec.description.trainingInput[1].shortDescription = 'Set the class label here'
```

此外，我们需要为可更新模型设置超参数，如 epochs、[学习率](https://heartbeat.comet.ml/introduction-to-learning-rates-in-machine-learning-6ed685c16506)和训练样本的批量，如下所示:

```
builder.set_sgd_optimizer(SgdParams(lr=0.01, batch=12))
builder.set_epochs(20)
builder.set_categorical_cross_entropy_loss(name='lossLayer', input='output')
```

模型内部的损失函数就像层一样。目前，`binary_entropy`和`categorical_cross_entropy`(针对多个标签类别)是少数几个受支持的损失函数。

最后，您需要在模型规范的最低规范版本旁边设置`isUpdatable`标志(Core ML 有 v4)，如下所示:

```
model_spec.isUpdatable = True
model_spec.specificationVersion = coremltools._MINIMUM_UPDATABLE_SPEC_VERSION
```

> 注意:除了 SGD，你也可以使用 Adam 优化器。

# 运行预测

从`coremltools` Python 脚本本身运行预测很容易。首先，您需要使用`coremltools`加载您的`.mlmodel`。

以下代码加载图像分类`.mlmodel`并对其运行预测:

```
import coremltools
import PIL.Imagedef load_image(path, resize_to=None):
    # resize_to: (Width, Height)
    img = PIL.Image.open(path)
    if resize_to is not None:
        img = img.resize(resize_to, PIL.Image.ANTIALIAS)
    return imgmodel = coremltools.models.MLModel('catDogModel.mlmodel')
img = load_image('./test-image.jpeg', resize_to=(150, 150))result = model.predict({'image': img})
print(result)
```

在上面的代码中，我们在`.mlmodel`类构造函数中传递模型名。或者，您可以通过在构造函数中设置布尔参数`useCPUOnly=True`来限制模型仅在 CPU 上运行。

接下来，我们使用`PIL`(枕形封装)将图像加载到我们的[定制的 Core ML 模型](https://github.com/anupamchugh/iowncode/blob/master/iOSCoreMLOnDeviceTraining/iOSCoreMLTrainModelOnDevice/iOSCoreMLTrainModelOnDevice/CatDogUpdatable.mlmodel)中，并调整其大小以适应输入约束(该模型为 150x150)。

我们使用下面的图片运行了上面的 Python 脚本，并获得了作为`cat`的输出(确切的输出在图片标题中)。

![](img/3a31d3ab81217d04c04aeb47b282f18b.png)

{u'classLabel': u'Cat '，u'output': {u'Dog': 0.0，u'Cat': 1.0}}

除了像上面那样测试你的模型的准确性，你还可以调试你的模型层并打印出规格、概要，或者你可以通过调用`.mlmodel`上的`visualize_spec()`来可视化你的模型。

# 结论

Core ML 3 引入了许多新的控制流层，可以使用神经网络生成器以编程方式构建不同的神经网络 Core ML 模型。这里有一个[的例子](https://github.com/apple/coremltools/blob/master/examples/neural_network_inference/Neural_network_control_flow_power_iteration.ipynb)来自文档。

此外，`coremltools`的新版本也带来了对 TensorFlow 2.0 转换器的支持。接下来，您可以尝试向模型添加激活层，量化和评估模型，然后再将它们部署到您的应用程序中。

这就结束了。我希望你喜欢阅读。