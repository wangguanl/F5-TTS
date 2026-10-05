# 运行命令

- 项目：F5-TTS（语音克隆 / TTS Gradio）
- 生成时间：2026-09-08
- 运行方式：直接运行（uv + 本地 editable 安装）
- 硬件评估：**满足** — 官方未要求 >16GB；Base 推理通常数 GB 级显存。本机 RTX 4080 16GB，启动前约占用 1.3GB、剩余约 15GB；系统内存 31.8GB、E 盘空闲充足。

## 环境准备

```powershell
# ffmpeg（本机已有，会话级加入 PATH）
$env:Path = "E:\Programs\ffmpeg-master-latest-win64-gpl\bin;$env:Path"

# Hugging Face 官方超时，使用镜像
$env:HF_ENDPOINT = "https://hf-mirror.com"

# 虚拟环境 + CUDA PyTorch（README 示例 cu128）
cd E:\AI\local-voice\F5-TTS
uv venv --python 3.11
.\.venv\Scripts\Activate.ps1
uv pip install torch==2.8.0+cu128 torchaudio==2.8.0+cu128 --extra-index-url https://download.pytorch.org/whl/cu128
uv pip install -e .
```

## 启动

- 推荐：`pwsh -NoProfile -File .\start.ps1`
- 说明：单服务 `webui`，无参直接启动（跳过菜单）。Docker：`-Mode docker`。
- 等价手动命令：

```powershell
$env:Path = "E:\Programs\ffmpeg-master-latest-win64-gpl\bin;$env:Path"
$env:HF_ENDPOINT = "https://hf-mirror.com"
uv run f5-tts_infer-gradio --host 0.0.0.0 --port 7860
```

Docker 备选（未默认）：`pwsh -NoProfile -File .\start.ps1 -Mode docker`

## 验证

1. Gradio：http://127.0.0.1:7860 返回 HTTP 200（已验证）。
2. CLI 烟雾：5-tts_infer-cli 官方英文示例已生成 `outputs/cli_smoke.wav`（约 3s 推理）。
3. 启动后显存约 2.9–4GB，无 CUDA OOM。

## 备注

- 端口：`start.ps1` 从 7860 起占用则 +1 顺延。
- 镜像：`HF_ENDPOINT=https://hf-mirror.com`；PyPI 直连可用；PyTorch wheel 用 `download.pytorch.org`（短超时曾失败，安装时加长超时）。
- 首次启动会自动从 HF 拉 F5-TTS / Vocos 等权重。
- Voice-Chat 会再加载 Qwen2.5-3B，显存压力更大；基础 TTS 优先验证。
- `nvcc` 不在 PATH（全局 CUDA Toolkit 未进 PATH），推理用驱动 + torch 轮子即可，不必装进项目。
- 本文件与 `start.ps1` 为本机运行记录，默认不提交 git。