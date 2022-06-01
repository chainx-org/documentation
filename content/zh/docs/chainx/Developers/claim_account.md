---
title: "绑定账户"
linkTitle: "绑定账户"
weight: 8
date: 2022-04-19
description: 绑定账户
---

##  绑定账户

绑定账户操作，可以方便wasm-evm资产来回划转

**第一步**
使用remix生成签名，签署格式为evm:substrate公钥不加0x

首先生成substrate公钥不加0x，访问 https://scan.chainx.org/tools/SS58，填入你的substrate地址
![](/images/scan1.png)

生成签名
![](/images/remix1.png)

![](/images/remix2.png)

![](/images/remix3.png)


![](/images/remix4.png)
生成签名：0x48129e580b013b76880d3327f77b5c7e19db1d15090f02e11cf462679338ab3c10ef62d49cb9a4f831305a5d9f32e9681cbad10de7d568893a0ec20ca8592f291c


**第2步** 
来到Chainx Dapp Wallet，选择网络，这里我们选择测试网，需要主网绑定，选择主网即可
![](/images/dapp1.png)
![](/images/dapp2.png)
点击保存


**第3步**
链接Chainx Testnet成功之后，去绑定账户
![](/images/dapp3.png)
![](/images/dapp4.png)

确认交易即可

