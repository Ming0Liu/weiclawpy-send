---
name: weiclawpy-send
description: 通过 weiclawpy 给微信用户主动发送消息和文件。当用户提到要给微信发消息、微信通知某人、通过微信传文件、转发文档/截图/报告给微信联系人、主动推送告警到微信时触发。也适用于查询可发送的微信联系人列表、检查 weiclawpy 是否可用。不要用于发送邮件、短信或其他非微信渠道。
---

# weiclawpy-send

通过 `weiclawpy send` CLI 工具主动向微信用户推送消息和文件。

## 操作流程

### 1. 前提检查

发送前先确认环境就绪：

```bash
# weiclawpy 是否已安装？
python -m weiclawpy --version 2>/dev/null || pip show weiclawpy 2>/dev/null
```

若未安装，征得用户同意后执行：
```bash
pip install weiclawpy
```

```bash
# 凭证是否存在？
ls ~/.weiclawpy/credentials.json 2>/dev/null
```

若无凭证，需要先扫码登录一次：
```bash
weiclawpy send --to "<任意_user_id>" --text "登录测试" 2>&1
```
此时终端会出现二维码，让用户用微信扫码。

### 2. 查找可发送的接收者

读取 `~/.weiclawpy/context_tokens.json`，每个 key 就是一个可发送的微信用户：

```bash
cat ~/.weiclawpy/context_tokens.json 2>/dev/null
```

若此文件为空或不存在，目标用户必须先向机器人发过至少一条消息（这样才会生成 context_token）。

### 3. 发送消息

**发送文本：**
```bash
weiclawpy send --to "<user_id>" --text "消息内容"
```

**发送文件：**
```bash
weiclawpy send --to "<user_id>" --file "/path/to/file.pdf"
```

**同时发送文本 + 文件：**
```bash
weiclawpy send --to "<user_id>" --text "请看附件" --file "/path/to/report.pdf"
```

## 错误处理

| 场景 | 现象 | 处理方式 |
|---|---|---|
| Token 过期 | API 返回 401/403 | 命令后加 `--relogin` 强制重新扫码登录 |
| 无上下文令牌 | `⚠️ 未找到与 {id} 的对话上下文` | 对方必须先在自己的微信上向机器人发一条消息 |
| 文件不存在 | `文件不存在` | 先确认文件路径是否正确 |
| weiclawpy 未安装 | Command not found | 执行 `pip install weiclawpy` |
| 无凭证 | 终端弹出二维码 | 首次使用需要扫码登录，引导用户操作 |

## 重要说明

- **需要 weiclawpy 已配置好**（凭证存在 `~/.weiclawpy/` 下）
- **新收件人**：目标用户必须先给机器人发过至少一条消息，才能向 ta 发送
- **User ID**：在 `~/.weiclawpy/context_tokens.json` 中查看，通常是 `wx_user_xxx` 格式
- **凭证**保存在 `~/.weiclawpy/credentials.json`，重启后仍有效
- 每次发送前建议先确认 context_token 是否仍然有效
- `weiclawpy send` 返回成功仅表示送达微信服务器，实际是否到达取决于接收者的网络状态

## 使用示例

**例 1 — 查看接收者并发送：**
```
用户：给张三发微信说"项目已部署"
你：先 cat ~/.weiclawpy/context_tokens.json 找到张三的 user_id
    → weiclawpy send --to "wx_zhangsan_xxx" --text "项目已部署"
```

**例 2 — 发送文件：**
```
用户：把这个 report.pdf 通过微信发给李四
你：→ weiclawpy send --to "wx_lisi_xxx" --file "./report.pdf"
```

**例 3 — 发文件并附说明：**
```
用户：把今天的测试报告发给王五并说"请查收"
你：→ weiclawpy send --to "wx_wangwu_xxx" --text "请查收" --file "./test_report.pdf"
```
