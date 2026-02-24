# 自愈式家庭服务器 (Self-healing Home Server)

让 OpenClaw 管理你的家庭实验室或个人服务器。它可以监控服务、更新容器、清理磁盘空间，甚至在出现问题时尝试修复——所有这些都无需你的干预。

## 功能

1. **自动更新**：定期检查 Docker 容器更新。如果更新可用，它会拉取、重新创建并验证服务是否恢复正常。
2. **磁盘清理**：如果磁盘使用率 > 80%，OpenClaw 会运行 `docker system prune`，删除旧日志，并压缩备份。
3. **服务健康检查**：如果 Plex、Home Assistant 或你的个人网站宕机，OpenClaw 会尝试重新启动容器。如果失败，它会检查日志并将错误摘要发送给你。
4. **安全审计**：定期扫描开放端口或过时的包。

## 它是如何工作的

OpenClaw 作为服务器上的特权代理运行（或通过 SSH 访问）。

### Cron 计划表
```text
## Cron Schedule
Every 15 minutes:
- Check kanban board for in-progress tasks → continue work
Every hour:
- Monitor health checks (Gatus, ArgoCD, service endpoints)
- Triage Gmail (label actionable items, archive noise)
- Check for unanswered alerts or notifications
```

### 自愈逻辑 (伪代码)

```python
def check_service(service_name):
    status = run(f"docker inspect -f '{{.State.Status}}' {service_name}")
    if status != "running":
        log(f"{service_name} is down. Restarting...")
        run(f"docker restart {service_name}")
        
        # 等待并再次检查
        sleep(10)
        new_status = run(f"docker inspect -f '{{.State.Status}}' {service_name}")
        if new_status == "running":
            notify(f"{service_name} successfully restarted.")
        else:
            logs = run(f"docker logs --tail 50 {service_name}")
            notify(f"CRITICAL: {service_name} failed to restart. Logs: {logs}")
```

## 基础设施即代码 (IaC)

OpenClaw 还可以管理你的 `docker-compose.yml` 文件。
> 用户："添加 Uptime Kuma 到我的服务器。"
> OpenClaw：
> 1. 编辑 `docker-compose.yml` 添加服务定义。
> 2. 运行 `docker-compose up -d`。
> 3. 验证它是否在端口 3001 上响应。

## 为什么使用它

- **安心**：知道你的服务正在被监控和维护。
- **低维护**：不再需要手动 SSH 进去运行更新或清理磁盘。
- **弹性**：在大多数问题影响你之前自动修复它们。
