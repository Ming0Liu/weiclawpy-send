# weiclawpy-send

<p>
  <img src="https://img.shields.io/badge/python-3.9%2B-blue" alt="Python 3.9+">
  <img src="https://img.shields.io/badge/license-MIT-green" alt="MIT License">
</p>

让 AI 学会使用 `weiclawpy send` 命令，主动向微信用户发送消息和文件。用户只需用自然语言告知发送对象和内容，AI 即可自动完成发送。

## 概述

**weiclawpy-send** 是一个 [Claude Code](https://claude.ai/code) SKILL，赋予 AI 调用 `weiclawpy send` CLI 的能力。安装后，用户可直接对 AI 说"给张三发微信说项目已部署"或"把这份报告发给李四"，AI 会自动查找接收者、调用命令完成发送。

核心能力：

- **发送文本消息** — AI 主动向微信用户推送文字通知
- **发送文件** — 支持 PDF、图片等任意格式文件
- **文本+文件一同发送** — 发送文件时附带说明文字
- **自然语言指挥** — 用户无需记忆命令，直接说出需求

## 前置条件

1. 已安装 [weiclawpy](https://github.com/Ming0Liu/weiclawpy)（`pip install weiclawpy`）
2. 已完成至少一次扫码登录（凭证保存在 `~/.weiclawpy/`）
3. 目标微信用户已向机器人发送过至少一条消息（用于获取上下文令牌）

## 安装

将本仓库克隆或复制到 Claude Code 项目目录中，确保 `.claude/skills/weiclawpy-send/SKILL.md` 文件位于可访问位置。

## 使用方法

### 发送文本消息

```bash
weiclawpy send --to "<user_id>" --text "消息内容"
```

### 发送文件

```bash
weiclawpy send --to "<user_id>" --file "/path/to/file.pdf"
```

### 同时发送文本和文件

```bash
weiclawpy send --to "<user_id>" --text "请看附件" --file "/path/to/report.pdf"
```

### 查看可发送的接收者

```bash
cat ~/.weiclawpy/context_tokens.json
```

每个 key 即为一个可发送的微信用户 ID。

## 示例

| 用户说... | AI 执行 |
|---|---|
| "给张三发微信说项目已部署" | 查找张三的 user_id → `weiclawpy send --to "wx_zhangsan_xxx" --text "项目已部署"` |
| "把这个 report.pdf 通过微信发给李四" | `weiclawpy send --to "wx_lisi_xxx" --file "./report.pdf"` |
| "把今天的测试报告发给王五并说请查收" | `weiclawpy send --to "wx_wangwu_xxx" --text "请查收" --file "./test_report.pdf"` |

## 关联项目

- [weiclawpy](https://github.com/Ming0Liu/weiclawpy) — 微信与 OpenCode 之间的双向桥接工具，`weiclawpy send` 命令由此项目提供

## License

[MIT](LICENSE) © 2026 weiclawpy-send
