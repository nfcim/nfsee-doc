---
layout: page
title: "添加卡片支持"
permalink: /add-card-support/
---

如果您想给 NFSee 添加更多的卡片支持，可以遵循以下的步骤：

## 脚本撰写

1. 阅读我们的 [扩展 API 文档](/js-extension/) 和 [内置读卡脚本](https://github.com/nfcim/nfsee/blob/master/assets/read.js) 以了解 NFSee 提供的 API 和使用方式；
2. 为卡片撰写相应的代码以进行通信，读取相应信息；
3. 将得到的卡片信息以规定格式返回，它将会直接被显示在 NFSee 的卡片详情页面。

下面是读取中国第二代居民身份证的示例函数：

```javascript

const tag = await poll();

if (tag.standard === "ISO 14443-3 (Type B)") {
    let r = await _transceive('0036000008');
    if (r.endsWith('900000'))
        return await ReadChinaID(r.slice(0, 16));
}

let ReadChinaID = async (ic_serial) => {
    let r = await _transceive('00A40000026002');
    if (!r.endsWith('900000'))
        return {};
    r = await _transceive('80B0000020');
    if (!r.endsWith('900000'))
        return {};
    const mgmt_number = r.slice(0, 32);
    return {
        'card_type': 'ChinaResidentIDGen2',
        'ic_serial': ic_serial,
        'mgmt_number': mgmt_number,
    };
};
```

NFSee 内置读卡脚本的返回数据格式为一个 JavaScript 对象：

```javascript
{
    'card_type': 'some_type',
    'some_attribute': 'some_value',
    'other_attribute': 'other_value'
}
```

其中 'card_type' 为必选项，表征卡片的唯一类别。其他属性为读取的卡片具体内容。为了方便呈现，请尽量复用内置读卡脚本中已有的字段。

请注意，您不应该在脚本中尝试对卡片进行任何形式的写操作。

## 联系我们

在使用脚本模式完成测试后，您可以联系我们，将脚本内置在 NFSee 中。您需要提供：

- 卡片名称（如：银联信用卡）
- 卡片对应的 Logo
- 读卡脚本
- 返回的数据格式

您可以将这些内容发送到 [nfsee@nfc.im](nfsee@nfc.im)，或者直接在 [GitHub](https://github.com/nfcim/nfsee) 上提出 Issue 或者 Pull Request。

感谢您对 NFSee 的贡献！
