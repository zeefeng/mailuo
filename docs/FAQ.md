# 常见问题

**为什么只有一个YAML，没有镜像压缩包？**

Compose会从阿里云公开仓库自动拉取固定版本镜像，无需手工导入或登录仓库。

**app、init和migrate使用相同版本吗？**

app/init使用`0.1.0-beta.1-amd64`，migrate使用`0.1.0-beta.1-migrate-amd64`，数据库使用`postgres-16-alpine-amd64`。不要只更新其中一项到不匹配版本。

**init或migrate退出了？**

退出码0表示完成。非0则检查对应日志；不要跳过迁移依赖直接启动app。

**containerd.sock超时？**

若日志指向服务器的containerd socket，先检查服务器Docker服务状态；这与数据库密码或应用账号不同，不应通过删除数据库来处理。

**端口被占用？**

修改宿主机端口9000，容器内部端口3000保持。详见安装说明。

**登录后马上跳回登录页？**

HTTP局域网访问保持`MAILUO_COOKIE_SECURE=0`；只有正确配置HTTPS后才设为1。不要将HTTP端口与HTTPS Cookie配置混用。

**数据没有出现在YAML旁？**

确认实际运行的配置包含`./data`、`./secrets`，并在该YAML目录执行docker compose up -d重新部署。查看Mounts的Type和Source，不要只看文件管理器截图。旧容器必须重建。

**我能使用ARM设备吗？**

当前仅x86_64/amd64，不提供ARM。

**遇到错误如何反馈？**

提交版本、CPU架构、Linux发行版、Docker/Compose版本及容器状态、复现步骤和脱敏日志。不要上传初始化码、密码、secrets、数据库或私人事项截图。

已知限制：复盘页此前出现过React水合警告，恢复后可显示，仍待进一步处理；Safari/HarmonyOS触摸及输入法交互需继续真机反馈。当前为测试版，没有正式SLA承诺。
