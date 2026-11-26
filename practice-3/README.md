# Pi Agent 源码获取、依赖安装与本地编译

本仓库记录第三次实践课的内容：从小组组织内的课程仓库获取 Pi Agent 源码，完成依赖安装、本地编译，并验证构建产物能够运行。第一次实践课侧重安装和使用 Pi 客户端，本次侧重建立本地源码开发环境。

完成后应能够说明四件事：

- 源码在哪里
- 依赖装在哪里
- 构建执行了什么
- 正在运行哪个版本

> 本次不要求构建独立 EXE，也不要求部署大模型权重。

## 仓库与版本

- 仓库地址：`<你的小组组织内 fork 的仓库地址>`
- 分支：`main`
- 提交编号：`<提交编号 SHA>`

## 环境要求

| 工具 | 本次用途 | 要求或建议 |
|---|---|---|
| Node.js | 运行 JavaScript 程序和项目开发脚本 | 根项目声明 `>=22.19.0`，建议使用 Node.js 24 LTS |
| npm | 安装依赖、执行项目脚本 | 使用 Node.js 附带的 npm，记录实际版本 |
| Git for Windows | 下载和管理源码，并提供 Git Bash | 安装后能执行 `git --version` |
| 文本编辑器 | 查看和编辑源码 | 可使用 VS Code 或自己熟悉的编辑器 |

根项目的版本要求来自 `package.json` 的 `engines` 字段，不是根据截图中的版本推测。根配置没有指定固定的 npm 版本。

## 本次任务清单

1. 检查并准备 Node.js、npm 和 Git，记录实际版本并截图。
2. 下载课程仓库源码，并截图。
3. 在仓库根目录安装依赖，说明安装命令的作用并截图。
4. 完成本地编译，检查构建产物并验证其能够运行，并截图。
5. 记录所遇到的困难、解决过程，并截图。

---

## 任务一 实验准备：检查和安装开发工具

### 1. 检查现有环境

打开 PowerShell，逐条执行：

```powershell
node --version
npm --version
git --version
```

三条命令分别检查 Node.js、npm 和 Git。第一次实践中 `pi` 能运行，不代表当前终端一定能找到这三个工具。

**截图要求 S1：** 显示当前环境依赖的各个版本。

![截图 S1：Node.js、npm、Git 版本](assets/placeholder-S1-env.png)

*图 S1 环境版本检查结果：Node.js `<版本>`，npm `<版本>`，Git `<版本>`。*

### 2. 安装 Node.js 24 LTS

已安装合适版本可跳过；需要安装时：

1. 打开 Node.js 官方下载页面。
2. 选择 **Node.js 24 LTS**，使用该系列可用的最新补丁版本。若教师指定统一版本，按指定版本安装。
3. 常见 Intel、AMD 电脑选择 **x64**；Windows ARM 电脑选择 **ARM64**。
4. 下载 **Windows Installer（`.msi`）** 并双击运行。
5. 按提示安装，保留默认目录和组件，确认勾选 **Node.js runtime**、**npm package manager** 和 **Add to PATH**。
6. 原生模块开发工具本次可暂不勾选，遇到具体编译任务时再安装。
7. 安装完成后关闭原终端，重新打开 PowerShell；使用 VS Code 内置终端时建议重新打开 VS Code。

npm 随 Node.js 官方安装程序一起安装，无需另外下载。重新检查：

```powershell
node --version
npm --version
node -e "console.log('Hello, Node.js!')"
```

最后一条命令应输出 `Hello, Node.js!`。版本号按实际结果记录即可。

### 3. 已使用 fnm 时：切换到 Node.js 24

`fnm` 是 Node.js 版本管理工具，以下命令仅适用于已安装并配置好 fnm 的环境。

```powershell
fnm --version
fnm install 24
fnm use 24
fnm default 24
node -v
npm -v
```

- `install`：下载指定版本系列。
- `use`：切换当前终端使用的版本。
- `default`：设置默认版本。

若提示环境变量未设置，在当前会话执行：

```powershell
fnm env --use-on-cd --shell powershell | Out-String | Invoke-Expression
```

之后重新执行版本切换和检查。无需照抄截图中固定的 npm 版本，记录自己实际结果即可。

**截图要求：** 显示切换后的 Node.js 与 npm 版本。

![截图：使用 fnm 切换到 Node.js 24](assets/placeholder-node24.png)

*图 使用 fnm 切换到 Node.js 24 后的版本输出。*

---

## 任务二 下载课程仓库源码

### 1. 建立实验目录并下载源码

尽量在全英文路径下下载项目。若目标文件夹已存在，不要直接删除或覆盖，可换一个空目录，或确认已有目录确实是本次所需仓库。

```powershell
git clone --branch main <你的小组组织内仓库地址>
cd <仓库目录名>
```

### 2. 确认源码来源和版本

```powershell
Get-Location
git remote -v
git branch --show-current
git rev-parse HEAD
git status --short
```

