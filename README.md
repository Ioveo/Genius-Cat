# 🐱 天才猫订阅器 (Genius Cat) - 终极自动化防封锁架构

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Platform](https://img.shields.io/badge/platform-Cloudflare%20Workers-f38020.svg)
![Version](https://img.shields.io/badge/version-Ultimate-ff69b4.svg)

Genius Cat 是一款运行在 Cloudflare Workers 上的边缘极速代理调度枢纽。

本项目首创 **“前哨雷达 (Check_Proxy) + 主战坦克 (Genius Cat)”** 的双端联动架构。通过 Cloudflare KV 数据总线，雷达脚本会在后台定时进行真实 TLS 握手测绘，自动将全网最优质的 ProxyIP 喂给天才猫节点，实现**无人值守、永不失联**的终极抗封锁网络基建。

---

## ✨ 核心特性

- 🚀 **原生 VLESS 核心**：极低延迟的 WebSocket 流处理，支持 0-RTT 早期数据。
- 🛡️ **高阶链路穿透 (Proxy Chain)**：
  - 支持 `socks5://` 及 `http://` 代理协议二次接力。
  - 独家支持 `sstp://` (SoftEther VPN) 底层 TCP/IP 协议栈手搓穿透。
  - **全自动 ProxyIP 兜底**：通过 KV 读取雷达测出的极品 IP，防 CF 回环阻断。
- 🎭 **终极路径伪装 (Camouflage)**：支持将高权重域名（如 `https://...`）直接写入路径进行免流与 SNI 混淆。
- 🌍 **双引擎地理解析**：通过 Cloudflare 原生变量叠加 `IP-API` 兜底，精准嗅探节点归属地与 ASN。
- 🎨 **骇客霓虹 UI**：纯代码 (SVG + CSS3) 手搓霓虹幻影猫 LOGO，0 外部依赖，极速加载。

---

## 🛠️ 部署指南：双端联动架构 (保姆级)

为了实现“永不失联”的自动化架构，你需要部署 **两个 Worker** 和 **一个 KV 数据库**。请严格按顺序操作。

### 第一步：创建 KV 共享数据总线
1. 登录 Cloudflare 控制台，左侧菜单选择 **存储和数据库 (Storage & Databases)** -> **KV**。
2. 点击 **创建命名空间 (Create a namespace)**，命名为 `PROXY_KV_BUS`，点击添加。
*(这是雷达和天才猫之间交换极品 IP 的共享抽屉)*

### 第二步：部署“前哨雷达” (Check_Proxy)
这个 Worker 负责每天在后台自动测绘极品 IP，并写入 KV。
1. 在 Cloudflare 中创建一个新的 Worker，命名为 `proxy-radar`。
2. **绑定 KV（极重要）**：
   - 进入 `proxy-radar` 的 **设置 (Settings)** -> **绑定 (Bindings)**。
   - 添加 **KV 命名空间**，**变量名称必须填写：`KV`**，命名空间选择刚才创建的 `PROXY_KV_BUS`。
3. **注入雷达代码**：
   - 编辑代码，填入修改了自动化触发器逻辑的 `Check_Proxy.js` 代码。
   - *(注：必须在原版 `Check_Proxy.js` 底部加入 `scheduled` 触发器逻辑，让其定时拉取 IP 库进行扫描并 `env.KV.put('BEST_PROXY_IP', ip)`，详见代码示例)*。
4. **设置定时巡航**：
   - 切换到 **触发器 (Triggers)** 选项卡。
   - 找到 **Cron 触发器**，添加一条规则（建议填写 `0 */6 * * *`，即每 6 小时测绘一次）。

### 第三步：部署“主战坦克” (Genius Cat)
这个 Worker 是你实际连接翻墙和管理面板的入口。
1. 创建另一个新的 Worker，命名为 `genius-cat`。
2. **绑定同一个 KV（极重要）**：
   - 同样进入设置，添加 KV 绑定，**变量名称必须填写：`KV`**，命名空间同样选择 `PROXY_KV_BUS`。
3. **绑定自定义域名（核心防封锁）**：
   - 在 **触发器 (Triggers)** 中，添加 **自定义域 (Custom Domains)**（例如：`cat.yourdomain.com`）。
   - *注意：千万不要使用废弃的 `Routes (路由)` 方式绑定，也不要使用默认的 `*.workers.dev` 域名。*
4. **注入天才猫代码**：
   - 编辑代码，将本项目提供的 `_worker.js` (天才猫终极版) 代码粘贴进去，保存并部署。

---

## 🎮 使用方法与架构联动

### 1. 访问控制面板
在浏览器中打开你绑定的自定义域名并加上安全路径：
👉 `https://cat.yourdomain.com/auto` 
*(代码默认 token 为 `auto`，可在代码顶部 `mytoken` 变量处修改)*

### 2. 获取订阅链接
面板提供了 `Base64`、`Clash` 和 `Sing-box` 三种格式的极速订阅通道。点击即可复制，直接导入你的代理客户端（如 v2rayN, Clash Meta 等）。**请确保客户端节点的“地址”和“伪装域名”均为你的自定义域名。**

### 3. 架构如何自动联动？ (黑客魔法)
- **日常使用**：你不需要在面板的 PROXY 框里填任何东西。天才猫在处理你的流量时，会自动从 KV 中读取雷达 (`proxy-radar`) 最新测出的 `BEST_PROXY_IP` 作为底层跳板。
- **自愈能力**：如果当前的极品 IP 被 GFW 封锁，到了下一个定时周期（如 6 小时后），雷达会自动测出新的存活 IP 并更新 KV。你的客户端**无需更新订阅**，即可自动恢复满速翻墙。

### 4. 高阶自定义穿透 (PROXY 区域)
如果你有自己专属的节点，可以在面板的红色 **PROXY** 框中强行覆盖自动 IP：
- **SOCKS5 接力**：`socks5://user:pass@1.2.3.4:1080`
- **SSTP VPN 穿透**：`sstp://public-vpn.example.com:443`
- **免流/高权重路径伪装**：`https://secure.mse.waseda.ac.jp:443`
*(保存时，系统将自动嗅探这些地址的国家地区并在订阅中打上标签)*

---

## ⚠️ 声明与致谢

本项目为基于开源社区优秀代码的**二次改编 (Mod & Fork)** 版本，旨在学习与研究网络代理协议、边缘计算与高可用防封锁架构。在此向以下核心模块的原作者表达最崇高的敬意：

- **代理与协议栈核心 (Proxy Core)**：特别感谢原作者 **Alexandre Kojève大姐**（提供了硬核的底层 TCP/IP 协议栈手搓代码与 VLESS 穿透实现）。
- **订阅分发核心 (Sub Core)**：特别感谢原作者 **CM大佬**（提供了稳定高效的节点聚合分发与自动化订阅转换逻辑）。
- **真实 TLS 测绘核心 (Radar Core)**：特别感谢 Alexandre Kojève大姐（提供了基于内嵌 TlsClient 的 100% 真实可用性验证方案）。

**免责声明：**
1. 本项目代码仅供编程学习与网络协议研究使用，请勿用于任何商业用途。
2. 用户在使用本项目时，必须严格遵守其所在国家和地区的法律法规。任何因违法使用而导致的直接或间接后果，均与本项目、代码贡献者及原作者无关。
