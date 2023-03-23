# 如何通过 Relayer 部署智能合同

> 原文：<https://betterprogramming.pub/how-to-deploy-smart-contracts-via-relayer-db438ad7c399>

## 而不暴露私钥

![](img/28775a56ffcf1f784b962413f4663cf5.png)

照片由[Olav Ahrens rtne](https://unsplash.com/@olav_ahrens?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

以下是如何使用 [OpenZeppelin Defender](http://docs.openzeppelin.com/defender) 的 [Relayer API](https://www.npmjs.com/package/defender-relay-client) 部署智能合约而没有暴露私钥的风险。

## 复制你的防御者队 API 密钥和秘密

从[防御者](https://defender.openzeppelin.com)中，选择右上角的汉堡菜单，然后选择团队 API 键。在这里，您将看到任何现有的 API 键以及可以用它们做什么。在这个仪表板上，您还可以通过将鼠标悬停在 API 密钥上并从右侧选择垃圾桶来删除 API 密钥。

选择创建团队 API 密钥，然后选中所有内容，除非您希望限制使用此 API 密钥执行的权限。点击保存。系统将提示您复制 API 密钥和密码。将这些储存在安全的地方。它们将不会再次显示。

选择复选标记表示您已经复制了密钥，然后选择保存。

从 Defender 复制你的团队 API 密钥和秘密。

## 创建继电器

首先，安装必要的软件包:

```
$ npm install defender-relay-client
```

创建如下所示的脚本，在您的`.env`文件中提供 Defender API 密钥和秘密，并适当调整变量名。

```
const { RelayClient } = require(‘defender-relay-client’);
const { appendFileSync, writeFileSync} = require(‘fs’);async function run() {
 require(‘dotenv’).config();
 const { DEFENDER_API_KEY: apiKey, DEFENDER_API_SECRET: apiSecret } = process.env;
 const relayClient = new RelayClient({ apiKey, apiSecret });// create relay using defender client
 const requestParams = {
 name: ‘MyRelayer’,
 network: ‘goerli’,
 minBalance: BigInt(1e17).toString(),
 };
 const relayer = await relayClient.create(requestParams);

 // store relayer info in file (optional)
 writeFileSync(‘relay.json’, JSON.stringify({
 relayer
 }, null, 2));
 console.log(‘Relayer ID: ‘, relayer);// create and save the api key to .env — needed for sending tx
 const {apiKey: relayerKey, secretKey: relayerSecret} = await relayClient.createKey(relayer.relayerId);
 appendFileSync(‘.env’, `\nRELAYER_KEY=${relayerKey}\nRELAYER_SECRET=${relayerSecret}`);
}run().catch((error) => {
 console.error(error);
 process.exitCode = 1;
});
```

注意:要使用 API 通过 Relayer 直接发送事务，您需要密钥和秘密(在上面的代码中将它们附加到您的`.env`文件中)。

但是，如果您希望通过自动任务的方式发送一个事务，您需要的唯一凭证是团队 API 密钥和秘密以及`relayerId`(在上面的步骤中保存到了`relay.json`文件中)。

## 编译和部署合同

运行`npx hardhat compile`(或者如果使用不同的编译器，则运行同等工具)以准备好部署您的智能合约。

使用以下脚本部署合同:

```
const { DefenderRelayProvider, DefenderRelaySigner } = require(‘defender-relay-client/lib/ethers’);
const { ethers } = require(‘hardhat’);
const {writeFileSync} = require(‘fs’);async function main() {
 require(‘dotenv’).config();
 const credentials = {apiKey: process.env.RELAYER_KEY, apiSecret: process.env.RELAYER_SECRET};
 const provider = new DefenderRelayProvider(credentials);
 const relaySigner = new DefenderRelaySigner(credentials, provider, { speed: ‘fast’ });

 const MyContract = await ethers.getContractFactory(“SimpleRegistry”);
 const myContract = await MyContract.connect(relaySigner).deploy().then(f => f.deployed());writeFileSync(‘deploy.json’, JSON.stringify({
 MyContract: myContract.address,
 }, null, 2));console.log(`MyContract: ${myContract.address}\n`);
}if (require.main === module) {
 main().then(() => process.exit(0))
 .catch(error => { console.error(error); process.exit(1); });
}
```

这段代码从本地文件中提取中继者的凭证以及契约的构件，并使用 ethers.js 进行部署。合同的相关地址保存到本地文件`deploy.json`。

祝贺您通过 Relayer 成功部署您的合同！