# 在 10 个 Bash 命令中运行 TensorRT

> 原文：<https://betterprogramming.pub/run-tensorrt-in-10-bash-commands-b89a207d61c5>

## TensorRT 引擎推理的接口，以及正在使用的 YOLOv4 引擎的示例

![](img/abe2d67b28339bb7820be8fc78216d96.png)

由[劳拉塔拉](https://pixabay.com/users/lauratara-6167959/)在 [pixabay](https://pixabay.com/) 拍摄的照片

我认为优化推理是人工智能开发中最重要的部分之一。让我们想象一个典型的工作流程，并假设我们有一个工程师，他使用 PyTorch 框架训练了一个神经网络来检测图像中的文本。

准备好部署后，工程师在 AWS 上启动了一个虚拟机，他选择的实例是最便宜的深度学习 EC2 实例，即著名的 [g4dn.xlarge](https://aws.amazon.com/ec2/instance-types/g4/) ，带有一个负担得起的 Nvidia Tesla T4 图形处理单元。为了利用硬件，工程师使用了 CUDA 框架，PyTorch 支持开箱即用。

这很好，推理比在 CPU 上快得多，但仍然有 320 个图灵张量核心没有得到正确的使用！

张量核不仅能够最大化您可以从 EC2 实例中获得的价值，而且还可以带来难以置信的性能提升(速度提高了 10 到 30 倍，这不是一个错误)。

让我们把例子举得更远一点，并假设给定任务的算法需要的不是一个，也不是两个模型，而是一个架构，比如说五个不同的网络？

在这种情况下，网络通常会有不同的格式，并且来自不同的框架。将几个框架作为依赖项会很快变得很麻烦。不仅如此，当涉及到 GPU 内存分配时，框架之间还会经常发生冲突。

因此，我发现将每个模型导出为 ONNX 格式是非常有益的一步，ONNX 格式是开放神经网络交换的缩写，最初由微软的人开发。

知道网络将接收的确切数据形状和输入类型，并准备好权重，工程师可以使用 PyTorch 内置模块( [torch.onnx](https://pytorch.org/docs/stable/onnx.html) )将其模型直接导出到 ONNX 中。不再需要模型基类，因为`.onnx`格式包括模型图和权重，torch 不再是依赖关系。

然后，ONNX 导出的模型可以用`onnxruntime`模块进行推理。我在回购协议中提供了一个例子，这里是。

# 要求

*   `amd64/linux`机器架构
*   带张量内核的 Nvidia GPU
*   `nvidia-docker`
*   `.onnx`格式的模型(或`.caffemodel`)

# 设置

首先，可以派生/克隆包含代码的 repo。

```
[https://github.com/piotrostr/infer-trt](https://github.com/piotrostr/infer-trt)
```

该示例使用了`opencv`库，该库可以使用可以在[官方文档](https://docs.opencv.org/4.x/d7/d9f/tutorial_linux_install.html)中找到的官方指令来构建。

即使使用`8-core-cpu`构建它也需要相当长的时间，所以我建议安装来自 [conda-forge](https://anaconda.org/conda-forge/opencv) 的二进制文件。

```
conda install -y -c conda-forge/label/gcc7 opencv
```

TensorRT 设置相当复杂，因此建议使用预构建的 nvcr.io 容器。

```
docker pull nvcr.io/nvidia/tensorrt:22.03-py3
```

要用 GPU 运行容器，还需要 nvidia-docker，对于基于 debian 的发行版来说，安装非常快。

要运行容器，需要包含一些选项，为了不每次都面临长时间的 docker 运行，可以方便地将这些选项放入`docker-compose.yml`。

# 使用

通常情况下，`yolo.onnx`重量可能是训练的结果。如前所述，如果使用 PyTorch 或 TF2，很容易导出 ONNX。

对于这个例子，预先训练的权重可以从 ONNX repo 中卷曲或获取。

```
curl \
  -o yolo.onnx [https://github.com/onnx/models/blob/main/vision/object_detection_segmentation/yolov3/model/yolov3-10.onnx](https://github.com/onnx/models/blob/main/vision/object_detection_segmentation/yolov3/model/yolov3-10.onnx)
```

为了获得给定模型的 TensorRT 引擎，可使用`trtexec`工具从`onnx`重量文件中导出。

该工具的可执行文件在`nvcr.io`容器的 bin 中。

```
./trtexec \
  --onnx=./yolo.onnx \
  --best \
  --workspace=1024 \
  --saveEngine=./yolo.trt \
  --optShapes=input:1x3x416x416
```

`trt_model.py`包含一个用于继承的基类。一旦预处理和后处理方法被覆盖以匹配每个给定模型所需的步骤，就可以进行推理了。凭借其高级 API:

```
#/usr/bin/env python
import cv2
import pycuda.autoinit
import tensorrt as trtfrom yolo import YOLOyolo = YOLO(trt.Logger())
img = cv2.imread(‘some_img.png’)
labels, confidences, bboxes = yolo(img)
```

# 许可证

麻省理工学院[https://github.com/piotrostr/infer-trt](https://github.com/piotrostr/infer-trt)

*原载于*【http://github.com】**。**