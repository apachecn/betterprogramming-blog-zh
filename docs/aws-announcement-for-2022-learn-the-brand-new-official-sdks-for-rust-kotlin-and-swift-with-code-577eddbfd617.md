# AWS 推出 Rust、Kotlin 和 Swift 的官方 SDK

> 原文：<https://betterprogramming.pub/aws-announcement-for-2022-learn-the-brand-new-official-sdks-for-rust-kotlin-and-swift-with-code-577eddbfd617>

## 看看如何在每种编程语言中使用 AWS SDKs

![](img/74045ff217777571bd4068acaf5deeb8.png)

卡斯帕·卡米尔·鲁宾在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在`Re:invent Dec,2021`活动期间，亚马逊网络服务宣布了 Rust、Kotlin 和 Swift 的 SDKs 目前处于开发者预览阶段。

这些语言的开发者社区对此非常欣赏和高兴。在这篇文章中，我们将通过代码示例详细讨论这些细节。

据 Amazon 称，每个 SDK 都利用了每种语言的通用最佳实践，并利用了高级语言语法。让我们深入了解每一个问题。

# 原生 AWS Rust SDK

Rust 是一种没有垃圾收集器的系统编程语言，专注于三个目标:安全性、速度和并发性。

> Rust 的 AWS SDK 提供了 Rust APIs 来与 Amazon Web Services 基础设施服务进行交互。使用 SDK，您可以在亚马逊 S3、亚马逊 EC2、DynamoDB 等基础上构建应用程序。

长期在 Rust 中开发的 AWS 客户想要一个本机 Rust SDK，以便他们可以使用他们习惯的语言结构，而 Rust 的新客户想要一个行为类似于他们在其他语言环境中使用的 SDK 的 SDK。

现在让我们做一些动作。

# 创建您的第一个 Rust AWS SDK 应用

让我们创建一个简单的 Rust 应用程序，列出您的 DynamoDB 表。

1.  导航到计算机上您想要创建应用程序的位置。
2.  执行以下命令创建一个`hello_world`目录，并用一个框架 Rust 项目填充它:

```
cargo new hello_world --bin
```

