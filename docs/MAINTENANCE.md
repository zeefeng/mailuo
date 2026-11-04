# 备份与升级

## 备份

同时保留数据库与secrets。只保留docker-compose.yml不能恢复事项或账号。

普通Docker Compose终端可执行：

```bash
umask 077
docker compose exec -T postgres pg_dump -U mailuo mailuo > mailuo.sql
docker compose exec -T app tar -C /run/mailuo -czf - . > mailuo-secrets.tar.gz
```

将备份复制到另一处安全位置，不要提交GitHub或公开分享。在服务器的YAML目录执行；需要Docker权限时为docker命令加sudo。

如要直接复制data目录，必须先停止app和postgres，保留文件权限复制data和secrets；不要将运行中的数据库文件直接复制后当作一致性备份。

## 升级到 beta.2

本步骤适用于使用当前相对目录挂载的`0.1.0-beta.1`实例。请在原项目目录操作，保留原项目名、`data`和`secrets`。如果使用自定义端口、HTTPS、反向代理、环境变量或不同挂载，先把原配置与新版比较，保留自己的配置，不要直接覆盖。

先停止应用写入并备份（数据库保持运行）：

```bash
umask 077
docker compose stop app
cp docker-compose.yml docker-compose.before-beta2.yml
docker compose exec -T postgres pg_dump -U mailuo -d mailuo -Fc > mailuo-before-beta2.dump
tar -czf mailuo-secrets-before-beta2.tar.gz secrets
curl -fL https://github.com/zeefeng/mailuo/releases/download/v0.1.0-beta.2/docker-compose.yml -o docker-compose.beta2.yml
```

核对新旧配置后更新。以下假定主配置文件名为`docker-compose.yml`；原文件名不同时按实际调整，并始终使用同一个Compose项目。

```bash
cp docker-compose.beta2.yml docker-compose.yml
docker compose config --quiet
docker compose pull
# 重新运行一次性初始化与迁移，现有密钥不会重建。
docker compose up -d --force-recreate init migrate app
docker compose ps -a
docker compose logs --tail=100 migrate app
curl -f http://127.0.0.1:9000/api/health
```

`init`和`migrate`正常退出0，`app`与`postgres`应为healthy；健康接口版本应为`0.1.0-beta.2`。自定义端口请修改上面的9000。登录原账号，检查事项、任务、计划时间，再使用脑图/列表切换和全局背景。

本次新增日历投递/回传、任务父子关系及个人背景的增量迁移，原任务保持顶层，旧账号默认主题不变。个人上传图片存数据库，随数据库备份，不依赖应用容器文件。

不得执行`migrate reset`、`db push`或删除数据目录。若拉取或迁移失败，保留备份及数据，先检查日志；不要继续录入数据。完成升级并使用父子任务后，旧版会平铺任务且不能正确维护子树，不应直接切回beta.1。数据库恢复应在停机、核对备份及明确接受恢复点之后进行，不能用旧备份覆盖升级后的新增数据。

## 旧命名卷迁移

旧版可能使用mailuo_data、mailuo_secrets命名卷。新配置使用相对目录；更换YAML不会自动搬迁旧卷。

需要保留旧数据时：先备份并停机，确认真实Mounts来源，再保留权限复制数据库和密钥两份内容至新data/secrets目录，最后重新部署。切勿直接用空目录启动后误以为旧数据已丢失。

## 停止和卸载

停止/删除容器与删除数据是两件事。目录挂载的数据仍在项目data/secrets下。只有确认不要账号和事项后，才删除自己的项目数据；不要直接删除Docker的volumes总目录或metadata.db。
