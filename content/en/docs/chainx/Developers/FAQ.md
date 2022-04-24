---
title: "FAQ"
date: 2020-07-13
weight: 1
description: >
  FAQ about ChainX specific RPC APIs.
---

**Q**: Why does `"error": {"code": -32601, "message": "Method not found"}` appear?
**A**: When the port is accessible from the external network, some rpc is disabled, try setting `{rpc,ws}-external` to `false`, or turn on the `unsafe-{rpc,ws}-external` option.