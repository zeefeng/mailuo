# 极空间安装脉络

## 准备

- 支持 Docker Compose 的极空间，CPU 为 x86_64 / amd64（64 位 Intel/AMD）。
- 联网访问阿里云公开镜像仓库，无需登录或手工导入镜像。
- 下载仓库根目录的 `docker-compose.yml`，放到自己的 `docker/mailuo` 文件夹。

## 创建项目

在极空间 Docker → Compose 中，从你保存的 `docker-compose.yml` 创建/部署项目。不同系统版本入口名称可能不同。选择这个现有文件，避免在另一个目录再生成一份配置。

默认端口 **9000**。如被占用，修改配置中的 `${APP_PORT:-9000}` 为需要的端口，例如 `${APP_PORT:-9001}`，容器内部端口 `3000` 不改。

初始化顺序：`init → postgres → migrate → app`。`init` 与 `migrate` 执行完成后退出码为0是正常结果。`app`、`postgres` 应运行且健康。

## 设置账号

查看 init 容器日志，找到 `Mailuo Community setup code (keep private):` 后面的初始化码。不要公开此码。

打开 `http://你的设备IP:9000`，填写初始化码，自定义账号和密码。没有统一默认账号密码。首次设置完成后初始化入口关闭，重启不会重置账号。

init 日志可能显示固定示例端口8080，实际请以你YAML的9000或修改后的宿主机端口为准。

初始化中的版本提醒/开发者公告默认勾选，可取消；创建账号时保存选择，详情见[通知说明](NOTIFICATIONS.md)。

## 数据在哪里

本配置全部使用相对目录：`./data`、`./secrets`，不使用命名卷或写死的绝对路径。目录随 Compose 配置文件位置解析。

部署后应看到文件夹：

```text
mailuo/
├── docker-compose.yml
├── data/
└── secrets/
```

具备 SSH 权限时可只读核验（容器名按你的项目实际名称替换）：

```bash
sudo docker inspect mailuo-postgres-1 --format '{{json .Mounts}}'
```

应看到 `Type: bind`，Source 指向项目下的data/secrets。如果是 `Type: volume`，说明仍在使用旧命名卷配置或旧容器，需要核对实际文件并重新部署；只点重启不改变挂载。

数据目录有严格权限属正常现象，不要为了浏览而递归改成777。

## 普通 Linux Docker

进入YAML所在目录执行 `docker compose up -d`。极空间若SSH中提示找不到compose子命令，可使用它的Compose界面，无需通过手工删文件修复。
