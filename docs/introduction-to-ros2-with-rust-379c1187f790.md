# Rust 公司 ROS2 简介

> 原文：<https://betterprogramming.pub/introduction-to-ros2-with-rust-379c1187f790>

## 编写发布者和订阅者

![](img/7ee02f44aef9d5deba2d732d88b880a4.png)

# 介绍

2022 年 5 月，ROS 2 卑微玳瑁(卑微)发布，支持 Ubuntu 22.04。由于我现在还在用 Ubuntu 20.04，所以这篇博客将重点放在`foxy`上。

先说第一件事，ROS2 怎么安装？

因为我们的团队正在从 ROS1 迁移到 ROS2，所以我现在需要同时使用两者。我目前的做法是按照[官方指南](https://docs.ros.org/en/galactic/Installation.html)在我的 OS 上安装 ROS2，使用 [RoboStack](https://robostack.github.io/) 通过`mamba`安装 ROS1。

尽管 RoboStack 也提供了`ros-galactic-desktop`，但我并不推荐它，因为它的包支持并不完整，而且不能很好地与 Python APIs 一起工作。

人们可能会想，在投入全面安装之前，是否有办法品尝一下 ROS2 的味道，看看自己是否喜欢它。有 [docker 文件](https://github.com/athackst/dockerfiles)给那些有 nvidia GPU 的人，而[这个 repo](https://github.com/Tiryoh/docker-ros2-desktop-vnc) 为 CPU 和 VNC 提供 docker 文件。本博客使用的是 VNC 版本。拉动并构建 docker 文件后，用浏览器连接`http://127.0.0.1:6080/`，显示桌面。还需要通过`sudo chmod 777 -R ~/.ros/`处理许可问题，你就可以走了。

# 你好世界

对于这一节的 Rust 接口，我准备用 [r2r](https://github.com/sequenceplanner/r2r) ，里面有如何使用`tokio`的例子。其他 Rust 接口也有，比如 [ros2_rust](https://github.com/ros2-rust/ros2_rust) ，正在积极开发中，但是还不支持 tokio。这个博客的代码在[这个回购](https://github.com/shanmo/learn-ros2)里。

让我们从古老的`hello world`例子开始。首先，创建一个货物二进制包

```
cargo new hello_world --bin --vcs none
```

在`src/main.rs`中，添加以下内容

```
use r2r::QosProfile;
use tokio::task;#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let ctx = r2r::Context::create()?;
    let mut node = r2r::Node::create(ctx, "testnode", "")?;
    let duration = std::time::Duration::from_millis(2500); let mut timer = node.create_wall_timer(duration)?;
    let publisher =
        node.create_publisher::<r2r::std_msgs::msg::String>("/hw_topic", QosProfile::default())?; task::spawn(async move {
        loop {
            timer.tick().await.unwrap();
            let msg = r2r::std_msgs::msg::String {
                data: "hello world".to_string(),
            };
            publisher.publish(&msg).unwrap();
            std::thread::sleep(std::time::Duration::from_millis(100));
        }
    });    // here we spin the node in its own thread (but we could just busy wait in this thread)
    let handle = std::thread::spawn(move || loop {
        node.spin_once(std::time::Duration::from_millis(100));
    });
    handle.join().unwrap(); Ok(())
}
```

`Cargo.toml`看起来是这样的

```
[package]
name = "hello_world"
version = "0.1.0"
edition = "2021"# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html[dependencies]
r2r = "0.6.2"
tokio = { version = "1.15.0", features = ["full"] }
```

下一次运行`Cargo run`并通过`ros2 topic list`检查主题，输出为

```
/hw_topic
/parameter_events
/rosout
```

要检查数据，使用`ros2 topic echo /hw_topic`

```
data: hello world
---
data: hello world
---
...
```

# 简单的出版商

我是`Sherlock Holmes`的粉丝，所以我将以此为例，尤其是 BBC 拍摄的[。在电视连续剧中，约翰·沃森写了一些关于夏洛克正在处理的案件的博客。因此，让我们创建一个出版商来发布沃森的博客，通过](https://www.bbc.co.uk/programmes/b018ttws)

```
cargo new watson --bin
```

本节使用的 Rust 接口是[rcrust](https://github.com/rclrust/rclrust)，它也支持`tokio`。注意，我们也可以从头开始创建一个 Rust 客户端，如这里的[所示](https://marshalshi.medium.com/create-a-rust-client-for-ros2-from-scratch-part-1-1-create-the-dynamic-library-via-cmake-empy-a93f78ae90d1)。我们需要将 Cargo.toml 中的依赖项定义为

```
[package]
name = "watson"
version = "0.1.0"
edition = "2021"# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html[dependencies]
rclrust = { git = "https://github.com/rclrust/rclrust.git", features = ["foxy"] }
rclrust-msg = { git = "https://github.com/rclrust/rclrust.git" }
tokio = { version = "1", features = ["full"] }
anyhow = "1.0"
```

在上面的代码中，我们可以通过 cargo 特性为`rclrust`指定 ROS2 版本。我们还需要包括`rclrust-msg`。

发布者代码是

```
use std::{thread::sleep, time::Duration};use anyhow::Result;
use rclrust::{qos::QoSProfile, rclrust_info};
use rclrust_msg::std_msgs::msg::String as String_;fn main() -> Result<()> {
    let ctx = rclrust::init()?;
    let node = ctx.create_node("watson_blog")?;
    let logger = node.logger();
    let publisher = node.create_publisher::<String_>("blog", &QoSProfile::default())?; let mut count = 1; 
    loop {
        publisher.publish(&String_ {
            data: format!("Watson's {}th blog", count),
        })?;
        rclrust_info!(logger, "Watson's {}th blog published", count);
        count += 1; 
        sleep(Duration::from_millis(100));
    } Ok(())
}
```

输出将是

```
[INFO] [1653823142.928885221] [watson_blog]: Watson's 1th blog published
[INFO] [1653823143.029225096] [watson_blog]: Watson's 2th blog published
[INFO] [1653823143.129426721] [watson_blog]: Watson's 3th blog published
[INFO] [1653823143.230213513] [watson_blog]: Watson's 4th blog published
[INFO] [1653823143.330655138] [watson_blog]: Watson's 5th blog published
```

更多示例可在[rcrust-examples](https://github.com/rclrust/rclrust-examples)中找到。

# 简单的订户

一个住在贝克街名叫比利的年轻人非常喜欢阅读沃森的博客，所以让我们为比利写一个订阅者。代码如下

```
use std::sync::Arc;use rclrust::{qos::QoSProfile, rclrust_info};
use rclrust_msg::std_msgs::msg::String as String_;#[tokio::main]
async fn main() -> anyhow::Result<()> {
    let ctx = rclrust::init()?;
    let mut node = ctx.create_node("billy_reader")?;
    let logger = node.logger(); let _subscription = node.create_subscription(
        "blog",
        move |msg: Arc<String_>| {
            rclrust_info!(logger, "I read: {}", msg.data);
        },
        &QoSProfile::default(),
    )?; node.wait();
    Ok(())
}
```

我们需要运行发布者和订阅者，输出是

```
[INFO] [1653826256.633754176] [billy_reader]: I read: Watson's 2th blog
[INFO] [1653826256.734067551] [billy_reader]: I read: Watson's 3th blog
[INFO] [1653826256.835288093] [billy_reader]: I read: Watson's 4th blog
```

比利非常喜欢这些博客，以至于每看完一篇他都会给一点奖励。我们需要发布一个`u8`类型来处理这个特性，如下所示

```
use std::sync::Arc;use rclrust::{qos::QoSProfile, rclrust_info};
use rclrust_msg::std_msgs::msg::String as String_;
use rclrust_msg::std_msgs::msg::UInt8 as u8_;#[tokio::main]
async fn main() -> anyhow::Result<()> {
    let ctx = rclrust::init()?;
    let mut node = ctx.create_node("billy_reader")?;
    let logger = node.logger();
    let publisher = node.create_publisher::<u8_>("reward", &QoSProfile::default())?;
    let reward: u8 = 10;     let _subscription = node.create_subscription(
        "blog",
        move |msg: Arc<String_>| {
            rclrust_info!(logger, "I read: {}", msg.data);
            publisher.publish(&u8_ {
                data: reward,
            }).unwrap();
            rclrust_info!(logger, "I paid: {} for the blog", reward);
        },
        &QoSProfile::default(),
    )?; node.wait();
    Ok(())
}
```

需要修改 blog publisher 节点来接收奖励，如下所示

```
use std::{thread::sleep, time::Duration};
use std::sync::Arc;use anyhow::Result;
use rclrust::{qos::QoSProfile, rclrust_info};
use rclrust_msg::std_msgs::msg::String as String_;
use rclrust_msg::std_msgs::msg::UInt8 as u8_;#[tokio::main]
async fn main() -> Result<()> {
    let ctx = rclrust::init()?;
    let mut node = ctx.create_node("watson_blog")?;
    let logger = node.logger();
    let publisher = node.create_publisher::<String_>("blog", &QoSProfile::default())?; let _subscription = node.create_subscription(
        "reward",
        move |msg: Arc<u8_>| {
            rclrust_info!(logger, "I received ${} reward", msg.data);
        },
        &QoSProfile::default(),
    )?; let logger = node.logger();
    let mut count = 1; 
    loop {
        publisher.publish(&String_ {
            data: format!("Watson's {}th blog", count),
        })?;
        rclrust_info!(logger, "Watson's {}th blog published", count);
        count += 1; 
        sleep(Duration::from_millis(100));
    } Ok(())
}
```

并且输出包括奖励消息

```
[INFO] [1653829848.327928005] [watson_blog]: Watson's 79th blog published
[INFO] [1653829848.329881922] [watson_blog]: I received $10 reward
```

# 摘要

这是对如何编写发布者和订阅者的快速介绍。

需要注意的一点是，与 ROS/ROS2 的 Python 或 C++示例不同，Rust 中没有回调函数，因为函数是异步的，正如这里提到的[和](https://github.com/adnanademovic/rosrust/issues/121)。

# 参考

*   [ROS 2 车间](https://ros2-industrial-workshop.readthedocs.io/en/latest/)
*   [d2l-ros2](https://github.com/fishros/d2l-ros2) 中文 ros2 课程