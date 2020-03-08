---
layout: page
title: "JS 扩展 API"
permalink: /js-extension/
---

NFSee 提供脚本功能，允许用户运行自己编写的 JavaScript 脚本与卡片进行交互，以进行数据的读写。

## 概述

脚本功能可在主界面中点击齿轮按钮进入。您撰写的脚本将会作为一个异步 JavaScript 函数的内容执行。需要注意，可使用的 JavaScript 语法受到设备的浏览器版本限制。

我们提供下列的函数供第三方使用（类型标注仅供参考）：

## 标准库

### `async poll(): Promise<object>`

启动读卡模式并开始寻卡，默认超时为 20 秒。返回的数据为一个 [NFCTag](https://pub.dev/documentation/flutter_nfc_kit/latest/flutter_nfc_kit/NFCTag-class.html) 对象编码为 JSON 后的内容（字段名称均不变），任何空字段表示不支持或无法获取。

### `async transceive(command: string): Promise<string>`

用于与标签进行通信，`command` 需要为大写 16 进制字符串，返回值格式与其相同。此函数不对返回值进行检查，如果需要判断成功与否，请手动检查（如 `result.endsWith('9000')`）。

### `report(result: any)`

向 NFSee 返回数据，我们将会调用 `result.toString()` 并将其显示在脚本结果中。可调用多次，每次调用的结果将会被拼接起来。

### `error(err: any)`

与 `report` 类似，但将本次脚本执行状态标记为“错误”，NFSee 会将脚本结果显示为红色以提醒用户。请注意这一标记是不可逆的。`error` 与 `report` 可以任意混合使用。

### `log(log: any)`

向 Flutter 调试输出中打印日志，用户无法获取这一信息。

### `finish()`

结束本次读卡并关闭读卡模式。您**无需**在脚本中调用此函数，NFSee 会在函数执行完毕或遇到错误时自动执行。

## 错误处理

需要注意，如无特殊需要，您**不应该**捕获标准库的错误，NFSee 会处理它们并向用户显示友好的提示（如超时、传输错误等）。您也不应该通过抛出错误的方法来提示用户，而应该调用 `error` 函数。

## 贡献更多

如果觉得我们提供的库函数还不够，欢迎提出你的建议！
