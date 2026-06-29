# V2Ray Subscription Aggregator & OSS Deployer

这是一个基于 **GitHub Actions** 的自动化工具，旨在收集、去重、加密并将 V2Ray 节点订阅托管至 **Oracle Cloud Object Storage (OSS)**。

## 🚀 项目作用
本项目解决了手动更新节点订阅的繁琐过程。通过简单的文件管理和 GitHub 自动化流，实现节点从本地/仓库到云端对象存储的无缝同步。

## ✨ 核心功能
* **多源合并**：自动扫描并串联项目根目录下所有的 `.txt` 节点文件。
* **智能清洗**：
    * **强制换行**：解决不同文件合并导致的备注（Remarks）与协议头粘连问题。
    * **协议过滤**：仅保留 `vless`、`vmess`、`ss`、`trojan`、`hysteria2` 等标准节点链接。
    * **全局去重**：自动删除重复的节点行，保持订阅文件精简。
* **Base64 编码**：生成符合 V2Ray 客户端标准的单行（`-w 0`）加密订阅文件。
* **自动化部署**：利用 `rclone` 将生成的 `sub-test.txt` 自动推送到 Oracle OSS 指定存储桶。

## 🛠️ 配置与使用

### 1. 准备工作
* **Oracle OSS**: 在 Oracle Cloud 中创建一个对象存储桶（Bucket），获取 `Access Key` 和 `Secret Key`。
* **Rclone 配置**: 在本地安装并配置好 `rclone`。确保你的配置文件中包含指向 Oracle OSS 的 S3 兼容配置（建议命名为 `monk`）。

### 2. 设置 GitHub Secrets
在 GitHub 仓库的 `Settings > Secrets and variables > Actions` 中添加以下变量：
* `RCLONE_CONFIG_CONTENT`: 填写你本地 `rclone.conf` 的**完整文本内容**。

### 3. 使用方法
1.  将你的节点链接（每行一个）放入以 `.txt` 结尾的文件中，放置在仓库根目录。
2.  `git push` 提交代码至 GitHub。
3.  GitHub Actions 会自动触发工作流：
    * 安装并配置 Rclone 环境。
    * 提取所有 `.txt` 中的合法节点并去重。
    * 生成加密后的 `sub-test.txt`。
    * 同步至 Oracle OSS。

## 📂 文件结构
* `*.txt`: 存放原始节点链接。
* `.github/workflows/update_sub.yml`: 自动化任务定义文件。
* `sub-test.txt`: (生成项) 最终上传到 OSS 的加密订阅文件。

## ⚠️ 注意事项
* 请确保 Oracle OSS 桶的权限已正确配置（建议使用 **PAR** 或 **Public Read**），以便客户端能够通过 URL 拉取。
* 不要将包含明文节点或密钥的配置文件直接提交到仓库，务必使用 Secrets 功能。
