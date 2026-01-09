[中文](README.md) | [English](README_EN.md) | [日本語](README_JA.md)

---

# Gophish Enhanced Edition

<div align="center">

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Go Version](https://img.shields.io/badge/go-1.15+-00ADD8.svg)
![Platform](https://img.shields.io/badge/platform-Linux%20%7C%20macOS-lightgrey.svg)

**Enterprise-Grade Phishing Simulation Platform based on Gophish**

**Optimized for Red Teaming & Security Awareness Training**

[Core Features](#-core-features) •
[Quick Start](#-quick-start) •
[Deep Dive](#-feature-deep-dive) •
[Configuration](#-configuration) •
[Security](#-security-policy)

</div>

---

## 💡 Introduction

Gophish Enhanced Edition is a deeply customized version based on the original Gophish v0.12.1. It addresses common pain points encountered in real-world red team operations and corporate security training, such as email blocking, obvious fingerprints, and lack of localization.

**Why Enhanced Edition?**

- 🛡️ **High Stealth**: Removes all fingerprint headers that trigger antivirus software or email gateways.
- 📧 **High Deliverability**: Ensures emails are delivered and displayed correctly using automated MIME degradation and native QR code technology.
- 🌏 **Localized Experience**: Full Chinese management interface, lowering the barrier for entry.
- ⚡ **Ready to Use**: Built-in one-click deployment options (optional), no complex environment configuration required.

---

## ✨ Core Features

Compared to the original Gophish, the Enhanced Edition implements the following breakthrough improvements:

| Dimension | Original Pain Point | Enhanced Solution |
|-----------|---------------------|-------------------|
| **Fingerprints** | `X-Gophish` headers expose tool identity | ✅ **Zero Fingerprint**: Removes all ID headers, spoofs sender clients (Zoho/Outlook) |
| **QR Code** | Remote image reference exposes IP & gets blocked | ✅ **CID Embedded**: QR codes sent as attachments, **offline viewable, 100% display rate** |
| **Delivery** | Often flagged as spam due to missing plain text | ✅ **Smart Degradation**: Auto-converts HTML to plain text RFC standard compliant |
| **Interface** | English-only UI, unfriendly to some users | ✅ **Deep Localization**: Core pages (Campaigns, Users, Templates) fully localized |
| **Anti-Tracing** | Default 404 page & fixed routes are easily traced | ✅ **Dynamic Camouflage**: Custom 404 pages, routes disguised as static resources (.js, .png) |

---

## 🔍 Feature Deep Dive

### 1. Native QR Code (CID Embedded)

This is one of the core features. Traditional phishing emails insert QR codes using `<img src="http://phish-server/qrcode.png">`. This has two fatal weaknesses:
1. Many corporate emails block remote images by default.
2. Image requests expose the recipient's IP address and are easily intercepted by gateways.

**Enhanced Mechanism**:
We rewrote the email sending module to use the **MIME CID (Content-ID)** mechanism. The QR code is generated instantly upon sending and sent together as an **embedded attachment**.

**Advantages**:
- 💯 **100% Display Rate**: The image is part of the email, independent of external networks.
- 🔒 **More Secure**: Does not trigger remote image loading warnings.

**Usage**:
Simply use the `{{.QR}}` variable in your email template.

---

### 2. Email Fingerprint Removal (Stealth Mode)

The original Gophish adds `X-Gophish-Contact` and `X-Gophish-Signature` headers to every email sent.

**Enhanced Improvements**:
- Completely removed all headers starting with `X-Gophish`.
- Spoofs the `X-Mailer` header to appear as common email clients (e.g., `Microsoft Outlook 16.0` or `Zoho Mail`), blending into normal traffic and making it extremely difficult for SOCs or email gateways to identify.

---

### 3. Localized Management Interface

For users who prefer Chinese, we have localized the Gophish admin panel, covering the most frequently used modules:
- **Campaigns**: Creation, Editing, Dashboard
- **Users & Groups**: Import targets, Editing
- **Templates**: Visual Editor
- **Landing Pages**: Page Editor

---

## 🚀 Quick Start

### Option 1: Docker (Fastest)

```bash
# 1. Build image
docker build -t gophish-enhanced .

# 2. Start container
# Mapped ports: 3333 (Admin), 80 (Phishing)
docker run -d \
  --name gophish \
  -p 3333:3333 \
  -p 80:80 \
  -v $(pwd)/gophish.db:/opt/gophish/gophish.db \
  gophish-enhanced
```

Access `https://127.0.0.1:3333` to enter the admin panel.

### Option 2: Build from Source

```bash
# 1. Clone project
git clone https://github.com/25smoking/gophishV4Modified.git
cd gophishV4Modified

# 2. Install dependencies
go get github.com/jaytaylor/html2text
go get github.com/skip2/go-qrcode

# 3. Build
go build -o gophish

# 4. Run
./gophish
```

---

## ⚙️ Configuration (config.json)

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
    "enable_qrcode": true    // Enable QR code feature
  },
  "db_name": "sqlite3",
  "db_path": "gophish.db",
  "migrations_prefix": "db/db_"
}
```

---

## 🛡️ Security Policy (Must Read)

⚠️ **Disclaimer**:
This project is intended ONLY for authorized enterprise security construction, red team operations, and security awareness training.

1. **Authorization Required**: You must obtain written authorization from the target system owner before using this tool for any testing.
2. **Illegal Use Prohibited**: Using this tool for any unauthorized attack activities is strictly prohibited.
3. **Data Privacy**: Exercise data should contain desensitized information; please destroy database files promptly after the exercise.

The developers assume no liability for any direct or indirect consequences resulting from the use of this tool.

---

## 🤝 Contribution & Support

Issues for bug reports or suggestions are welcome.

- **GitHub**: [github.com/25smoking/gophishV4Modified](https://github.com/25smoking/gophishV4Modified)

---

<div align="center">

Made with ❤️ for Security Professionals

</div>