检查 `origin` 是否指向课程仓库、当前分支是否为 `main`，并把完整提交编号记录到报告中。后续安装和构建命令，除特别说明外，都在仓库根目录执行。

**截图要求 S2：** 显示当前目录、远端地址、分支和提交编号。仅打开 GitHub 网页不能证明源码已下载到本地。

![截图 S2：源码来源与版本](assets/placeholder-S2-source.png)

*图 S2 当前目录、远端地址、分支与提交编号。*

---

## 任务三 阅读配置，确认安装与编译的依据

用编辑器打开仓库根目录，查看以下文件：

| 文件或目录 | 需要观察的内容 |
|---|---|
| `README.md` | `Development` 中提供了哪些开发命令 |
| `package.json` | `engines`、`workspaces`、`scripts`、`devDependencies` |
| `package-lock.json` | 锁定依赖版本和依赖关系的文件 |
| `packages/` | 同一仓库中的多个子包 |
| `packages/coding-agent/package.json` | CLI 包的构建脚本和 `bin` 入口 |

在根 `package.json` 中找到：

```json
"engines": {
  "node": ">=22.19.0"
}
```

再找到 `scripts.build`。其中的命令会依次进入多个子包并执行各包自己的 `npm run build`；`&&` 表示前一步成功后才继续下一步。`npm run build` 的具体工作由项目配置决定，不是 npm 对所有项目都执行同一套编译过程。

该仓库使用 npm workspaces 管理多个包。需从根目录安装依赖，按根目录脚本顺序构建，不要自行给每个子包分别创建新的依赖锁定文件。

**截图要求 S3：** 显示 `engines.node` 和 `scripts.build` 的定义，可分为两张图。

**报告需回答：**

- Node.js 版本要求在哪里？
- `npm run build` 实际从哪里读取命令？

![截图 S3-1：engines 字段](assets/placeholder-S3-engines.png)

*图 S3-1 根 `package.json` 中的 `engines.node`。*

![截图 S3-2：build 脚本](assets/placeholder-S3-build-script.png)

*图 S3-2 根 `package.json` 中的 `scripts.build`。*

---

## 任务四 安装项目依赖

### 1. 执行安装

确认当前目录包含根 `package.json`，执行：

```powershell
npm install --ignore-scripts
```

该命令主要完成以下工作：

- 根据 `package.json` 和 `package-lock.json` 解析、下载并安装依赖；锁文件与声明一致时使用锁定版本。
- 在本地建立 `node_modules` 等依赖目录，并关联工作区中的本地包。
- 安装项目需要的库和开发工具，例如编译器、打包工具。
- `--ignore-scripts` 跳过安装阶段自动触发的生命周期脚本，例如 `postinstall` 和 `prepare`；不影响后续主动执行 `npm run build`。

这里没有 `-g`，安装的是当前项目的依赖，不会把本地修改后的 Pi 自动安装成全局 `pi` 命令。

**截图要求 S4：** 显示安装结束的输出结果，应区分警告与导致命令失败的错误。

![截图 S4：依赖安装结果](assets/placeholder-S4-install.png)

*图 S4 `npm install --ignore-scripts` 的安装结果（退出码 `<退出码>`）。*

### 2. 理解安装警告

- `EBADENGINE`：当前 Node.js 不满足某个包声明的版本要求。关注 `required` 与 `current` 的差异，切换到合适版本后重新安装。
- `deprecated`：依赖包已弃用或不再维护的提示，不等同于安装命令必然失败。
- `vulnerabilities`：依赖树的审计结果，需要记录和分析。不要直接照输出执行 `npm audit fix --force`，它可能改变课程使用的依赖版本。

`found 0 vulnerabilities` 只表示该次审计没有报告已知漏洞，也不是构建成功的证明。

---

## 任务五 本地编译与构建产物验证

### 1. 首次构建

仍在仓库根目录执行：

```powershell
npm run build
```

首次构建需要联网获取模型目录数据。这里的“模型数据”指模型目录、能力等配置数据，**不是下载大模型权重**。本地客户端构建与模型推理服务是两件事。

**截图要求：** 显示构建过程日志。

![截图：npm run build 过程](assets/placeholder-build-progress.png)

*图 `npm run build` 依次进入多个子包并生成模型目录数据。*

### 2. 检查并运行构建产物

```powershell
Test-Path .\packages\coding-agent\dist\bundle\cli.js
node .\packages\coding-agent\dist\bundle\cli.js --version
```

第一条应输出 `True`，第二条应输出当前源码对应的 Pi 版本。这个入口来自 CLI 包的 `bin.pi` 配置。

`dist` 保存构建产物。以后修改程序时应修改 `src` 下的源码，再重新构建；直接修改 `dist` 中的文件，可能在下次构建时被覆盖。

