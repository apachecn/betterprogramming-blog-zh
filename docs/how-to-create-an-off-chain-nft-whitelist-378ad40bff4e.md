# 如何创建离线 NFT 白名单

> 原文：<https://betterprogramming.pub/how-to-create-an-off-chain-nft-whitelist-378ad40bff4e>

## 这篇技术文章将探讨 NFT 白名单的概念，以及如何为基于以太坊的 NFT 集合实现这一概念。

![](img/fbfe3dfbef8dc7bb6329d44de5550577.png)

照片由 [Shubham Dhage](https://unsplash.com/@theshubhamdhage?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/@theshubhamdhage?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

首先，让我们来定义什么是 NFT 白名单:这是一个获取预先批准用于铸造的加密钱包地址的过程。

这是一种防止所谓“天然气战争”的常见方法，在这种情况下，人们提高他们愿意支付的天然气价格来制造 NFT，以便他们的交易首先被接受，并且可以是一种有用的营销工具，在这种情况下，人们在采取某些行动后被添加到白名单中(例如:作为签署电子邮件简讯的交换)。

本文将带您了解在以太坊区块链上使用智能合约实现这类系统的步骤。

虽然有多种有效的方法，但我们将使用一个*优惠券*系统，在该系统中，钱包地址以一种智能合约可以验证其来自可信来源的方式进行离线签名。

完成本文后，您将能够:

*   将“白名单”功能添加到您的智能合约中，这将允许预先批准的钱包创造一个单一的 NFT。
*   设计一个与您的智能合同白名单相集成的 web 解决方案。

# 它将如何工作？

## 产生

每张优惠券都是一个简单的 javascript 对象，包含一个钱包地址，使用一个只有我们知道的私钥进行离线签名。

## 检索

我们的优惠券将存在于一个简单的 JSON 文件中，并将通过一个简单的 API 公开。

## 消费

当调用我们的智能合约时，可以使用生成的优惠券签名来证明接收的数据是由我们生成的。

# 让我们建造它

## 产生

让我们从生成优惠券开始。我们将使用公钥加密来加密“优惠券”中的钱包地址。

下面的脚本公开了一个接受一个`address`和`privateKey`并将返回一张优惠券的`createCoupon`函数。

```
const { ethers } = require(‘ethers’);async function createCoupon(address, privateKey) {
    // We’ll leverage the ethers library to create a new wallet
    // that we’ll use to sign the coupon with the private key. const signer = new ethers.Wallet(privateKey); // We need to encode the wallet address in a way that
    // can be signed and later recovered from the smart contract.
    // Hashing the address using the SHA-256 hashing algorithm
    // is a good way to do this. const message = ethers.utils.solidityKeccak256(
        [‘address’],
        [address]
    ); // Now we can sign the message using the private key. const signature = await   signer.signMessage(ethers.utils.arrayify(message)); // The signature can be expanded into it’s underlying components
    // which we can pass directly into the smart contract.
    // If we didn’t split the signature here — we’d have to do it
    // in the smart contract, which is a bit of a hassle. let { r, s, v } = ethers.utils.splitSignature(signature); return {r,s,v}
}module.exports = {
    createCoupon,
}
```

我们将需要一个密钥对来使用——用于部署您的合同的密钥对也可以使用，但是如果您仍然需要生成一个密钥对，您可以使用元掩码快速创建一个新的 wallet，并从那里导出私钥。

下面是一个小的 node.js 脚本，它将生成我们的优惠券:

```
const { createCoupon } = require("./coupons");
const fs = require('fs');
require("dotenv").config();// Insert private key corresponding to _couponSigner
const privateKey = process.env.PRIVATE_KEY;// Populate with addresses to whitelist
let addresses = [
    // ..
];const main = async () => {
    let output = [];
    console.log('Generating...');
    for (let i = 0; i < addresses.length; i++) {
        let signature = await createCoupon(addresses[i], 0, privateKey);
        output.push({
            wallet: mint[i],
            r: signature.r,
            s: signature.s,
            v: signature.v
        })
    }// Save the generated coupons to a coupons.json filelet data = JSON.stringify(output);
    fs.writeFileSync('coupons.json', data);
    console.log('Done.');
    console.log('Check the coupons.json file.');};const runMain = async () => {
    try {
        await main();
        process.exit(0);
    } catch (error) {
        console.log(error);
        process.exit(1);
    }
};runMain();
```

首先确保您用将用于签名的钱包的私钥填充了您的`.env`文件。然后，用钱包地址列表填充脚本中的`addresses`数组。

运行`node generateCoupons.js`生成优惠券并保存到`coupons.json`文件中。搞定了。

## 检索

因为每张优惠券只对一个钱包地址有效，所以如果优惠券被暴露就没有风险。然而，为了保持白名单的私密性，将它隐藏在一个 API 端点之后仍然是一个好主意，该端点响应钱包地址并在找到时返回相应的优惠券。

虽然编写一个 API 来提供这些优惠券超出了本文的范围，但是我可以向您展示使用下面的代码来查找并返回正确的优惠券是多么容易:

```
// retrieve the wallet from the query wallet
const wallet = req.query.wallet// Find a coupon for the passed wallet address
const c = coupons.filter(coupon => coupon.wallet.toLowerCase() === wallet.toLowerCase())if (0 == c.length) {
    return res.status(200).json({
        coupon: null, 
        message: 'Coupon not found'
    })
}return res.status(200).json({coupon: c[0]})
```

💡 [Next.js](https://nextjs.org/) 框架是构建这个 API 和剩下的前端造币网站的绝佳选择。

## 消费

在我们的智能契约中，让我们首先定义一个`struct`来表示我们的优惠券。

```
struct Coupon {
    bytes32 r;
    bytes32 s;
    uint8 v;
}
```

您可能会注意到，这看起来已经像我们用 Javascript 生成的优惠券了。

在我们的智能合约中，我们需要做一些事情来验证优惠券是有效的。

1.  创建与我们在 Javascript 代码中创建的相同的消息摘要(包含钱包地址)。
2.  使用消息摘要来恢复我们优惠券的签名者。
3.  确保恢复的签名者实际上是我们。

在 Solidity 中，我们可以通过编写两个内部函数来实现这一点:

```
// Recover the original signer by using the message digest and
// the passed in coupon, to then confirm that the original
// signer is in fact the _couponSigner set on this contract.function _isVerifiedCoupon(bytes32 digest, Coupon memory coupon)
    internal
    view
    returns (bool)
{
    address signer = ecrecover(digest, coupon.v, coupon.r, coupon.s);
    require(signer != address(0), "ECDSA: invalid signature");
    return signer == _couponSigner;
}// Create the same message digest that we know the coupon created
// in our JavaScript code has created.function _createMessageDigest(address _address)
    internal
    pure
    returns (bytes32)
{
    return keccak256(
        abi.encodePacked(
            "\x19Ethereum Signed Message:\n32",
             keccak256(abi.encodePacked(_address))
        )
    );
}
```

然后，我们可以更新我们的铸造功能，以使用我们的新优惠券系统:

```
function mint(Coupon memory coupon)
    external
    payable
{ require(
        _isVerifiedCoupon(_createMessageDigest(msg.sender), coupon),
        "Coupon is not valid."
    ); // require that each wallet can only mint one token
    require(
        !_mintedAddresses[msg.sender],
        "Wallet has already minted."
    ); // Keep track of the fact that this wallet has minted a token
    _mintedAddresses[msg.sender] = true; // ...
}
```

我们做到了！为了防止优惠券被重复使用，跟踪已经铸造的钱包是很重要的。

在造币网站上，我们需要在调用造币函数时传递我们的优惠券:

```
async function fetchCoupon(wallet) {
    const res = await fetch(`/api/coupons?wallet=${wallet}`) return await res.json()
}async function mint(wallet) {
    const coupon = await fetchCoupon(wallet) let tx = await contract.mint(coupon) // ...}
```

# 结论

您已经学习了一种简单、安全且有效的方法来实施 NFT 白名单。

这篇文章是从我即将发行的新书《NFT 作品集开发者指南》中特别改写的摘录。

```
If you'd like to get a head-start on learning to code an NFT Collection from scratch, check out my book: "[A developer's guide to launching an NFT Collection](https://book.michaelstivala.com/)" - use this link for 10% off![Follow me on twitter](https://twitter.com/MichaelStivala) for more blockchain-related tips and tricks, and to follow along as I use my software development skills to build $10k/month in passive income.
```