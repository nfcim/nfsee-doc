---
layout: page
title: "添加卡片支持"
permalink: /add-card-support/
---

如果您想给 NFSee 添加更多的卡片支持，可以遵循以下的步骤：

## 脚本撰写

1. 阅读我们的 [Lua 扩展 API 文档](/lua-extension/)
2. 为卡片撰写相应的代码以进行通信，读取相应信息
3. 将得到的卡片信息作为 Lua 数组返回，它将会直接被显示在 NFSee 的卡片详情页面

下面是读取银联信用卡的示例代码：

```lua
require 'sc'
rapdu = sc.transceive('00A4040008A000000333010102')
if not sc.is_ok(rapdu) then return nil end
rapdu = sc.transceive('00B2010C00')
if not sc.is_ok(rapdu) then return nil end
s, t = string.find(rapdu, '57') -- find track 2
if t == nil then return nil end
s, _ = string.find(rapdu, 'D', s) -- find card number margin
number = string.sub(rapdu, t + 3, s - 1)
exp = string.sub(rapdu, s + 1, s + 2)..'年'..string.sub(rapdu, s + 3, s + 4)..'月'
return {
    [1] = {'卡号', number},
    [2] = {'有效期', exp},
}
```

在读取到卡片时，所有的代码片段会被顺次执行，如果脚本返回 `nil` 则表示通信失败或非此类卡，NFSee 会尝试执行下一个脚本，直到成功或者所有片段被遍历。

请注意，您不应该在脚本中尝试对卡片进行任何形式的写操作。

## 辅助资料

您还需要提供：

- 卡片名称（如：银联信用卡）
- 卡片正面图片（可选，应该为标准信用卡比例）

而后，您可以将其发送给我们，途径为：

// TO BE DONE