**截图要求 S5：** 显示完整构建结束的最后一段输出、退出码 `0`、入口文件存在，以及通过本地路径运行得到的版本号。某一个子包成功不能代表根构建全部成功；只有版本号也不能证明已完成本次构建。

![截图 S5：构建结果与产物验证](assets/placeholder-S5-build-verify.png)

*图 S5 构建结束输出、退出码、入口文件存在性检查与本地版本号。*

### 3. 后续重新构建

首次构建成功、模型目录数据已准备好且仍有效后，可使用：

```powershell
npm run build:offline
```

它仍会重新编译相关包，但复用已有模型目录数据。不要把它当成首次安装或首次数据初始化的替代命令；若提示数据缺失或过期，按错误提示补齐数据，或恢复网络后重新执行 `npm run build`。

---

## 任务六 启动本地源码版本并完成一次会话

### 1. 先验证源码入口

在仓库根目录执行：

```powershell
.\pi-test.bat --version
```

脚本名称包含 `test`，但它是运行本地源码的入口，不是执行整个项目测试套件。当前版本的 `.bat` 调用 `.ps1`，由 Node.js 加载源码解析器并运行 `packages/coding-agent/src/cli.ts`。

### 2. 启动交互界面

```powershell
.\pi-test.bat
```

本次使用上述命令验证本地源码。直接输入 `pi`，可能启动第一次实践课安装的全局版本，无法单独证明运行的是刚下载的源码。

本地源码版默认与已安装的 Pi 共用当前用户的配置目录 `~/.pi/agent`。第一次实践课已完成登录且凭据仍有效的同学，运行 `.\pi-test.bat` 后通常无需再次 `/login`。这表示复用了用户配置，并不表示运行的是全局安装版本。只有未配置模型、凭据失效或需要更换账户时，才需重新配置。

发送一个简单问题，例如：

```text
请用一句话说明源码和编译产物的区别。只回答问题，不修改文件。
```

确认实际收到模型回复。模型能否回复取决于账户、网络和模型服务配置；若失败，应将“本地程序启动成功”和“模型调用失败”分别记录，不能混为编译失败。

退出 Pi 返回 PowerShell 后，也可以运行编译产物进行比较：

```powershell
node .\packages\coding-agent\dist\bundle\cli.js
```

**截图要求 S6：** 显示 `.\pi-test.bat` 的启动命令或相关终端上下文、Pi 界面、自己的提示词和实际模型回复，可分为多张截图。不要提交 API Key、`auth.json` 或授权回调地址。

![截图 S6-1：本地源码启动与交互界面](assets/placeholder-S6-session.png)

*图 S6-1 通过 `.\pi-test.bat` 启动本地源码并完成一次会话。*

**报告需回答：**

- 本次如何确保运行的是本地源码？
- 源码入口与 `dist/bundle/cli.js` 入口分别适合什么场景？

---

## 命令速查

| 目的 | 命令 |
|---|---|
| 检查环境 | `node --version` / `npm --version` / `git --version` |
| 下载源码 | `git clone --branch main <仓库地址>` |
| 确认版本 | `git remote -v` / `git branch --show-current` / `git rev-parse HEAD` |
| 安装依赖 | `npm install --ignore-scripts` |
| 首次构建 | `npm run build` |
| 离线重建 | `npm run build:offline` |
| 验证产物 | `Test-Path .\packages\coding-agent\dist\bundle\cli.js` |
| 运行产物 | `node .\packages\coding-agent\dist\bundle\cli.js --version` |
| 运行源码 | `.\pi-test.bat` |

## 提交方式

将实验报告和截图上传到个人 Git 仓库，在课程作业仓库按第一次实践课的方式提交 Issue，填写报告地址。

- Issue 名称：`实践课3：Pi Agent源码安装与编译 <姓名拼音>`
- 个人仓库地址：`<个人 Git 仓库地址>`
- 姓名：`<姓名>`
- 学号：`<学号>`

建议复制原始作业 Markdown 文档后再贴图并补充自己的理解与操作过程，避免只有截图提交。

## 常见困难与解决记录

| 问题 | 现象 | 解决方法 |
|---|---|---|
| Node.js 版本过低 | 安装时出现 `EBADENGINE` | 升级到 Node.js 24 LTS 或满足依赖声明的版本后重装 |
| 终端找不到命令 | `node`/`npm`/`git` 不是内部或外部命令 | 重开终端；检查安装时是否勾选 `Add to PATH` |
| 构建失败 | 子包构建报错 | 查看完整日志定位具体子包，按提示修复后重新构建 |
| 模型目录数据缺失 | `npm run build:offline` 提示数据缺失或过期 | 恢复网络执行 `npm run build`，或按错误提示补齐数据 |
| 模型调用失败 | 程序启动成功但无模型回复 | 检查账户、网络与模型服务配置，与编译问题分开记录 |

> 个人遇到的困难：`<描述具体问题、排查过程与最终解决方案，并附截图>`
