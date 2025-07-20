# 每日天气预报飞书通知工作流

这个工作流会自动通过你的公网IP地址定位城市，并获取该城市的天气预报，然后通过社区提供的 `n8n-nodes-feishu-lite` 节点将信息发送到你的飞书群聊。如果无法通过IP定位，它将使用默认的后备城市“北京”进行查询。

## ✨ 功能

- **定时触发**: 每天早上 8:00 自动运行。
- **获取天气**: 从心知天气 API 获取实时的天气数据。
- **格式化消息**: 将天气数据格式化为美观的 Markdown 格式。
- **飞书通知**: 使用 `n8n-nodes-feishu-lite` 节点发送消息卡片到指定飞书群聊。

## 🚀 如何使用

1.  **导入工作流**: 将项目中的 `workflow.json` 文件导入到您的 n8n 实例中。

2.  **安装社区节点**: 此工作流依赖社区节点包 `n8n-nodes-feishu-lite`。请先在您的 n8n 实例中安装它。您可以参考 [n8n 官方文档](https://docs.n8n.io/integrations/community-nodes/installation/) 进行安装。

3.  **配置凭证与参数**: 这是运行工作流最关键的一步。您需要创建一个凭证，并修改工作流中的两个节点：

    *   **获取天气 (`Get Weather`) 节点**:
        *   打开此节点，找到 `URL` 参数。
        *   将 `YOUR_API_KEY` 替换为您自己的心知天气 API 密钥。如果您没有，可以前往[心知天气官网](https://www.seniverse.com/)免费注册获取。

    *   **配置飞书凭证**: `n8n-nodes-feishu-lite` 需要使用飞书应用的 App ID 和 App Secret 进行认证。
         *   1. 在 n8n 的 `Credentials` 页面，点击 `Add credential`。
         *   2. 搜索并选择 `Feishu API` (由 `n8n-nodes-feishu-lite` 提供)。
         *   3. 填入您的飞书应用的 `App ID` 和 `App Secret`。您可以参考[飞书开发文档](https://open.feishu.cn/document/ukTMukTMukTM/uADN14CM0UjLwQTN)创建一个应用并获取凭证。
         *   4. 保存凭证。

     *   **配置飞书发送节点 (`Send Feishu Message`)**:
         *   打开此节点，从 `Credential for Feishu API` 下拉列表中选择您刚刚创建的凭证。
         *   在 `Receive ID` 字段中，填入您想要接收消息的飞书群的 `Chat ID`。您可以参考[此文档](https://open.feishu.cn/document/uAjLw4CM/ukTMukTMukTM/reference/im-v1/chat/events/added-to-chat)了解如何获取 Chat ID。
        *   由于工作流需要调用飞书 Webhook，您需要创建一个凭证来存储您的 Webhook URL。
        *   1. 在 n8n 的 `Credentials` 页面，点击 `Add credential`。
        *   2. 搜索并选择 `HTTP Header Auth` (一个通用的凭证类型，因为没有专门的飞书凭证)。
        *   3. 在 `Name` 字段中，输入 `Lark`。在 `Value` 字段中，填入您的飞书群机器人的 Webhook 地址。
        *   4. 保存凭证。然后，您需要在 `Send Lark Notification` 节点的 `URL` 字段中，手动将 `YOUR_LARK_WEBHOOK_URL` 替换为 `{{$credentials.Lark.value}}`。
        *   您可以参考[飞书官方文档](https://open.feishu.cn/document/ukTMukTMukTM/ucTM5YjL3ETO24yNxkjN)来创建一个机器人并获取 Webhook 地址。

3.  **激活工作流**: 保存所有更改后，点击 n8n 界面右上角的 “Active” 开关来激活工作流。

## 🔧 自定义

- **修改后备城市**：如果你想修改无法自动定位时的后备城市，请修改 `Set Fallback Location` 节点中的 `location` 参数值。
- **修改定时**: 在 `Schedule Trigger` 节点中，您可以修改 `Cron Time` 参数来调整工作流的执行时间。例如，`0 9 * * *` 表示每天早上 9:00 执行。
