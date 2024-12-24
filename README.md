# GitHub Action: 自动推送时间触发任务

这是一个基于时间触发的自动推送 GitHub Action。通过该 Action，你可以根据预设时间自动执行任务，例如推送数据或运行脚本。

## 功能特点
- **时间触发**：支持通过 CRON 表达式设定触发时间。
- **灵活配置**：支持 Python 环境配置和依赖安装。
- **多时区支持**：可设置目标时区，适配不同地区需求。

## 使用方法

1. **创建仓库或使用现有仓库**
   在 GitHub 中准备一个存放项目代码的仓库。

2. **编写工作流文件**
   在项目的 `.github/workflows/` 目录下，创建一个 YAML 文件，例如 `time-push.yml`。

   以下是完整的工作流文件：

   ```yaml
   name: time-push

   on:
     workflow_dispatch:
     schedule: 
       # 代表国际标准时间 0 点 0 分，北京时间需 +8 小时，即中午 12 点运行
       - cron: '0 0 * * *'

   jobs:
     # 将工作流程中运行的所有作业组合在一起
     build:
       # 定义名为 build 的作业，子键将定义作业的属性
       runs-on: ubuntu-latest 
       steps:
         - uses: actions/checkout@v2

         - name: Set up Python 3.9
           uses: actions/setup-python@v2
           with:
             python-version: 3.9

         - name: Set timezone
           run: |
             cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

         - name: install pip packages
           run: |
             python -m pip install --upgrade pip
             pip3 install -r requirements.txt

         - name: time-push
           run: |
             python3 main.py
   ```

3. **主要配置说明**
- **触发条件**：
  - `workflow_dispatch`：支持手动触发工作流。
  - `schedule`：使用 CRON 表达式设定每日运行时间，默认设置为 UTC 时间凌晨 0 点，北京时间为中午 12 点。

- **运行环境**：
  - 使用 `ubuntu-latest` 作为运行环境。
  - 安装并设置 Python 3.9 环境。
  
- **时区设置**：
  - 通过 `cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime` 设置时区为中国标准时间（CST）。

- **依赖安装**：
  - 使用 `pip` 和 `requirements.txt` 安装所需 Python 包。

4. **运行与测试**
- 手动触发：在 GitHub Actions 界面中点击 **Run workflow** 手动触发任务。
- 自动触发：根据 CRON 表达式设定的时间自动运行。

5. **调试和日志**
- 查看运行日志：进入 GitHub Actions 界面，选择最近一次运行记录，查看输出日志以排查问题。
- 调试 CRON 表达式：[CRON 表达式生成器](https://crontab.guru/)。

## 注意事项
- 确保 `requirements.txt` 文件包含所有依赖的 Python 包。
- 如果 `main.py` 涉及敏感信息（如 API 密钥），建议使用 GitHub Secrets 存储并引用。

## 参考
- [GitHub Actions 官方文档](https://docs.github.com/en/actions)
- [CRON 表达式生成器](https://crontab.guru/)
