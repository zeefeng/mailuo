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

## 升级

1. 备份数据库、secrets与现用YAML。
2. 在原项目中更新镜像标签及匹配的版本信息，保留data、secrets目录。
3. 重新拉取镜像并重建项目，migrate执行增量迁移。
4. 确认服务健康，并检查原账号、事项是否保留。

不要执行migrate reset或db push，也不要删除data/secrets来“修复升级”。数据库迁移后不能默认认为切回旧镜像就一定兼容，应根据具体版本说明恢复。

本次0.1.0-beta.1是对前期测试beta.5的重新编号，语义版本比较不会将其视为beta.5的新版本，需要手动更新；原镜像标签保留。

## 旧命名卷迁移

旧版可能使用mailuo_data、mailuo_secrets命名卷。新配置使用相对目录；更换YAML不会自动搬迁旧卷。

需要保留旧数据时：先备份并停机，确认真实Mounts来源，再保留权限复制数据库和密钥两份内容至新data/secrets目录，最后重新部署。切勿直接用空目录启动后误以为旧数据已丢失。

## 停止和卸载

停止/删除容器与删除数据是两件事。目录挂载的数据仍在项目data/secrets下。只有确认不要账号和事项后，才删除自己的项目数据；不要直接删除Docker的volumes总目录或metadata.db。
