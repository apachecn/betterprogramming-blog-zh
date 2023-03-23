# 编写定制的 Kubernetes 控制器和 Webhooks

> 原文：<https://betterprogramming.pub/writing-custom-kubernetes-controller-and-webhooks-141230820e9>

## 创建一个 Kubernetes API，控制器，验证 webhooks，并进行测试。

![](img/093cdf6d8b54c72b3f54e15d9aa2d8f2.png)

本杰明·戴维斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在[之前的](https://medium.com/@Karthik-K-N/unit-testing-in-go-language-using-mocks-3b873ce1348d)文章中，我们试图使用模拟来理解 go 语言中的单元测试。在本文中，我们将讨论以下主题

1.  创建 Kubernetes API
2.  创建 Kubernetes 控制器
3.  创建验证网页挂钩
4.  用类簇测试变化
5.  额外小费

在开始编写自己的控制器之前，我们应该对 Kubernetes 中的[控制器](https://kubernetes.io/docs/concepts/architecture/controller/)、 [webhooks](https://kubernetes.io/docs/reference/access-authn-authz/extensible-admission-controllers/) 有个基本的了解。

# 编写自定义 Kubernetes 控制器

让我们尝试开发一个控制器来添加两个数字，这听起来可能很简单，但这里的目的是理解创建 API 或控制器所需的步骤。我们将使用`[kubebuilder](https://book.kubebuilder.io/)`进行初始搭建。

1.  创建一个新目录并初始化项目

```
$ mkdir custom-k8-controller

$ cd custom-k8-controller 

$ go mod init github.com/Karthik-K-N/custom-k8-controller
```

2.将`kubebuilder`用于初始脚手架

```
$ kubebuilder init --domain sample.domain --repo github.com/Karthik-K-N/custom-k8-controller

$ kubebuilder create api --group calculator --version v1 --kind Sum
Create Resource [y/n]
y
Create Controller [y/n]
y
```

3.在您选择的编辑器中打开新创建的项目。现在我们可以看到 Kubebuilder 为我们创建的各种目录。完整的项目可以在这个 [GitHub](https://github.com/Karthik-K-N/custom-k8-controller) 资源库中找到。

4.现在让我们将所需的成员添加到 API 目录下的`SumSpec`和`SumStatus`结构中。由于我们的意图是将两个数字相加，我们需要在代表输入的`SumSpec`中添加两个成员(`NumberOne`、`NumberTwo`)并在`SumStatus`中添加一个成员(`Result`)来存储结果。

```
// SumSpec defines the desired state of Sum
type SumSpec struct {
   NumberOne int `json:"numberOne,omitempty"`
   NumberTwo int `json:"numberTwo,omitempty"`
}

// SumStatus defines the observed state of Sum
type SumStatus struct {
   Result int `json:"result,omitempty"`
}
```

5.现在让我们在 Reconcile 方法中添加主控制器逻辑，以找到两个数的和。确保添加足够的日志记录——这将有助于调试和理解代码工作流。

```
func (r *SumReconciler) Reconcile(ctx context.Context, req ctrl.Request) (ctrl.Result, error) {

   var sum calculatorv1.Sum
   if err := r.Get(ctx, req.NamespacedName, &sum); err != nil {
      klog.Error(err, "unable to fetch sum")
      return ctrl.Result{}, client.IgnoreNotFound(err)
   }
   klog.Infof("Found the sum object %v", sum)

   klog.Infof("Calculating the sum of %d and %d", sum.Spec.NumberOne, sum.Spec.NumberTwo)
   result := sum.Spec.NumberOne + sum.Spec.NumberTwo
   sum.Status.Result = result

   klog.Info("Updating the result of calculation")
   if err := r.Status().Update(ctx, &sum); err != nil {
      klog.Error(err, "Unable to update sum status")
      return ctrl.Result{}, err
   }

   klog.Infof("Successfully updated the sum status with result %d", result)
   return ctrl.Result{}, nil
}
```

6.一旦完成了业务逻辑，现在就该测试控制器了。作为先决条件，我们需要一个 Kubernetes 集群来进行简单的测试。让我们创建一个 kind 集群并[部署](https://book.kubebuilder.io/cronjob-tutorial/cert-manager.html) cert-manager，当我们必须测试我们的 webhook 更改时，这是必需的。

```
$ kind create cluster --name custom-controller
$ kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.7.1/cert-manager.yaml
```

7.创建我们的控制器的清单，将它们安装到集群上并运行控制器。

```
$ make manifests
$ make install
$ make run
```

8.创建并应用`Sum` 资源:

```
$ cat custom-k8-controller/config/samples/calculator_v1_sum.yaml
apiVersion: calculator.sample.domain/v1
kind: Sum
metadata:
  name: sum-sample
spec:
  numberOne: 10
  numberTwo: 20$ kubectl create -f custom-k8-controller/config/samples/calculator_v1_sum.yaml$ kubectl get sum
NAME         AGE
sum-sample   5m8s$ kubectl get sum sum-sample -o yaml                                                           
apiVersion: calculator.sample.domain/v1
kind: Sum
metadata:
  creationTimestamp: "2022-02-27T17:17:03Z"
  generation: 1
  name: sum-sample
  namespace: default
  resourceVersion: "4562"
  uid: 9d25777c-d695-485f-9ba0-9c65526a8737
spec:
  numberOne: 10
  numberTwo: 20
status:
  result: 30
```

9.添加`kubebuilder`标签以查看关于获取 sum 资源的更多信息。

```
//+kubebuilder:printcolumn:name="Number One",type="integer",JSONPath=".spec.numberOne",description="Input number one"
//+kubebuilder:printcolumn:name="Number Two",type="integer",JSONPath=".spec.numberTwo",description="Input number two"
//+kubebuilder:printcolumn:name="Result",type="integer",JSONPath=".status.result",description="Sum of two numbers"

// Sum is the Schema for the sums API
type Sum struct {
   metav1.TypeMeta   `json:",inline"`
   metav1.ObjectMeta `json:"metadata,omitempty"`

   Spec   SumSpec   `json:"spec,omitempty"`
   Status SumStatus `json:"status,omitempty"`
}
```

10.创建并安装新的清单，观察结果的变化

```
$ make manifests
$ make install
$ make run
$ kubectl get sum
NAME         NUMBER ONE   NUMBER TWO   RESULT
sum-sample   10           20           30
```

这样，我们可以很容易地得到输入及其相应的结果。

# 编写 Kubernetes Webhook

因为我们的 sum 控制器已经准备好了，所以让我们添加一个 webhook。这里我们将只添加验证 webhook——我们不会添加变异或默认的 web hook。

1.  让我们再次使用`kubebuilder`进行 webhook scaffloding。

```
$ kubebuilder create webhook --group calculator --version v1 --kind Sum  --programmatic-validation
```

2.Kubebuilder 将在 api 目录下创建 webhook 文件。现在让我们添加验证 webhook 逻辑。为了简单起见，让我们在负数时使控制器无效

```
func (r *Sum) ValidateCreate() error {
   klog.Infof("In validate create of %s", r.Name)
   if r.Spec.NumberOne < 0 || r.Spec.NumberTwo < 0 {
      return fmt.Errorf("The input values Number One: %d Number Two: %d cannot be negative ", r.Spec.NumberOne, r.Spec.NumberTwo)
   }
   return nil
}
```

3.在测试更改之前，我们需要取消几个文件的注释以使 webhook 工作:

```
1\. [config/crd/kustomization.yaml](https://github.com/Karthik-K-N/custom-k8-controller/blob/main/config/crd/kustomization.yaml)
2\. [config/default/kustomization.yaml](https://github.com/Karthik-K-N/custom-k8-controller/blob/main/config/default/kustomization.yaml)
```

因为我们只使用验证 webhook，所以我们需要从下面的文件中删除变异块。

```
$ cat config/default/webhookcainjection_patch.yamlapiVersion: admissionregistration.k8s.io/v1
kind: ValidatingWebhookConfiguration
metadata:
  name: validating-webhook-configuration
  annotations:
    cert-manager.io/inject-ca-from: $(CERTIFICATE_NAMESPACE)/$(CERTIFICATE_NAME)
```

4.现在我们可以测试这些变化了。让我们首先构建 docker 图像。当测试 webhook 变化时，最好将它作为一个容器运行，这样我们就不需要在本地机器上处理证书管理。

```
$ make docker-build IMG=karthik/custom-controller:v1
```

5.因为我们使用的是一个 kind 集群，所以我们不需要将 docker 映像推送到 dockerhub。相反，我们可以将它加载到 kind 集群上

```
kind load docker-image karthik/custom-controller:v1 --name custom-controller
```

6.我们已经准备好部署映像了

```
make deploy IMG=karthik/custom-controller:v1
```

7.检查控制器盒状态

```
$ kubectl -n custom-k8-controller-system  get pods
NAME                                                       READY   STATUS    RESTARTS   AGE
custom-k8-controller-controller-manager-784f74cdf6-45hpq   2/2     Running   0          66s 
```

8.现在，让我们尝试用负数创建一个无效的 sum 资源，并观察控制器和 webhook 做了什么。

```
$ kubectl create -f config/samples/calculator_v1_sum.yamlError from server (The input values Number One: -10 Number Two: 20 cannot be negative ): error when creating "config/samples/calculator_v1_sum.yaml": admission webhook "vsum.kb.io" denied the request: The input values Number One: -10 Number Two: 20 cannot be negative
```

不出所料，验证 webhook 拒绝了创建新资源的请求。此外，我们可以看到控制器日志是怎么说的:

```
$ kubectl -n custom-k8-controller-system logs -f custom-k8-controller-controller-manager-784f74cdf6-45hpqI0304 15:45:49.386571       1 sum_webhook.go:39] In validate create of sum-sample
1.646408749386699e+09    DEBUG    controller-runtime.webhook.webhooks    wrote response    {"webhook": "/validate-calculator-sample-domain-v1-sum", "code": 403, "reason": "The input values Number One: -10 Number Two: 20 cannot be negative ", "UID": "2343b586-dc3f-45b5-9c75-df28b1c30b40", "allowed": false}
```

这样，我们可以测试控制器和 webhook 的变化。

# **额外提示—** 在默认 API 目录之外的其他目录中创建 webhook 文件

如果我们仔细观察，当我们使用`kubebuilder`进行 webhook 搭建时，它在 API 目录下创建了文件`sum_webhook.go`文件，并在 API 目录下多添加了一个依赖项。

如果它是一个简单的独立控制器，这没有关系。但是，如果新创建的 API 被导入到另一个控制器中呢？在这种情况下，导入的 API 将包含不必要的 webhook 依赖项。

为了避免这种情况，我们可以使用 webhooks 自定义验证器。让我们将生成的 webhook 文件移动到一个名为 webhook 的新目录中。

创建一个简单的结构，实现`Customvalidator`方法并更新`SetupWebhookWithManager`方法。新方法看起来是这样的，文件内容可以在这里看到[。](https://github.com/Karthik-K-N/custom-k8-controller/blob/main/webhook/sum_webhook.go)

```
type SumWebhook struct {
}func (r *SumWebhook) SetupWebhookWithManager(mgr ctrl.Manager) error {
   return ctrl.NewWebhookManagedBy(mgr).
      For(&v1.Sum{}).
      WithValidator(r).
      Complete()
}
```

请记住将 webhook 验证器更改为自定义验证器:

```
// var _ webhook.Validator = &Sum{}
var _ webhook.CustomValidator = &SumWebhook{}
```

最后，让我们更新`main.go`文件，使用新创建的 webhook，而不是 API 目录中的默认 webhook。

```
//if err = (&calculatorv1.Sum{}).SetupWebhookWithManager(mgr); err != nil {
// setupLog.Error(err, "unable to create webhook", "webhook", "Sum")
// os.Exit(1)
//}

if err = (&webhook.SumWebhook{}).SetupWebhookWithManager(mgr); err != nil {
   setupLog.Error(err, "unable to create webhook", "webhook", "Sum")
   os.Exit(1)
} 
```

一旦完成，我们就可以开始摇滚了！我们可以像前面一样测试这个变化——通过构建 docker 映像，将它加载到 kind 集群上，然后部署它。

希望这篇文章有助于理解如何创建一个简单的 Kubernetes 控制器和 webhooks！