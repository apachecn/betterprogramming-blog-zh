# 在近距离协议上做一个 NFT Minter

> 原文：<https://betterprogramming.pub/make-an-nft-minter-on-the-near-protocol-56ac3c5c1f45>

## 使用 rust 在 NEAR 协议上创建 NFT

![](img/05e0c0385330b2d4cd6a96cfe131bf5d.png)

## 介绍

NFT 或不可替换令牌是具有关联元数据的唯一令牌。NFTs 不能用别的东西代替。它们可以被购买，并且喜欢加密货币。在本指南中，我们将为 near 协议创建一个智能合同，以创建 NFT。

## 什么是近协议？

[近协议](https://near.org/)是新一代的第 1 层区块链，它解决了交易速率慢、吞吐量有限和兼容性差等问题。它是基于惊人的分片概念开发的，这意味着当网络利用率高时，节点会被分割成更小的分片，并在这些分片上进行并行计算。这个网络可以根据高需求进行自我扩展。

## 先决条件

来创建我们需要的近乎智能的契约。

*   **Rust —** Rust 是一种高性能通用编程语言，我们将使用它来创建一个智能合同。有一本非常好的[书](https://doc.rust-lang.org/book/)可供学习铁锈。
*   [**Near CLI**](https://docs.near.org/docs/tools/near-cli)**——**命令行界面，用于与 Near 区块链连接和交互。
*   **近距离钱包—** 在开始我们的项目之前，我们需要一个近距离钱包。查看本[指南](https://docs.near.org/docs/develop/basics/create-account)了解如何设置近距离钱包。

## **设置**

首先，创建一个货物项目。

```
cargo new nft --lib
```

转到项目文件夹 nft 你会看到。
`**src/lib.rs**` **—** 这是我们将要编写智能合同的地方
`**Cargo.toml**` **—** 这是我们的项目配置文件

前往`cargo.toml`并添加

```
[lib]
crate-type = ["cdylib", "rlib"][dependencies]
near-sdk = "*"
near-contract-standards = "*"
```

## **入门**

让我们从我们的智能合同脚手架开始。

```
use near_sdk::{
    near_bindgen,
    borsh::{self, BorshDeserialize, BorshSerialize}
};near_sdk::setup_alloc!();#[near_bindgen]
#[derive(BorshDeserialize, BorshDeserialize)]
pub struct Contract {}#[near_bindgen]
impl Contract {}
```

这看起来像一个奇怪的代码，现在什么也不做，让我们简化它。NEAR 遵循有状态方法，因此 struct Contract 将保存我们的契约状态，而在实现契约中，我们将编写更改状态的函数。

这里要注意的一点是，我们实现了一些宏并派生了一些特征。`#[near_bindgen]`宏将状态转换成有效形式，以便在近链上运行。`BorshDeserialize`、`BorshDeserialize`是来自 [borsh](https://borsh.io/) 机箱的特征，用于状态的反序列化和序列化。

现在，让我们为 NFT 创建我们的州。您知道，NFT 是一个唯一的令牌和与之相关联的元数据。根据这个定义，我们的状态看起来像这样。

```
use near_contract_standards::non_fungible_token::{
    metadata::NFTContractMetadata, NonFungibleToken,
};pub struct Contract {
    token: NonFungibleToken,
    metadata: LazyOption<NFTContractMetadata>,
}
```

现在，这个状态总是需要一个默认值，所以我们需要一个初始化函数来设置默认状态。

```
#[derive(BorshSerialize, BorshStorageKey)]
pub enum StorageKey {
    NonFungibleToken,
    Metadata,
    TokenMetadata,
    Enumeration,
    Approval
}#[near_bindgen]
impl Contract {
    #[init]
    pub fn new(owner_id: ValidAccountId) -> Self {
        Self {
            token: NonFungibleToken::new(
                StorageKey::NonFungibleToken,
                owner_id,
                Some(StorageKey::TokenMetadata),
                Some(StorageKey::Enumeration),
                Some(StorageKey::Approval)
             ),
             metadata: LazyOption::new(
                StorageKey::Metadata,
                Some(&NFTContractMetadata {
                    spec: NFT_METADATA_SPEC.to_string(),
                    name: "Example Name".to_string(),
                    symbol: "Example".to_string(),
                    icon: Some("ANY_SVG".to_string()),
                    base_uri: None,
                    reference: None,
                    reference_hash: None,
                 })
             ),
        }
    }
}
```

带有#[init]宏的函数将总是首先执行。通常用于设置默认状态。对于令牌标准和元数据标准，我推荐检查[这个](https://nomicon.io/Standards/NonFungibleToken/Core.html)和[这个](https://nomicon.io/Standards/NonFungibleToken/Metadata.html)链接。

现在，一切都为我们的初始化器和状态设置好了。让我们制作 NFT 铸造函数。

```
impl Contract {

    ......

    #[payable]    
    pub fn nft_mint(
        &mut self,
        token_id: TokenId,
        receiver_id: ValidAccountId,
        token_metadata: TokenMetadata,
    ) -> Token {
        self.token.mint(token_id, receiver_id, Some(token_metadata))
    }
}
```

让我们将它部署到链中。运行以下命令进行构建:

```
cargo build --target wasm32-unknown-unknown --release
```

运行以下命令进行部署:

```
near deploy --wasmFile target/wasm32-unknown-unknown/release/nft.wasm --accountId "your near account username"
```

这将返回一个程序 id，可用于调用智能合约的 RPC。您可以使用 near CLI 或 javascript 客户端与合同进行交互。