3.安装来自`crates.io`的 [aws-config](https://crates.io/crates/aws-config) 和 [aws-sdk-dynamodb](https://crates.io/crates/aws-sdk-dynamodb) 板条箱；

4 .导航到`hello_world`目录并编辑`Cargo.toml`以将这些板条箱包含在`[dependencies]`中，其中`*VERSION*`是您在`crates.io`上找到的版本号:

```
[dependencies] aws-config = "VERSION" aws-sdk-dynamodb = "VERSION" tokio = { version = "1", features = ["full"] }
```

5 .更新 src 目录中的`main.rs`以包含以下代码。

```
use aws_config::meta::region::RegionProviderChain;
use aws_sdk_dynamodb::{Client, Error};#[tokio::main]
async fn main() -> Result<(), Error> {
    let region_provider = RegionProviderChain::default_provider().or_else("us-east-1");
    let config = aws_config::from_env().region(region_provider).load().await;
    let client = Client::new(&config);let resp = client.list_tables().send().await?;println!("Tables:");let names = resp.table_names().unwrap_or_default();for name in names {
        println!("  {}", name);
    }println!("Found {} tables", names.len());Ok(())
}
```

6 .运行程序:

```
cargo run
```

您应该会看到一个表名列表。

# 原生 AWS Kotlin SDK

AWS SDK for kot Lin Developer Preview 为 Amazon Web Services 提供了 Kotlin APIs。使用 SDK，您可以构建适用于亚马逊 S3、亚马逊 EC2、DynamoDB 等的 Kotlin 应用程序。

> 在开发人员预览版中，您可以面向 JVM 平台或 Android API 级别 24+,并在未来的版本中支持其他平台，如 JavaScript 和 Native。

## 创建您的第一个 Kotlin AWS SDK 应用程序

```
package example.aws.getstartedimport kotlinx.coroutines.delay
import kotlinx.coroutines.runBlocking
import aws.smithy.kotlin.runtime.time.Instant
import aws.smithy.kotlin.runtime.time.epochMillisecondsimport aws.sdk.kotlin.runtime.UnknownServiceErrorException
import aws.sdk.kotlin.services.dynamodb.DynamoDbClient
import aws.sdk.kotlin.services.dynamodb.model.AttributeDefinition
import aws.sdk.kotlin.services.dynamodb.model.AttributeValue
import aws.sdk.kotlin.services.dynamodb.model.CreateTableRequest
import aws.sdk.kotlin.services.dynamodb.model.DynamoDbException
import aws.sdk.kotlin.services.dynamodb.model.KeySchemaElement
import aws.sdk.kotlin.services.dynamodb.model.KeyType
import aws.sdk.kotlin.services.dynamodb.model.ScalarAttributeType
import aws.sdk.kotlin.services.dynamodb.model.TableStatusfun main() = runBlocking {val dynamoDbClient = DynamoDbClient { region = “us-west-2” }
 val newTable = “TestSDK” + Instant.now().epochMilliseconds
 val key = “key”try {
 tutorialSetup(dynamoDbClient, newTable, key)println(“Writing to table…”)dynamoDbClient.putItem {
 tableName = newTable
 item = mapOf(
 key to AttributeValue.S(“${key}_value”)
 )
 }println(“Completed writing to table.”)
 println()cleanUp(dynamoDbClient, newTable)} catch (e: DynamoDbException) {
 println(“ERROR (DynamoDbException): “ + e.message)
 } catch (e: UnknownServiceErrorException) {
 println(“ERROR (UnknownServiceErrorException): “ + e.message)
 } finally {
 dynamoDbClient.close()
 }
 println(“Exiting…”)
}private suspend fun tutorialSetup(dynamoDbClient: DynamoDbClient, newTable: String, key: String) {val createTableRequest = CreateTableRequest {
 tableName = newTable
 attributeDefinitions = listOf(
 AttributeDefinition {
 attributeName = key
 attributeType = ScalarAttributeType.S
 }
 )
 keySchema = listOf(
 KeySchemaElement {
 attributeName = key
 keyType = KeyType.Hash
 }
 )
 provisionedThroughput {
 readCapacityUnits = 10
 writeCapacityUnits = 10
 }
 }
 println(“Creating table: $newTable…”)
 dynamoDbClient.createTable(createTableRequest)
 println(“Waiting for table to be active…”)
 var tableIsActive = dynamoDbClient.describeTable {
 tableName = newTable
 }.table?.tableStatus == TableStatus.Active
 do {
 if (!tableIsActive) {
 delay(500)
 tableIsActive = dynamoDbClient.describeTable {
 tableName = newTable
 }.table?.tableStatus == TableStatus.Active
 }
 } while(!tableIsActive)
 println(“$newTable is ready.”)
 println()
}private suspend fun cleanUp(dynamoDbClient: DynamoDbClient, newTable: String) {
 println(“Cleaning up…”)
 println(“Deleting table: $newTable…”)
 dynamoDbClient.deleteTable {
 tableName = newTable
 }
 println(“$newTable has been deleted.”)
 println()
 println(“Cleanup complete”)
 println()
}
```

有关 Kotlin 的完整 AWS 文档，请参考此处的。

# 原生 AWS Swift SDK

AWS SDK for Swift，这是一个纯粹的 Swift SDK，可以更容易地开发利用 AWS 服务的工具，包括亚马逊 S3、亚马逊 EC2、DynamoDB 等，所有这些都使用 [Swift](https://swift.org/) 编程语言。

> 使用 SDK for Swift 的开发目前在 Mac 和 Linux 系统上得到支持，Windows 支持即将推出。支持的目标包括苹果平台和 Linux 系统。对 Windows 目标的支持也即将推出。

支持的 Apple 目标操作系统包括:

*   马科斯
*   ios
*   iPadOS
*   watchOS *(即将推出)*
*   tvOS *(即将推出)*

## 创建您的首个 Swift AWS SDK 应用

以下是 AWS Swift 文档中关于如何开始的代码片段:

```
func listAllTables(ddb: DynamoDbClient) {
 ddb.listTables(input: ListTablesInput()) { result in
 switch(result) {
 case .success(let response):
 guard let namesOfTables = response.tableNames else {
 return
 }
 for currName in namesOfTables {
 print(“Table name is \(currName)”)
 }
 case .failure(let err):
 print(err)
 }
 }
}
```

请参考此处的[以获取 AWS Swift 的完整文档。](https://docs.aws.amazon.com/sdk-for-swift/latest/developer-guide/examples-ddb-tables.html)

目前就这些。感谢阅读。

# 参考

[用于 Swift 的 AWS SDK](https://github.com/awslabs/aws-sdk-swift)

[用于 Kotlin 的多平台 AWS SDK](https://github.com/awslabs/aws-sdk-kotlin)

[用于 Rust 编程的 AWS SDK](https://github.com/awslabs/aws-sdk-rust)

如果你喜欢你所读到的，看看下面这个故事:

[](/aws-big-announcement-just-before-2022-introduction-of-rds-readable-standby-instances-in-multi-az-7657ed96e48c) [## AWS 2022 年的新功能 RDS 中的可读备用实例介绍

### 就在 2021 年圣诞节的节日气氛之前，AWS 宣布了亚马逊 RDS 多 AZ 中可读的待机实例…

better 编程. pub](/aws-big-announcement-just-before-2022-introduction-of-rds-readable-standby-instances-in-multi-az-7657ed96e48c)