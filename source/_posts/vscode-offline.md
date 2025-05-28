---
title: vscode插件离线下载
date: 2025-05-27 16:48:38
tags:vscode
---

由于公司网络隔离,需要在无网络的情况下配置 vscode 的开发环境

经过追踪，发现插件的下载地址为

```code
https://marketplace.visualstudio.com/_apis/public/gallery/publishers/发布者/vsextensions/插件名/版本/vspackage
```

连接中对应的信息，需要在 vscode 的插件详情页面获取
![img](/assets/img/vscode-offline-1.png "插件详情页面")

代码处理片段如下，批量或者单个插件下载根据以下逻辑修改即可

```python
identifier = "saoudrizwan.claude-dev"
version = "3.17.5"
publisher = identifier.split(".")[0]
extension = identifier.split(".")[1]

print(f"https://marketplace.visualstudio.com/_apis/public/gallery/publishers/{publisher}/vsextensions/{extension}/versions/{version}/vspackage")
```

> 注意
> 插件名和发布者要以 identifier 中的为准，因为我们熟知的插件名与插件打包的名发布的名字不一样
