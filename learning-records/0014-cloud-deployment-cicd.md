# 学习记录：L14 — 云部署与 CI/CD 流水线

## 核心收获

1. **云服务器选型与初始化**：
   - RAG 服务推荐 2C8G 起步（Chroma + Redis + API 进程合计约 4-6GB 内存）
   - 安全组只放行 22/80/443，8000/6379 等内部端口绝不暴露
   - 服务器环境初始化标准化步骤：Docker 安装（阿里云镜像加速）→ UFW 防火墙 → 系统优化（swappiness、日志限制）

2. **从本地到云端的三种部署方案**：
   - 方案 B（直接构建）：代码传到服务器，`docker compose up -d --build`——最简单，适合初学
   - 方案 A（镜像仓库）：本地/CI 构建→推送 ACR→服务器 pull——生产标准做法，支持 CI/CD
   - 跨平台构建陷阱：Mac M 系列（ARM）→ 服务器（x86）必须指定 `--platform linux/amd64`
   - 阿里云 ACR 与 ECS 同地域可走内网，传输速度极快

3. **HTTPS 配置完整流程**：
   - 域名 DNS A 记录解析到服务器 IP
   - Let's Encrypt + Certbot 申请免费证书（HTTP-01 验证需要 80 端口可达）
   - Nginx HTTPS 配置：TLSv1.2/TLSv1.3、HSTS、安全头部、关闭 API 文档
   - 生产环境必须配置自动续期（systemd timer 代替 cron），每天检查两次
   - `certbot renew --dry-run` 测试续期是否正常

4. **GitHub Actions CI/CD 流水线**：
   - 完整 workflow：代码 push → 测试（pytest）→ 构建镜像（docker buildx）→ 推送 ACR → SSH 部署
   - `needs` 关键字保证部署在构建成功后执行，测试失败则阻断整个流水线
   - GitHub Secrets 管理敏感信息（SSH 密钥、ACR 密码、API Key），绝不硬编码在 workflow 文件中
   - appleboy/ssh-action 是最常用的 SSH 远程执行 Action

5. **蓝绿部署与回滚**：
   - 核心原理：两套环境（蓝/绿）同时运行，Nginx 切换指向实现零停机
   - 通过 Nginx upstream 组 + sed 替换 + nginx -s reload 实现瞬时切换
   - 资源代价：内存翻倍（两套 RAG API 实例），但 Chroma 和 Redis 可共享
   - 简化方案：利用镜像 tag（如 `:latest` + `:$git_sha`）实现快速回滚

6. **云端托管服务 vs 自建**：
   - 适应"12 Factor App"后端服务原则——通过配置切换（Chroma↔Milvus）对应用透明
   - 前期自建省钱可控，生产迁移到托管服务只需改配置
   - 阿里云 DashVector（Milvus 托管）和阿里云 ES 是国内主要选择

## 理解转变

- 过去以为部署就是"把代码放到服务器上跑"，现在理解部署是一个**完整的自动化工程**——环境初始化、配置管理、安全防护、CI/CD、回滚策略、监控告警缺一不可
- 安全组 + UFW 的双层防护让人从"靠运气"变成"靠设计"——即使一层失效还有另一层兜底
- CI/CD 最核心的价值不是"自动化"而是"**让错误代码永远到不了生产**"——测试阶段失败比生产事故好 100 倍
- Let's Encrypt 的自动化证书管理改变了 SSL 的运维模式——从"每年手动续一次"变成"配置好就不用管"

## 后续关联

- L15（系统监控与可观测性）将基于本课部署的云上服务，搭建 Prometheus + Grafana 监控体系，采集本课配置的 JSON 日志到 Loki
- L17（成本控制）将对本课的云端资源（ECS、ACR、托管服务）做成本分析
- L16（安全策略）将扩展本课的安全防护：RBAC 权限、数据脱敏、API Key 管理

## 为什么学这个

没有本课的云部署和 CI/CD，你的 RAG 系统始终只是"能跑在你电脑上的代码"。部署到云端并实现自动发布，是 RAG 系统从个人项目变成团队可用的企业级服务的决定性一步。而这其中每一步——安全配置、HTTPS、CI/CD、蓝绿部署——都是生产环境的必修课，不是选修课。
