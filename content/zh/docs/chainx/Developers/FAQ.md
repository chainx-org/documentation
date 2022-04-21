---
title: "FAQ"
date: 2020-07-13
weight: 1
description: >
  FAQ about ChainX specific RPC APIs.
---

**Q**: 为什么出现`"error": {"code": -32601, "message": "Method not found"}`?  
**A**: 当端口可被外部网络访问时，部分rpc被禁用，可以尝试设置`{rpc,ws}-external`为`false`, 或者打开`unsafe-{rpc,ws}-external`选项。  
