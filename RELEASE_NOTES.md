脉络 Mailuo Community 首个 GitHub 测试发行：0.1.0-beta.1。

将安排、执行与总结放在同一个个人工作台。支持x86_64/amd64私有部署，镜像由阿里云公开仓库提供，无需仓库账号。

下载docker-compose.yml，放入项目目录，通过SSH登录Linux服务器，在该目录执行docker compose up -d，访问http://服务器IP:9000。从init日志取得初始化码并自定义账号密码。

数据位于YAML旁的data/、secrets/，不是Docker命名卷。旧命名卷实例先备份迁移，不能直接启动空目录。默认开发者信息订阅可取消，确认后才登记最小实例信息；不会自动升级。

仅提供社区发行配置和文档，应用源码未公开，不宣称开源。请先使用非关键数据试用并备份。

附件包含：独立Compose、文档安装包、SHA256校验文件。完整说明见仓库README与docs。

部署文档现已统一为通用Linux服务器命令行操作，镜像版本和YAML保持不变；最新部署指南见https://github.com/zeefeng/mailuo/blob/main/docs/INSTALL.md。
