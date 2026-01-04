说明：保持 Codespace 在非工作时间也能被定期唤醒

操作步骤：

1. 在 GitHub 上创建一个 PAT（Personal Access Token）：
   - 打开 Settings → Developer settings → Personal access tokens → Tokens (classic) 或 新令牌页面
   - 需要勾选 `codespaces`（和 `repo` 如果需要访问私有仓库）权限

2. 在仓库的 Settings → Secrets and variables → Actions 中新增 Secret：
   - 名称：`CODESPACE_PAT`
   - 值：上一步生成的 PAT

3. 检查并固定端口：
   - 打开 Codespace，点击左下角 `Ports` 面板，找到 80 端口，设置为 `Public` 并 `Pin`（固定）。

4. 工作原理：
- 我们添加了一个定时 GitHub Action（`.github/workflows/wake-codespace.yml`），每 **15 分钟** 尝试启动你的 Codespace（或创建一个新的 Codespace，如果尚不存在）；你也可以在 Actions 页面手动触发该 workflow（Run workflow）。
   - `.devcontainer/devcontainer.json` 中添加了 `forwardPorts: [80]` 和 `postStartCommand`，当 Codespace 被唤醒时，会自动尝试运行 `docker compose -f standAlone/docker-compose.yml up -d` 来启动站点。

注意：
- 该方法为权宜之计，不能阻止 Codespace 被完全休眠（平台策略），但能在被唤醒后自动启动服务。若需 24/7 可用，请考虑迁移到 VPS / 云服务。
