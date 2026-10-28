<p align="center">
  <img src="resources/icon.png" width="96" alt="DevKit Logo" />
</p>

<h1 align="center">DevKit</h1>

<p align="center">跨平台开发者工具箱</p>

<p align="center">
  Windows · macOS · Linux<br />
  网络抓包 · Mock 接口模拟 · H5 调试 · Git 可视化 · 文本与数据处理 · 局域网传输
</p>

DevKit 将日常开发中常用的调试、数据处理和系统辅助工具放进一个桌面应用，减少在多个软件、网页和命令行之间来回切换。

既可以查看手机 App 的请求、模拟尚未完成的后端接口，也可以编辑文本、整理思维导图、查询数据库，以及在手机和电脑之间传输文件与文字。

目前为公开测试版本，欢迎通过 Issues 反馈问题和提出建议。

[下载与安装](#下载与安装) · [功能一览](#功能一览) · [快速上手](#快速上手) · [本地开发](#本地开发) · [问题反馈](#问题反馈)

## 下载与安装

在 [GitHub Releases](https://github.com/yuanshenbin/DevKit/releases/latest) 选择适合自己系统和芯片架构的安装包。

| 系统 | 架构 | 安装包 |
| --- | --- | --- |
| Windows | Intel / AMD x64 | `.exe` 安装程序 |
| macOS | Apple Silicon / M 系列，arm64 | 对应的 arm64 `.dmg` |
| macOS | Intel，x64 | 对应的 x64 `.dmg` |
| Linux | Intel / AMD x64 | `.AppImage` 或 `.deb` |

### 首次安装与信任配置（请先阅读）

DevKit 由个人维护，使用免费生成的固定自签名证书。Windows、macOS 请同时下载 Release 中对应的公开证书，完成下列配置后，正常打开应用即可；这些证书与 HTTPS 抓包证书不同。

#### Windows

下载 EXE 和 `devkit-windows.cer`。在开始菜单搜索 `cmd`，右键「命令提示符」，选择**以管理员身份运行**，按系统提示确认。将下面命令中的 `D:\test\devkit-windows.cer` 替换为证书在你电脑上的实际完整路径，再复制到该管理员 CMD 窗口中执行，无需先打开 PowerShell。

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -Command "Import-Certificate -FilePath 'D:\test\devkit-windows.cer' -CertStoreLocation 'Cert:\LocalMachine\Root'"
```

导入成功后，**整台电脑的所有 Windows 账号均信任该证书**，无需为每个账号重复导入。运行 EXE 完成安装，再从开始菜单或桌面快捷方式正常打开 DevKit；日常运行无需以管理员身份启动。证书信任不会自动为所有账号安装应用，SmartScreen 仍可能提示。

#### macOS

当前 macOS 安装包使用自签名证书，尚未经过 Apple 公证。首次打开可能提示「Apple 无法验证 DevKit 是否包含恶意软件」。

先下载并双击 `devkit-mac.cer`，导入「登录」钥匙串，在「钥匙串访问」找到 `DevKit Internal macOS`，将其「代码签名」设为「始终信任」，关闭窗口并按系统提示确认。

再打开 DMG，将 DevKit 拖入「应用程序」并推出安装磁盘。确认安装包来自本项目 Releases 后，在「终端」中执行：

```bash
xattr -dr com.apple.quarantine "/Applications/DevKit.app"
```

该命令仅移除 DevKit 的下载隔离标记，不能替代代码签名信任。如果提示权限不足，可改用 `sudo xattr -dr com.apple.quarantine "/Applications/DevKit.app"`，按提示输入电脑登录密码（终端不显示密码字符）。执行成功后，从「应用程序」、启动台或 Dock **手动打开 DevKit** 即可，以后无需每次执行命令。

也可在启动被拦截后，进入「系统设置 → 隐私与安全性」，找到 DevKit 并选择「仍要打开」。本机放行不等于完成 Apple 公证。

#### Linux

Linux 无需安装 Windows/macOS 证书，也无需执行 `xattr`。AppImage 首次运行可能需要增加执行权限；在下载目录打开终端，将下面文件名替换为实际下载的文件名后执行：

```bash
chmod +x "./DevKit-版本号-x64.AppImage"
"./DevKit-版本号-x64.AppImage"
```

以后可双击 AppImage 正常打开；请放在当前用户可写的目录，在线更新需要文件及父目录可写。Debian / Ubuntu 也可安装 `.deb`，安装后从系统应用菜单打开，更新时可能需要管理员授权。Linux 的 GPG 公钥用于验证下载文件，不是启动或在线更新的必装证书。

配置完成后，进入「设置 → 关于 DevKit」即可检查并下载新版本，按提示完成覆盖更新。更新需满足对应平台的签名和目录权限要求；首次打开的放行不等于完成 Apple 公证。FUSE、沙箱问题及完整步骤见 [安装说明](docs/install.md)。具体发布版本、安装包和变更记录以 Releases 为准。

## 主要功能

### 网络抓包与接口调试

基于 [Whistle](https://github.com/avwo/whistle) 提供 HTTP / HTTPS 抓包能力，在 DevKit 中完成请求查看、规则配置和联调。

- 查看请求地址、Method、状态码、请求头、请求体、响应头和响应内容。
- 按 URL / Host 模糊过滤，支持用 `|` 分隔多个关键词，并按 Method 筛选。
- 按时间、状态码、耗时、响应大小等排序。
- 查看 JSON Tree，解析请求或响应中嵌套的字符串 JSON，保留原始抓包内容。
- 从抓包记录右键重新发送请求，或配置重放次数、并发数和超时；响应进入正常抓包列表。
- 配置上传 / 下载限速、请求延迟和请求失败率，模拟不同网络条件。

### Mock 接口模拟

后端接口还没完成时，可以先配置接口地址与响应，让 App 通过代理收到预设数据。

- 新建模拟接口，配置 URL、Method、响应状态码、Headers、正文和延迟。
- 默认按 URL + Method 匹配，也可启用 Query、请求 Headers 和 Body 条件。
- 支持 cURL 导入，将请求信息填入新建接口表单。
- 支持从抓包记录导入响应修改规则，编辑响应正文。
- 规则由 Whistle 执行，保存后同步到代理。

使用示例：将 `GET http://api.demo.test/user/info` 配置为返回一段 JSON。App 使用这个业务地址，并配置 DevKit 为 HTTP 代理，就能在请求命中规则后得到模拟响应。

详见 [Mock 接口模拟说明](docs/mock-interfaces.md)。

### H5 调试

手机访问本地 H5 时，通过抓包代理注入 vConsole，辅助查看页面日志和排查问题。

- 支持 HTTP 开发页面自动注入 vConsole。
- 查看已连接设备与调试日志。
- 支持配置注入黑名单。

本地开发服务需要允许局域网访问，手机请求需要经过 DevKit 代理。自动注入目前面向 HTTP 页面；HTTPS 页面及限制外部脚本的 CSP 页面暂不支持自动注入。

### 局域网传输

**手机互传**：电脑开启服务，手机用浏览器扫码，即可双向传文件；手机还可发送文字和链接到电脑，无需安装手机端应用，也无需开启抓包代理。

- 电脑收到文字后可直接复制，或打开弹窗查看全文。
- 保留接收到的文字原文，包括空格、换行和缩进。
- 单条文字上限为 1 MiB，按 UTF-8 字节计算。
- 电脑选择已允许的手机并发送文件，手机在原页面点击下载，保存位置由浏览器决定。

**电脑互传**：在运行 DevKit 的电脑之间发现或手动连接设备，发送文件及分享抓包记录；支持文件传输进度、暂停和断点续传。

详见 [手机互传](docs/file-transfer.md) 与 [电脑互传及抓包分享](docs/lan-capture-sharing.md)。

### Git 与本地工作台

- **Git 可视化**：打开多个仓库，查看工作区与暂存区 Diff、提交历史和分支，执行暂存、提交、拉取与推送；需要凭据时提供交互入口。
- **日志查看器**：实时监听本地日志文件，搜索、过滤和高亮关键词，按读取顺序升降序查看。
- **进程与端口**：查询本机进程、TCP / UDP 端口占用及系统可提供的资源信息，支持确认后结束进程或进程树。
- **SQLite 工作台**：浏览本地数据库与表结构，执行只读 SQL 查询，并导出 CSV。

## 功能一览

DevKit 当前提供 22 个工具菜单，部分菜单包含多个子功能。

| 分类 | 工具 | 功能 |
| --- | --- | --- |
| 文本与数据 | 文本编辑器 | 多标签编辑、语法高亮、查找替换、本地文件打开与保存 |
| 文本与数据 | JSON 格式化 | 格式化、压缩、校验、搜索及字符串 JSON 解析 |
| 文本与数据 | 文本对比 | 左右对比文本，高亮新增、删除与修改 |
| 文本与数据 | Markdown 预览 | 边写边预览，支持表格、代码块与任务列表 |
| 文本与数据 | 思维导图 | 节点编辑、布局与样式、搜索、XMind / JSON 文件管理及图片导出 |
| 文本与数据 | 二维码 / 深链 | 二维码生成与图片识别、URL 结构解析及 Query 参数编辑 |
| 网络与调试 | 网络抓包 | HTTP / HTTPS 抓包、Mock、请求重放、弱网模拟及 H5 调试 |
| 网络与调试 | HTTP 请求 | 请求发送、Headers 与 Body 配置、环境变量、cURL 导入、历史与收藏 |
| 网络与调试 | WebSocket 调试 | WS / WSS 连接、自定义请求头、Token、消息收发与文本心跳 |
| 网络与调试 | 局域网传输 | 手机与电脑双向传文件、手机传文字、电脑互传文件及抓包记录分享 |
| 项目与系统 | Git 可视化 | 仓库状态、Diff、提交历史、分支、暂存、提交、拉取与推送 |
| 项目与系统 | 日志查看器 | 文件监听、搜索过滤、关键词高亮、排序与跟随最新 |
| 项目与系统 | 进程与端口 | 进程查询、TCP / UDP 端口占用、进程及进程树结束 |
| 项目与系统 | SQLite 工作台 | 数据库浏览、表结构、只读 SQL 查询与 CSV 导出 |
| 编码与转换 | 颜色工具 | HEX、RGB、HSL 互转、透明度调节与颜色预览 |
| 编码与转换 | 坐标系转换 | WGS-84、GCJ-02、BD-09 与 Web Mercator 坐标转换 |
| 编码与转换 | 编码 / 解码 | URL、Base64、HTML 实体、Unicode、JSON 字符串与 Hex 转换 |
| 编码与转换 | 时间戳转换 | Unix 时间戳与日期互转 |
| 安全与加密 | 加密与摘要 | AES-256-GCM 加解密、MD5 / SHA 摘要、HMAC 及 Android 签名证书指纹 |
| 开发参考 | Node / npm 安装与镜像 | 三平台安装命令、镜像源、代理与包管理器配置参考 |
| 开发参考 | 环境变量配置 | Windows、macOS、Linux 开发环境配置命令生成 |
| 开发参考 | 命令与脚本 | 常用 Git、Android / ADB、Linux 命令及 Shell 脚本，支持收藏与复制 |

## 界面与使用习惯

- 支持浅色、深色和跟随系统主题。
- 可调整菜单与分组顺序，隐藏暂时不用的工具。
- 可开启顶部工具标签栏，切换已打开的工具。
- 快捷键在「设置 → 快捷键」统一查看、修改、禁用和恢复，按当前系统展示对应按键。
- 工具按需加载，切换时保留各工具支持恢复的输入、筛选与工作状态。
- 权限检查与恢复集中在「设置 → 权限与诊断」。

## 快速上手

### 抓取手机请求

1. 让手机与电脑连接到可互通的局域网。
2. 在 DevKit 打开「网络抓包」，使用 `0.0.0.0` 监听并启动代理，默认端口为 `8899`。
3. 在手机 Wi-Fi 设置中配置手动代理：服务器填写**电脑的局域网 IP**，端口填写 DevKit 实际显示的抓包端口。
4. 在手机上发起请求，回到 DevKit 查看抓包列表。
5. 需要解密 HTTPS 时，通过页面二维码下载 DevKit 根证书，并按系统要求安装、信任。

手机的代理服务器不能填写 `127.0.0.1`。部分 App 不遵循系统代理，或不信任用户安装的证书，需要其调试版本配合配置；仅安装证书不能保证所有 App 都能解密。

### 模拟尚未完成的接口

1. 打开「网络抓包 → Mock → 接口模拟」，新建接口。
2. 填写完整请求地址、Method 与模拟响应，保存并启用。
3. 启动抓包代理，让客户端通过该代理访问配置的业务地址。
4. 请求命中规则后，客户端收到预设响应；可在正常抓包列表中查看。

### 从手机发送文件或文字

1. 打开「局域网传输 → 手机互传」。
2. 确认接收目录并开启服务。
3. 手机浏览器扫码打开页面，按电脑端提示允许设备。
4. 在手机选择「文件」或「文字」发送，电脑端查看接收记录。

## 本地开发

需要 Node.js、npm 和 Git。安装版用户无需为了运行应用另行安装 Node.js；Git 可视化操作需要本机可用的 Git 环境。

```bash
npm install
npm run dev
```

类型检查：

```bash
npm run typecheck
```

技术栈：Electron、React 19、TypeScript、Ant Design 6、electron-vite；编辑器基于 Monaco，抓包基于 Whistle。

开发版使用独立数据目录，与安装版的设置、草稿、Mock 和抓包证书分开。原生模块重建、构建命令和签名配置见 [开发与打包指南](docs/development.md) 与 [打包维护说明](docs/packaging.md)。

## 进一步了解

- [Mock 接口模拟](docs/mock-interfaces.md)
- [请求重放](docs/proxy-replay.md)
- [弱网模拟](docs/network-simulation.md)
- [WebSocket 调试](docs/websocket.md)
- [局域网传输](docs/file-transfer.md)
- [电脑互传及抓包分享](docs/lan-capture-sharing.md)
- [思维导图](docs/mindmap.md)
- [三平台权限与恢复](docs/permissions.md)

## 问题反馈

欢迎通过当前仓库的 **Issues** 提交问题和功能建议。

反馈问题时，请尽量提供应用版本、操作系统与芯片架构、复现步骤、预期结果和实际结果；附上脱敏后的截图或错误信息，会更方便定位。

## 致谢

感谢 [Electron](https://www.electronjs.org/)、[React](https://react.dev/)、[Ant Design](https://ant.design/)、[Monaco Editor](https://microsoft.github.io/monaco-editor/)、[Whistle](https://github.com/avwo/whistle)、[Mind Elixir](https://github.com/SSShooter/mind-elixir-core) 等开源项目。

## 许可证

MIT。第三方依赖遵循各自的许可证。
