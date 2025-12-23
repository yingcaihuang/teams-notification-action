# Teams Notification Action / Teams 通知 Action

[English](#english) | [中文](#chinese)

<a name="english"></a>
## English

### Description
This GitHub Action sends rich **Adaptive Cards** notifications to Microsoft Teams. It is designed to provide a comprehensive summary of your GitHub Actions workflow runs, including status, version, commit details, and execution duration.

### Features
- 🎨 **Rich Adaptive Cards**: Sends beautifully formatted cards to Teams.
- 🚦 **Status Awareness**: Automatically styles the card based on status (Success, Failure, Cancelled, Waiting for Approval, etc.).
- ⏱️ **Duration Calculation**: Calculates and displays the workflow duration if `start_time` is provided.
- 🔗 **Quick Links**: Includes a button to jump directly to the Workflow Run on GitHub.
- 📝 **Detailed Info**: Shows repository, version/ref, actor, commit SHA, and commit message.
- 🌍 **Timezone**: Displays time in CST (UTC+8).

### Inputs

| Input | Description | Required | Default |
|-------|-------------|:--------:|---------|
| `webhook_url` | Microsoft Teams Webhook URL. | **Yes** | N/A |
| `status` | Workflow status (e.g., `Success`, `Failure`, `Cancelled`). | No | `Success` |
| `version` | Release version or tag (e.g., `v1.0.0`). | No | `github.ref` |
| `message` | Custom message or commit message. | No | `No commit message provided` |
| `title` | Custom title for the card. | No | Based on status |
| `color` | Card accent color (`Good`, `Attention`, `Warning`, `Accent`). | No | Based on status |
| `icon` | Status icon (e.g., ✅, ❌). | No | Based on status |
| `start_time` | Workflow start time for duration calculation (ISO 8601). | No | N/A |

### ⚠️ Key Configuration Guide

1.  **Webhook URL (Critical Security)**
    *   **Setup**: Get the URL from Teams Channel -> Connectors -> Incoming Webhook.
    *   **Security**: **NEVER** hardcode the URL. Store it in Repository Secrets (e.g., `TEAMS_WEBHOOK_URL`) and use `${{ secrets.TEAMS_WEBHOOK_URL }}`.

2.  **Status Awareness (Recommended)**
    *   Use `${{ job.status }}` to automatically set the card color (Green for Success, Red for Failure).
    *   Example: `status: ${{ job.status }}`.

3.  **Duration Calculation (Advanced)**
    *   To show "Duration: 2m 30s", you **MUST** capture the start time at the beginning of your job.
    *   See the "Set Start Time" step in the Usage Example below.

### Usage Example

```yaml
name: Build and Notify

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Set Start Time
        id: start_time
        run: echo "start_time=$(date -u +'%Y-%m-%dT%H:%M:%SZ')" >> $GITHUB_OUTPUT

      # ... Your build steps ...

      - name: Notify Teams (Success)
        if: success()
        uses: ./ # Or your-username/teams-notification-action@v1
        with:
          webhook_url: ${{ secrets.TEAMS_WEBHOOK_URL }}
          status: 'Success'
          version: ${{ github.ref_name }}
          message: ${{ github.event.head_commit.message }}
          start_time: ${{ steps.start_time.outputs.start_time }}

      - name: Notify Teams (Failure)
        if: failure()
        uses: ./ # Or your-username/teams-notification-action@v1
        with:
          webhook_url: ${{ secrets.TEAMS_WEBHOOK_URL }}
          status: 'Failure'
          message: "Build failed!"
```

---

<a name="chinese"></a>
## 中文

### 简介
这是一个用于向 Microsoft Teams 发送富文本 **自适应卡片 (Adaptive Cards)** 通知 GitHub Action。它旨在提供 GitHub Actions 工作流运行的全面摘要，包括状态、版本、提交详情和执行持续时间。

### 功能特性
- 🎨 **富文本自适应卡片**: 发送格式精美的卡片到 Teams。
- 🚦 **状态感知**: 根据状态（成功、失败、取消、等待审批等）自动调整卡片样式和颜色。
- ⏱️ **时长计算**: 如果提供了 `start_time`，会自动计算并显示工作流运行耗时。
- 🔗 **快速链接**: 包含一个直接跳转到 GitHub 工作流运行页面的按钮。
- 📝 **详细信息**: 显示仓库、版本/引用、触发者、提交 SHA 和提交信息。
- 🌍 **时区**: 显示时间为 CST (UTC+8)。
⚠️ 关键配置指南

1.  **Webhook URL (安全必读)**
    *   **获取**: Teams 频道 -> 连接器 (Connectors) -> Incoming Webhook -> 复制 URL。
    *   **安全**: **绝对不要**将 URL 明文写在代码中！请在仓库 Secrets 中配置 (如 `TEAMS_WEBHOOK_URL`)，并使用 `${{ secrets.TEAMS_WEBHOOK_URL }}` 引用。

2.  **状态感知 (推荐)**
    *   使用 `${{ job.status }}` 可以自动适配卡片颜色（成功为绿色，失败为红色）。
    *   配置: `status: ${{ job.status }}`。

3.  **耗时统计 (高级)**
    *   如果希望卡片显示 "Duration: 2m 30s"，你**必须**在 Job 的第一步记录开始时间。
    *   请参考下方“使用示例”中的 "设置开始时间" 步骤。

### 
### 输入参数 (Inputs)

| 参数名 | 描述 | 是否必填 | 默认值 |
|-------|-------------|:--------:|---------|
| `webhook_url` | Microsoft Teams Webhook URL 地址。 | **是** | N/A |
| `status` | 工作流状态 (例如: `Success`, `Failure`, `Cancelled`)。 | 否 | `Success` |
| `version` | 发布版本或 Tag (例如: `v1.0.0`)。 | 否 | `github.ref` |
| `message` | 自定义消息或提交信息。 | 否 | `No commit message provided` |
| `title` | 卡片标题。 | 否 | 基于状态自动生成 |
| `color` | 卡片强调色 (`Good`, `Attention`, `Warning`, `Accent`)。 | 否 | 基于状态自动生成 |
| `icon` | 状态图标 (例如: ✅, ❌)。 | 否 | 基于状态自动生成 |
| `start_time` | 工作流开始时间，用于计算耗时 (ISO 8601 格式)。 | 否 | N/A |

### 使用示例

```yaml
name: 构建并通知

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: 检出代码
        uses: actions/checkout@v4

      - name: 设置开始时间
        id: start_time
        run: echo "start_time=$(date -u +'%Y-%m-%dT%H:%M:%SZ')" >> $GITHUB_OUTPUT

      # ... 你的构建步骤 ...

      - name: 通知 Teams (成功)
        if: success()
        uses: ./ # 或者 your-username/teams-notification-action@v1
        with:
          webhook_url: ${{ secrets.TEAMS_WEBHOOK_URL }}
          status: 'Success'
          version: ${{ github.ref_name }}
          message: ${{ github.event.head_commit.message }}
          start_time: ${{ steps.start_time.outputs.start_time }}

      - name: 通知 Teams (失败)
        if: failure()
        uses: ./ # 或者 your-username/teams-notification-action@v1
        with:
          webhook_url: ${{ secrets.TEAMS_WEBHOOK_URL }}
          status: 'Failure'
          message: "构建失败！"
```
