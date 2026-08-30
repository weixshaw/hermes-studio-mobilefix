# hermes-studio-mobilefix

基于 [EKKOLearnAI/hermes-studio](https://github.com/EKKOLearnAI/hermes-studio) v0.7.12（commit c990be4）的私有修补构建，只改了一处：

## 修复内容：移动端玻璃模式抽屉与内容混叠

`packages/client/src/App.vue`：开启自定义背景图（玻璃/毛玻璃效果）时，桌面端侧栏被改为 72% 半透明+blur。手机端（≤768px）这些侧栏是**悬浮抽屉**，半透明导致抽屉菜单与下层聊天内容直接混叠——菜单看起来是透明的、和背景文字重叠、难以阅读和点选。

补丁：≤768px 时抽屉类侧栏（主侧栏、会话列表、配置侧栏、群聊房间栏、工作流侧栏）强制使用不透明背景、关闭 backdrop-filter；桌面端玻璃效果不变。

已用 Playwright 手机视口（390×844）验证：亮/暗主题下抽屉均恢复纯不透明。

## 在 NAS 上使用

你的 `docker-compose.yml` 里把 Web UI 镜像换成本构建：

```bash
WEBUI_IMAGE=ghcr.io/weixshaw/hermes-studio-mobilefix:latest
```

然后：

```bash
docker compose pull hermes-webui
docker compose up -d hermes-webui
```

数据目录、端口（6060）、token 等与官方镜像完全一致，无需其他改动。上游发新版后，把补丁 rebase 过来重新构建即可。

## 构建

`.github/workflows/docker.yml` 在 push 时自动构建 linux/amd64 + linux/arm64 并推送 ghcr.io。
