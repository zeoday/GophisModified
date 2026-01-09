[中文](README.md) | [English](README_EN.md) | [日本語](README_JA.md)

---

# Gophish Enhanced Edition - 红队增强版

<div align="center">

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Go Version](https://img.shields.io/badge/go-1.15+-00ADD8.svg)
![Platform](https://img.shields.io/badge/platform-Linux%20%7C%20macOS-lightgrey.svg)

**基于官方 Gophish 深度二次开发的企业级钓鱼演练平台**

**针对红队评估（Red Teaming）与安全意识培训场景进行全方位优化**

[核心功能](#-核心功能) •
[快速开始](#-快速开始) •
[功能详解](#-功能详解) •
[部署配置](#-部署配置) •
[安全规范](#-安全规范)

</div>

---

## 💡 项目简介

Gophish Enhanced Edition 是在原版 Gophish v0.12.1 基础上的深度改进版本。针对原版在实际红队演练和企业安全培训中遇到的痛点（如邮件被拦截、特征明显、缺乏本地化等），进行了大量底层代码级的优化和增强。

**为什么选择增强版？**

- 🛡️ **高隐蔽性**：移除了所有导致邮件被杀毒软件或网关拦截的指纹特征。
- 📧 **高送达率**：通过自动化 MIME 降级和原生二维码技术，确保邮件能准确送达并展示。
- 🌏 **本土化体验**：全中文管理界面，降低操作门槛，适合国内安全团队使用。
- ⚡ **开箱即用**：内置了一键部署方案（可选），无需复杂的环境配置。

---

## ✨ 核心功能

相比原版 Gophish，增强版实现了以下突破性改进：

| 增强维度 | 原版痛点 | 增强版方案 |
|---------|---------|-----------|
| **特征隐藏** | `X-Gophish` 头部直接暴露工具身份 | ✅ **零特征**：移除所有标识头，伪装发件客户端（Zoho/Outlook） |
| **二维码投放** | 只能引用远程图片，不仅暴露 IP 且即容易被拦截不显示 | ✅ **CID 内嵌技术**：二维码作为附件随邮件下发，**无需联网加载，必显** |
| **邮件投递** | 常常因缺少纯文本内容部分被判定为垃圾邮件 | ✅ **智能降级**：自动将 HTML 内容转化为纯文本，符合 RFC 标准，提高信誉度 |
| **用户界面** | 全英文界面，对非技术人员不友好 | ✅ **深度汉化**：活动、模板、用户管理等核心页面全中文支持 |
| **反溯源** | 默认 404 页面和固定路由容易被蓝队溯源 | ✅ **动态伪装**：支持自定义 404 页面，路由伪装成静态资源（如 `.js`, `.png`） |

---

## 🔍 功能详解

### 1. 原生二维码 (CID Embedded)

这是本版本最核心的特性之一。传统的钓鱼邮件插入二维码通常是使用 `<img src="http://phish-server/qrcode.png">`。这种方式有两个致命弱点：
1. 许多企业邮箱默认不加载远程图片。
2. 图片请求会暴露收件人的 IP 地址，且容易被邮件网关拦截。

**增强版机制**：
我们重写了邮件发送模块，使用 **MIME CID (Content-ID)** 机制。二维码在发送时即时生成，并作为邮件的**内嵌附件**一同发送。

**优势**：
- 💯 **100% 展示率**：因为图片是邮件的一部分，不依赖外部网络，离线也能看。
- 🔒 **更安全**：不触发远程图片加载警告。

**使用方法**：
在邮件模板中直接使用 `{{.QR}}` 变量即可。

---

### 2. 邮件指纹消除 (Stealth Mode)

原版 Gophish 会在每封发出的邮件中添加 `X-Gophish-Contact` 和 `X-Gophish-Signature` 头部。

**增强版改进**：
- 彻底移除了所有 `X-Gophish` 开头的头部字段。
- 将 `X-Mailer` 头部伪装成常见的邮件客户端（如 `Microsoft Outlook 16.0` 或 `Zoho Mail`），混入正常流量中，极难被 SOC 或邮件网关识别。

---

### 3. 中文管理界面

针对国内用户习惯，我们对 Gophish 的管理后台进行了汉化处理，覆盖了日常操作最频繁的模块：
- **Campaign (活动管理)**：新建、编辑、结果仪表盘
- **Users & Groups (用户与组)**：目标导入、编辑
- **Templates (邮件模板)**：可视化编辑器
- **Landing Pages (钓鱼页面)**：页面编辑器

---

## 🚀 快速开始

### 方式一：Docker 运行 (最快)

```bash
# 1. 拉取/构建镜像
docker build -t gophish-enhanced .

# 2. 启动容器
# 映射端口：3333(管理后台), 80(钓鱼服务)
docker run -d \
  --name gophish \
  -p 3333:3333 \
  -p 80:80 \
  -v $(pwd)/gophish.db:/opt/gophish/gophish.db \
  gophish-enhanced
```

访问 `https://127.0.0.1:3333` 即可进入管理后台。

### 方式二：源码编译

```bash
# 1. 克隆项目
git clone https://github.com/25smoking/gophishV4Modified.git
cd gophishV4Modified

# 2. 安装依赖
go get github.com/jaytaylor/html2text
go get github.com/skip2/go-qrcode

# 3. 编译
go build -o gophish

# 4. 运行
./gophish
```

---

## ⚙️ 核心配置 (config.json)

```json
{
  "admin_server": {
    "listen_url": "127.0.0.1:3333",
    "use_tls": true,
    "cert_path": "gophish_admin.crt",
    "key_path": "gophish_admin.key"
  },
  "phish_server": {
    "listen_url": "0.0.0.0:80",
    "use_tls": false,
    "enable_qrcode": true    // 开启二维码功能
  },
  "db_name": "sqlite3",
  "db_path": "gophish.db",
  "migrations_prefix": "db/db_"
}
```

---

## 🛡️ 安全规范 (必读)

⚠️ **免责声明**：
本项目仅供授权的企业安全建设、红队演练和安全意识培训使用。

1. **必须获得授权**：在使用本工具进行任何测试前，必须获得目标系统所有者的书面授权。
2. **严禁非法使用**：禁止将本工具用于任何未授权的攻击行为。
3. **数据隐私**：演练数据应包含脱敏信息，演练结束后请及时销毁数据库文件。

开发者不对因使用本工具造成的任何直接或间接后果承担责任。

---

## 🤝 贡献与支持

欢迎提交 Issue 反馈 Bug 或建议。

- **GitHub**: [github.com/25smoking/gophishV4Modified](https://github.com/25smoking/gophishV4Modified)

---

<div align="center">

Made with ❤️ for Security Professionals

</div>
