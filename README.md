这是您需要的GitHub Actions工作流文件，它会拉取两个Docker镜像（原版和中文版）并保存为可上传的tar包。

```yaml
name: Pull & Save OpenClaw Images

on:
  workflow_dispatch:

jobs:
  pull-and-save:
    runs-on: ubuntu-latest
    steps:
      - name: Pull OpenClaw (Original)
        run: docker pull ghcr.io/openclaw/openclaw:latest

      - name: Save OpenClaw Original to tar
        run: docker save ghcr.io/openclaw/openclaw:latest -o openclaw.tar

      - name: Pull OpenClaw (Chinese)
        run: docker pull ghcr.io/1186258278/openclaw-zh:latest

      - name: Save OpenClaw Chinese to tar
        run: docker save ghcr.io/1186258278/openclaw-zh:latest -o openclaw-zh.tar

      - name: Upload Original Image Artifact
        uses: actions/upload-artifact@v4
        with:
          name: openclaw-docker-image
          path: openclaw.tar

      - name: Upload Chinese Image Artifact
        uses: actions/upload-artifact@v4
        with:
          name: openclaw-zh-docker-image
          path: openclaw-zh.tar
```

使用方法

1. 创建文件：将上述内容保存为 .github/workflows/pull-and-save.yml
2. 触发工作流：在GitHub仓库的Actions页面手动运行 Pull & Save OpenClaw Images
3. 下载镜像：运行完成后，在Summary页面下载生成的 openclaw-docker-image 和 openclaw-zh-docker-image 工件

导入和运行（您提供的命令）

导入镜像：

```bash
docker load -i openclaw.tar
docker load -i openclaw-zh.tar
```

查看已导入的镜像：

```bash
docker images
```

运行原版容器：

```bash
docker run -d \
  --name openclaw \
  --restart always \
  -p 18789:18789 \
  -v openclaw-data:/root/.openclaw \
  ghcr.io/openclaw/openclaw:latest
```

运行中文版容器（注意修改镜像名）：

```bash
docker run -d \
  --name openclaw-zh \
  --restart always \
  -p 18790:18789 \  # 使用不同端口避免冲突
  -v openclaw-zh-data:/root/.openclaw \
  ghcr.io/1186258278/openclaw-zh:latest
```

这样您就可以同时拥有原版和中文版的OpenClaw镜像了。
