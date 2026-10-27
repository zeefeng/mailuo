# Linux 服务器部署指南

## 1. 准备服务器

- Linux x86_64 / amd64（64位Intel/AMD），当前不支持ARM。
- 已安装Docker Engine和Docker Compose插件，服务器能访问GitHub及阿里云公开镜像仓库。
- 示例使用当前有Docker权限的账号；若提示Docker socket权限不足，在每条docker命令前加sudo。

先检查：

```bash
uname -m
docker --version
docker compose version
```

CPU应显示x86_64。Docker安装方式请按[Docker官方Linux安装说明](https://docs.docker.com/engine/install/)选择对应系统。若没有compose子命令，先安装Compose插件，再继续。

## 2. 下载配置并启动

SSH登录服务器，选择一个新的空目录。以下以当前用户的mailuo目录为例：

```bash
mkdir -p ~/mailuo
cd ~/mailuo
```

确认该目录没有已有部署配置或数据，再下载；下面命令不会覆盖已有docker-compose.yml：

```bash
if [ -e docker-compose.yml ]; then
  echo 'docker-compose.yml 已存在，请先确认是否为已有部署，不要覆盖。'
else
  curl -fL https://github.com/zeefeng/mailuo/releases/download/v0.1.0-beta.1/docker-compose.yml -o docker-compose.yml
fi
```

检查配置、拉取镜像并启动：

```bash
docker compose config --quiet
docker compose pull
docker compose up -d
docker compose ps -a
```

默认端口9000，容器内部端口3000。如9000已占用，在YAML中把`${APP_PORT:-9000}`改为`${APP_PORT:-9001}`等可用端口，再启动。

初始化顺序为init → postgres → migrate → app。init与migrate退出码0代表完成；app、postgres应运行且健康。失败时先查看日志，不要跳过迁移依赖：

```bash
docker compose logs --tail=100 app postgres migrate
```

## 3. 创建自己的账号

```bash
docker compose logs init
```

找到`Mailuo Community setup code (keep private):`后的初始化码。不要公开此码。

在浏览器打开`http://服务器IP:9000`，填写初始化码，自定义账号密码。没有统一默认账号密码。首次设置后初始化入口关闭，重启不重置账号。

init日志中的8080是固定示例，实际以YAML的9000或修改后的宿主机端口为准。初始化的版本提醒/开发者公告默认勾选，可取消；创建账号时保存选择，见[订阅说明](NOTIFICATIONS.md)。

首次设置请限制在可信访问范围；公网长期访问建议配置HTTPS反向代理并把MAILUO_COOKIE_SECURE设为1。HTTP直连则保持0，否则登录Cookie不能正常保存。

## 4. 数据和配置在一起

本配置使用./data、./secrets相对目录，不使用命名卷或写死的绝对路径：

```text
mailuo/
├── docker-compose.yml
├── data/
└── secrets/
```

Compose根据YAML文件所在位置解析目录；不要在不同目录使用另一份配置。修改挂载后用up -d重新创建容器，仅restart不改变挂载。

只读核验真实挂载：

```bash
docker inspect "$(docker compose ps -q postgres)" --format '{{json .Mounts}}'
```

若需要sudo，内部命令也要使用sudo：

```bash
sudo docker inspect "$(sudo docker compose ps -q postgres)" --format '{{json .Mounts}}'
```

应为Type=bind，Source分别指向项目/data和/secrets。若为volume，请核对旧配置和旧容器。目录权限严格是正常现象，不要递归改成777。

## 5. 常用维护命令

在YAML所在目录执行：

```bash
docker compose ps -a
docker compose logs --tail=100 app
docker compose down
docker compose up -d
```

停止容器不删除data/secrets目录。升级、备份和旧命名卷迁移见[维护说明](MAINTENANCE.md)。
