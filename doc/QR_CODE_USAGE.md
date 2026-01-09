# QR 码生成功能使用指南

## 配置开关

在 `config.json` 中设置：
```json
"phish_server": {
    "enable_qrcode": true   // true 启用，false 禁用
}
```

## 使用方式

### 1. 直接访问生成
```
http://你的钓鱼服务器/qrcode?text=要编码的内容
```

### 2. 在邮件模板中嵌入
```html
<img src="{{.URL}}/qrcode?text={{.URL}}?id={{.RId}}" alt="扫码" />
```
每个收件人会收到包含个人追踪链接的唯一 QR 码。

### 3. 示例
```
http://example.com/qrcode?text=https://target.com/login
```
返回一个 256x256 的 PNG 图片。

## 注意事项
- 禁用后访问 `/qrcode` 会返回 Nginx 风格 404 页面
- QR 码大小固定为 256x256 像素
- 启用时日志会显示 "QR code endpoint enabled at /qrcode"
