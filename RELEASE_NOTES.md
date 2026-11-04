脉络 Mailuo Community 0.1.0-beta.2。

本次实际提供新版linux/amd64 Docker镜像，包含脑图概览、脑图内悬浮编辑、阶段/同级任务/多层子任务、分支折叠、25%–200%缩放及账号全局壁纸与自定义上传。

新安装：下载docker-compose.yml，在Linux服务器的项目目录执行docker compose up -d，默认9000端口。保留YAML旁的data和secrets目录。

beta.1升级：先停止app写入，备份数据库、secrets及旧YAML；在原项目目录使用匹配的beta.2配置，保留原项目名、端口、HTTPS及存储设置，再拉取镜像并执行增量迁移。不使用reset/db push，不删除data/secrets。升级后检查原账号、事项和任务。多层子任务投入使用后不应直接退回旧镜像。

详细安装：https://github.com/zeefeng/mailuo/blob/main/docs/INSTALL.md
升级与备份：https://github.com/zeefeng/mailuo/blob/main/docs/MAINTENANCE.md#升级到-beta2
功能说明：https://github.com/zeefeng/mailuo/blob/main/docs/FEATURES.md

当前仍为免费社区测试版，仅linux/amd64，不支持ARM；应用源码未公开。附件提供Compose、文档包及SHA256校验文件。旧beta.1发行保留。
