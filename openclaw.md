# 下载 Node.js 22.x 的官方安装源
curl -fsSL https://deb.nodesource.com/setup_22.x | bash -

# 使用 apt 安装 Node.js (这包含了正牌的 npm)
apt-get install -y nodejs

# 验证是否安装成功
npm -v

# 开始配置 openclaw
npm config set registry https://registry.npmmirror.com
npm install -g pnpm
pnpm install -g openclaw@latest

# 倘若上一步报错 解决方法如下

# 第一步：让 pnpm 自动配置它的全局目录
pnpm setup

# 第二步：刷新系统的环境变量（让刚才的配置立刻生效，这步极其关键！）
source ~/.bashrc

# 第三步：重新执行 OpenClaw 的极速安装
pnpm install -g openclaw@latest

# 开始部署
openclaw onboard

开始一系列的询问 部署自己的API接口

# 开始接入飞书

最好是不要用自己学校/实习公司的飞书账号 因为部署的时候需要管理者统一才能发布 自己注册一个账号就可以自行进行发布了

# 可以开通的3个权限
im:message (获取与发送单聊、群组消息)

为什么安全： 机器人只能读取你主动发给它的消息，或者它所在的群里的消息。它绝对看不了你和其他人的私人聊天记录。

im:chat (获取群组信息)

为什么安全： 这仅仅是为了让它知道自己被拉进了哪个群（比如获取群名字和群 ID，为了匹配刚才向导里的白名单），它无法解散群或者乱踢人。

contact:user.base:readonly (获取用户基本信息)

为什么安全： 注意后面的 readonly（只读）。这意味着它只能看一眼跟你聊天的这个人的名字和头像，用来区分是谁在跟它说话。它绝对没有权限去修改你的企业通讯录、删除员工或查看敏感的薪酬/考勤信息。

# 飞书-开发者平台-创建自己的平台- 权限管理- 机器人创建 - 事件回调 - 不断发布新版本

# 配备完成后
刷新环境：
source ~/.bashrc
（未安装的先安装：apt update && apt install -y tmux；重新激活：tmux new -s claw）
tmux new -s claw
# 点火起飞
openclaw gateway

# 执行审批命令
openclaw pairing approve feishu SMCZT62K （这个根据龙虾给自己发的来写）
openclaw gateway

恭喜你成功收官！看到你的“龙虾”（OpenClaw）已经在飞书上活过来了，这种从零到一的掌控感确实很棒。

为了方便你以后在其他服务器（或者帮同学）部署，我为你整理了一份标准的 **Markdown 部署笔记**。你可以直接复制到你的 Notion、Obsidian 或者 GitHub 里。

---
完整版本：
# 🦞 OpenClaw (飞书版) 部署实战手册

**环境：** AutoDL 云服务器 (Ubuntu/Debian)
**大脑：** DeepSeek-V3 / R1
**通道：** 飞书 (WebSocket 模式)

## 一、 准备阶段 (飞书开发者后台)
在部署代码前，必须先在 [飞书开放平台](https://open.feishu.cn/) 拿到“准考证”：
1. **创建应用**：创建企业自建应用，获取 `App ID` 和 `App Secret`。
2. **添加能力**：左侧菜单 -> 添加应用能力 -> **机器人** (必须添加，否则无法通信)。
3. **配置权限**：权限管理中开通以下 3 个基础权限：
    - `im:message` (接收发送消息)
    - `im:chat` (获取群信息)
    - `contact:user.base:readonly` (读取用户基础信息)
4. **发布应用**：版本管理与发布 -> 创建版本 -> 申请发布 (自己点通过即可)。

## 二、 环境安装
在 AutoDL 终端执行以下命令：

```bash
# 1. 安装 Node.js 运行环境 (若镜像未自带)
curl -fsSL https://deb.nodesource.com/setup_20.x | bash -
apt-get install -y nodejs

# 2. 全局安装 OpenClaw
npm install -g openclaw

# 3. 安装后台保活工具 (防止关掉网页就掉线)
apt update && apt install -y tmux
```

## 三、 配置向导 (`onboard`)
执行 `openclaw onboard` 进入交互式配置：
1. **Model**：选择 `deepseek`，填入你的 `API Key`。
2. **Channel**：选择 `Feishu`。
3. **Credentials**：填入第一步拿到的 `App ID` 和 `App Secret`。
4. **Mode**：**务必选择 `WebSocket`** (适合无公网 IP 的 AutoDL)。
5. **Search/Skills**：初期建议全部选 `Skip for now` 以防报错。

## 四、 关键激活步骤 (非常重要)
配置完后，回到飞书后台完成“最后 100 米”：
1. **开启长连接**：事件与回调 -> 订阅方式 -> 选择 **使用长连接接收事件**。
2. **添加事件订阅**：点击“添加事件”，搜索并勾选 `接收消息 v1.0`。
3. **再次发布**：修改了事件订阅后，**必须重新发布一个新版本**。

## 五、 点火运行与权限审批
```bash
# 1. 开启后台窗口
tmux new -s claw

# 2. 启动程序
openclaw gateway

# 3. 身份配对 (首次对话时)
# 当机器人在飞书回复“访问未配置”并给出一个配对码 (如 ABCD) 时：
# 在终端输入：
openclaw pairing approve feishu ABCD
```

## 六、 常用维护命令
- **进入后台窗口**：`tmux attach -t claw`
- **退出后台但不关闭**：按下 `Ctrl+B` 后松开，再按 `D`。
- **强制重置配置**：`openclaw onboard --force`
- **清空对话上下文**：在飞书里对机器人发 `/reset`。

---


关闭Autodl 下次打开终端

tmux attach -t claw
# 如果报错没有 session，就重新跑一遍启动命令：
openclaw gateway

# 可以输入
tmux ls 检查龙虾是否还活着