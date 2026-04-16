# 🐱 天才猫订阅器 (Genius Cat) 

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Platform](https://img.shields.io/badge/platform-Cloudflare%20Workers-f38020.svg)
![Version](https://img.shields.io/badge/version-Ultimate-ff69b4.svg)

Genius Cat 是一款运行在 Cloudflare Workers 上的边缘极速代理调度枢纽。它不仅仅是一个 VLESS 节点生成器，更是一个集成了 **链式代理穿透**、**底层协议栈伪装** 和 **自动化多节点汇聚** 的终极抗封锁基建。

---

## ✨ 核心特性

- 🚀 **原生 VLESS 核心**：极低延迟的 WebSocket 流处理，支持 0-RTT 早期数据。
- 🛡️ **高阶链路穿透 (Proxy Chain)**：
  - 支持 `socks5://` 及 `http://` 代理协议二次接力。
  - 独家支持 `sstp://` (SoftEther VPN) 底层 TCP/IP 协议栈手搓穿透。
  - 支持纯净 `ProxyIP` 优选节点兜底，防 CF 回环阻断。
- 🎭 **终极路径伪装 (Camouflage)**：支持将高权重域名（如 `https://...`）直接写入路径进行免流与 SNI 混淆。
- 🌍 **双引擎地理解析**：通过 Cloudflare 原生变量 (`request.cf`) 叠加 `IP-API` 兜底，实现精准的 Client IP、国家地区与 ASN 组织嗅探。
- 🗂️ **KV 云端数据总线**：无缝对接 Cloudflare KV，实现外部节点聚合、代理规则热更新。
- 🎨 **骇客霓虹 UI**：纯代码 (SVG + CSS3) 手搓霓虹幻影猫 LOGO，0 外部依赖，极速加载。

---

## 🛠️ 部署指南 (保姆级)

为了保证节点的可用性，**请务必严格按照以下步骤操作，切勿遗漏自定义域名与 KV 的绑定。**

### 第一步：准备工作
1. 拥有一个 [Cloudflare](https://dash.cloudflare.com/) 账号。
2. 拥有一个托管在 Cloudflare 上的**自定义域名**（极其重要，使用默认的 `*.workers.dev` 域名在国内无法连接）。

### 第二步：创建 KV 数据库
1. 进入 Cloudflare 控制台，左侧菜单选择 **存储和数据库 (Storage & Databases)** -> **KV**。
2. 点击 **创建命名空间 (Create a namespace)**，名称随意（例如：`CAT_PROXY_DB`），点击添加。

### 第三步：部署 Worker 代码
1. 左侧菜单选择 **Workers & Pages** -> **创建应用程序 (Create application)** -> **创建 Worker (Create Worker)**。
2. 为 Worker 命名（例如：`genius-cat`），点击部署。
3. 点击 **编辑代码 (Edit code)**，清空原有的代码，将本项目中的 `_worker.js` 代码全部复制并粘贴进去。
4. 点击右上角的 **保存并部署 (Save and Deploy)**。

### 第四步：绑定 KV 与自定义域名（⚠️ 核心关键）
1. 回到该 Worker 的主界面，点击 **设置 (Settings)**。
2. **绑定 KV**：
   - 切换到 **绑定 (Bindings)** 选项卡。
   - 点击 **添加 (Add)** -> **KV 命名空间 (KV Namespace)**。
   - **变量名称 (Variable name) 必须严格填写：`KV`** （全部大写）。
   - 命名空间选择你在第二步创建的那个数据库，保存部署。
3. **绑定自定义域名**：
   - 切换到 **触发器 (Triggers)** 选项卡。
   - 找到 **自定义域 (Custom Domains)**，点击 **添加自定义域 (Add Custom Domain)**。
   - 填入你的二级域名（例如：`cat.yourdomain.com`），点击添加并等待证书生效。

---

## 🎮 使用方法

### 1. 访问控制面板
在浏览器中打开你绑定的自定义域名并加上安全路径：
👉 `https://cat.yourdomain.com/auto` 
*(代码默认 token 为 `auto`，可在代码顶部 `mytoken` 变量处修改)*

### 2. 获取订阅链接
面板提供了 `Base64`、`Clash` 和 `Sing-box` 三种格式的极速订阅通道。点击即可复制，直接导入你的代理客户端（如 v2rayN, Clash Meta 等）。

### 3. 高阶穿透配置 (PROXY 区域)
在面板的红色 **PROXY** 输入框中，你可以填入以下格式的规则，保存时系统会自动解析 IP 归属地：
- **SOCKS5 接力**：`socks5://user:pass@1.2.3.4:1080`
- **SSTP VPN 穿透**：`sstp://public-vpn.example.com:443`
- **纯净 ProxyIP**：`8.8.8.8:443`
- **免流/高权重路径伪装**：`https://secure.mse.waseda.ac.jp:443`

### 4. 节点池汇聚 (LINK 区域)
在面板的 **LINK** 输入框中，可以直接粘贴单节点分享链接（支持 `vless://`, `vmess://`, `trojan://` 等）。保存后，这些节点会自动混入你的订阅链接中进行分发。

---

## ⚠️ 声明与致谢

本项目为基于开源社区优秀代码的**二次改编 (Mod & Fork)** 版本，旨在学习与研究网络代理协议。在此向以下核心模块的原作者表达最崇高的敬意：

- **代理核心 (Proxy Core)**：特别感谢原作者 **Alexandre Kojève大姐**（提供了硬核的底层 TCP/IP 协议栈手搓代码与 VLESS 穿透实现）。
- **订阅核心 (Sub Core)**：特别感谢原作者 **CM大老**（提供了稳定高效的节点聚合分发与自动化订阅转换逻辑）。

**免责声明：**
1. 本项目代码仅供编程学习与网络协议研究使用，请勿用于任何商业用途。
2. 用户在使用本项目时，必须严格遵守其所在国家和地区的法律法规。任何因违法使用而导致的直接或间接后果，均与本项目、代码贡献者及原作者无关。
