# Payment JS SDK 示例项目

本项目为 **Payment JavaScript SDK** 的集成示例，展示如何在 Web 页面中接入各种支付方式。每个示例页面均使用 SDK 的 iframe 输入组件完成完整的支付流程。

## 支付方式

| 示例页面 | 文件 | `paymentType` |
|---------|------|---------------|
| 银行卡支付 | `card-payment.html` | `cardpay` |
| Apple Pay | `apple-pay.html` | `applepay` |
| STC Pay | `stc-pay.html` | `stcpay` |
| Tabby 分期 | `tabby.html` | `tabby` |
| Tamara 分期 | `tamara.html` | `tamara` |

## 项目结构

```
payment_js_sdk_demo/
├── README.md
├── index.html          # 导航首页，包含所有示例入口
├── card-payment.html   # 银行卡支付示例
├── apple-pay.html      # Apple Pay 示例
├── stc-pay.html        # STC Pay 示例
├── tabby.html          # Tabby 分期支付示例
├── tamara.html         # Tamara 分期支付示例
└── shared/
    └── payment.css     # 所有示例共用样式
```

## 快速开始

### 第一步：获取 `orderId`

所有示例页面均通过 URL 查询参数传入订单号：

```
card-payment.html?orderId=<平台订单号>
```

`orderId` 由商户服务端调用 GCCPay **创建订单接口**后，从响应中获取。

### 第二步：运行示例

使用任意静态 HTTP 服务器托管本目录，然后在浏览器中打开：

```bash
# 使用 Python 内置服务器
python3 -m http.server 8080

# 访问导航首页
# http://localhost:8080/index.html

# 直接访问某个支付示例（需传入有效 orderId）
# http://localhost:8080/card-payment.html?orderId=YOUR_ORDER_ID
```

> **注意：** Apple Pay 需要 HTTPS 环境及支持 Apple Pay 的设备与浏览器，在不支持的环境下 SDK 不会渲染支付按钮。

## SDK 环境

示例默认使用**沙箱（测试）**环境。

| 环境 | SDK 地址 |
|------|---------|
| 沙箱 / 测试 | `https://h5-dev.gccpay.cn/jssdk/gccpay.payment.sdk.1.0.0.js` |
| 生产 | `https://cashier.sgate.sa/jssdk/gccpay.payment.sdk.1.0.0.js` |

上线前，将各 HTML 文件中的 `<script src="...">` 替换为生产环境地址即可。

## SDK 参数说明

```js
new PaymentSDK(
  orderId,        // String  — 平台订单号，由服务端下单接口返回
  paymentType,    // String  — 'cardpay' | 'applepay' | 'stcpay' | 'tabby' | 'tamara'
  orderType,      // String  — 固定值：'online_payin'
  iframe3DSFlag,  // Boolean — true：3DS 验证以 iframe 嵌入；false：另开新页面
  contentLanguage // String  — 'en-US'（英语）| 'ar-SA'（阿拉伯语）
);
```

## 集成流程

```
1. 引入 SDK <script> 标签
       ↓
2. 商户服务端下单，获取 orderId
       ↓
3. 实例化 PaymentSDK，传入 orderId 及支付参数
       ↓
4. 注册 paymentSuccess / paymentError 事件回调
       ↓
5. 调用 await paymentInstance.init(options) 渲染 iframe 输入组件
       ↓
6. 用户点击支付按钮时，调用 paymentInstance.submit()
```

## 错误处理

| 错误类型 | 来源 | 处理方式 |
|---------|------|---------|
| 初始化错误 | `new PaymentSDK()` / `init()` | `try...catch` 捕获，页面展示错误提示 |
| 支付流程错误 | `paymentError` 事件回调 | 读取 `data.message`，通过错误横幅提示用户 |

**初始化错误**常见原因：`orderId`、`paymentType` 或 `orderType` 无效，或指定的 DOM 容器不存在。

**支付流程错误**常见原因：卡号无效、支付被拒、网络超时等。

## 样式定制

SDK 将支付输入框以 `<iframe>` 形式渲染到指定容器中。可通过调整**容器 `<div>` 元素**的 CSS 控制布局与外观，`<iframe>` 内部样式不可直接修改。示例的基础样式统一定义在 `shared/payment.css` 中。
