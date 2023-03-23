# 如何艰难地保护库伯内特

> 原文：<https://betterprogramming.pub/how-to-secure-kubernetes-the-hard-way-9b421b36aba4>

## 改编自 Kelsey Hightower 的“Kubernetes the Hard Way ”,使用 Terraform 在运行 Docker 的 CentOS 上创建强化集群

![](img/d9ee65637e9f43197ab30c320f1afd80.png)

埃里克·麦克林在 [Unsplash](https://unsplash.com/s/photos/knot?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

Kubernetes 是开源的、健壮的，是市场上最流行的容器编排平台之一。然而，由于它的复杂性，并不是每个人都能适当地保护它。

经验丰富的系统管理员也很难处理它的多个移动部件和众多设置(他们内置这些以提供灵活性)。

因此，这可能是你最终陷入灾难的原因。由于 Kubernetes 的流行，许多供应商都提供托管的 Kubernetes 服务，这些服务都有一定程度的安全性，您不必太担心 Kubernetes 的架构，但是如果您没有这种选择呢？

你可以使用自动化工具在任何环境下创建集群，例如 [kubeadm](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm/) ，然而，大多数使用 kubeadm 的管理员很难理解 Kubernetes 是如何在幕后工作的，因为 kubeadm 会为你设置集群。

要知道，最好的方法是艰难地引导您的 Kubernetes 集群。这个故事改编自凯尔西·海托华的《苦日子》然而，与最初的指南不同，这份指南更关注构建一个更安全、生产就绪的 Kubernetes 集群。

这个故事是一个高级主题，假设您已经有了一些管理 Kubernetes 集群的经验。阅读“kubectl run nginx --image=nginx
kubectl get pods -l run=nginx
POD_NAME=$(kubectl get pods -l run=nginx -o jsonpath="{.items[0].metadata.name}")
kubectl port-forward $POD_NAME 8081:80

从堡垒主机的另一个实例中，运行:

```
ZONE=`gcloud compute instances list --filter="name=master01"| grep master01 | awk '{ print $2 }'`
gcloud compute ssh --zone=$ZONE --internal-ip master01
curl --head [http://127.0.0.1:8081](http://127.0.0.1:8081)
```

输出:

```
HTTP/1.1 200 OK
Server: nginx/1.17.9
Date: Sun, 12 Apr 2020 23:58:08 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Tue, 03 Mar 2020 14:32:47 GMT
Connection: keep-alive
ETag: "5e5e6a8f-264"
Accept-Ranges: bytes
```

## **执行测试**

测试我们是否可以在运行的容器中执行命令。

```
POD_NAME=$(kubectl get pods -l run=nginx -o jsonpath="{.items[0].metadata.name}")
kubectl exec -ti $POD_NAME -- nginx -v
```

输出:

```
nginx version: nginx/1.17.9
```

## **节点端口测试**

测试`NodePort`是否正常运行。

```
kubectl expose deployment nginx --port 80 --type NodePort
NODEPORT=$(kubectl get svc nginx -o jsonpath="{.spec.ports[0].nodePort}")
curl -I node01:$NODEPORT
curl -I node02:$NODEPORT
```

输出:

```
+ curl -I node01:32403
HTTP/1.1 200 OK
Server: nginx/1.17.9
Date: Sun, 12 Apr 2020 23:58:06 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Tue, 03 Mar 2020 14:32:47 GMT
Connection: keep-alive
ETag: "5e5e6a8f-264"
Accept-Ranges: bytes+ curl -I node02:32403
HTTP/1.1 200 OK
Server: nginx/1.17.9
Date: Sun, 12 Apr 2020 23:58:08 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Tue, 03 Mar 2020 14:32:47 GMT
Connection: keep-alive
ETag: "5e5e6a8f-264"
Accept-Ranges: bytes
```

## **测试日志**

测试我们是否可以从容器中浏览日志。

```
kubectl logs $POD_NAME
```

输出:

```
10.200.192.0 - - [12/Apr/2020:23:58:06 +0000] "HEAD / HTTP/1.1" 200 0 "-" "curl/7.29.0" "-"
10.200.0.1 - - [12/Apr/2020:23:58:08 +0000] "HEAD / HTTP/1.1" 200 0 "-" "curl/7.29.0" "-"
```

## **核心域名测试**

测试我们是否可以在节点内成功运行`nslookup`。为此，我们将发射一个`busybox`吊舱，从那里我们将尝试`nslookup` Kubernetes。如果您正确安装了 [CoreDNS](https://coredns.io/) ，那么 Kubernetes 就是默认创建的服务。

```
kubectl run busybox --image=busybox:1.28 --command -- sleep 3600
kubectl get pods
POD_NAME=$(kubectl get pods -l run=busybox -o jsonpath="{.items[0].metadata.name}")
sleep 10
kubectl exec -ti $POD_NAME -- nslookup kubernetes
```

输出:

```
Server:    10.32.0.10
Address 1: 10.32.0.10 kube-dns.kube-system.svc.cluster.localName:      kubernetes
Address 1: 10.32.0.1 kubernetes.default.svc.cluster.local
```

## **静态秘密加密测试**

我们将测试 Kubernetes 是否加密静态秘密。

```
kubectl create secret generic kubernetes-the-hard-way --from-literal="mykey=mydata"
```

现在登录到一个 etcd 节点，对秘密进行十六进制转储。

```
ZONE=`gcloud compute instances list --filter="name=etcd01"| grep master01 | awk '{ print $2 }'`
gcloud compute ssh --zone=$ZONE --internal-ip etcd01
sudo ETCDCTL_API=3 /usr/local/bin/etcdctl get \
  --endpoints=[https://127.0.0.1:2379](https://127.0.0.1:2379) \
  --cacert=/etc/etcd/ca.pem \
  --cert=/etc/etcd/etcd.pem \
  --key=/etc/etcd/etcd-key.pem\
  /registry/secrets/default/kubernetes-the-hard-way | hexdump -C
```

输出:

```
00000000  2f 72 65 67 69 73 74 72  79 2f 73 65 63 72 65 74  |/registry/secret|
00000010  73 2f 64 65 66 61 75 6c  74 2f 6b 75 62 65 72 6e  |s/default/kubern|
00000020  65 74 65 73 2d 74 68 65  2d 68 61 72 64 2d 77 61  |etes-the-hard-wa|
00000030  79 0a 6b 38 73 3a 65 6e  63 3a 61 65 73 63 62 63  |y.k8s:enc:aescbc|
00000040  3a 76 31 3a 6b 65 79 31  3a 37 8e ba c4 45 8b 58  |:v1:key1:7...E.X|
00000050  36 6d 23 a9 3f 3a 4d 3e  36 45 bd 37 be 23 85 52  |6m#.?:M>6E.7.#.R|
00000060  01 75 e9 df de 5a 66 9d  d0 26 04 d2 98 92 a8 ab  |.u...Zf..&......|
00000070  80 cb f2 fe a5 c5 16 9c  d5 14 1d c6 de 92 5b 1a  |..............[.|
00000080  6e 93 0c 91 17 ed d9 40  74 80 16 b1 36 45 7c cb  |n......[@t](http://twitter.com/t)...6E|.|
00000090  5e 1a 24 05 9f 2f 58 c0  b2 83 f7 2d b8 2d ca b6  |^.$../X....-.-..|
000000a0  1d 09 f8 3b 18 23 7c eb  1a 35 35 25 68 2f 6a 55  |...;.#|..55%h/jU|
000000b0  2f f2 53 f0 8a 04 53 dc  f0 56 4b a5 23 f1 fe 4a  |/.S...S..VK.#..J|
000000c0  bc 4d 0d a9 d8 03 2c 4b  1b 9e cd 12 ee 41 df ed  |.M....,K.....A..|
000000d0  4a f8 e5 19 a3 4c ed 06  74 08 07 d3 7e 1c 03 f2  |J....L..t...~...|
000000e0  a5 21 a4 0e 6a 91 86 93  5f 0a                    |.!..j..._.|
000000ea
```

如你所见，这个秘密被加密了！

## **烟尘测试清理**

清理我们为测试创建的资源。

```
kubectl delete secret kubernetes-the-hard-way
kubectl delete svc nginx
kubectl delete deployment nginx
kubectl delete deployment busybox
```

# 清理

如果你是为了学习而创建集群的，那么在你完成之后销毁基础设施以避免巨额账单是有意义的。

```
terraform destroy
```

# 结论

感谢阅读，我希望你喜欢这篇文章。故事的范围以按照最初的指南引导集群结束。

为了使您的集群更加安全，您还可以做更多的事情，我将在以后的文章中介绍这些内容。要获得更高层次的理解，请阅读“[如何为生产强化您的 Kubernetes 集群](https://medium.com/better-programming/how-to-harden-your-kubernetes-cluster-for-production-7e47990efc2a)”请继续关注空间，了解更多信息！