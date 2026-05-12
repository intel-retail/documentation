# System Requirements

Hardware, software, and network requirements for deploying Take-Away Order Accuracy.

---

## Hardware Requirements

### Development / Single-Station

| Component   | Specification                                  |
| ----------- | ---------------------------------------------- |
| **CPU**     | 8+ cores                                       |
| **RAM**     | 16 GB                                          |
| **GPU**     | Intel Arc A770 (16 GB) or equivalent Intel GPU |
| **Storage** | 50 GB SSD                                      |

### Production / Multi-Station

| Component   | Specification                                                  |
| ----------- | -------------------------------------------------------------- |
| **CPU**     | 16+ cores                                                      |
| **RAM**     | 32 GB                                                          |
| **GPU**     | Intel Data Center GPU Max (48 GB) — for 4+ concurrent stations |
| **Storage** | 200 GB NVMe SSD                                                |

**GPU VRAM guidance:** The Qwen2.5-VL-7B INT8 model requires ~6–8 GB of VRAM. Reserve at least 8 GB for the VLM; additional VRAM headroom allows more concurrent requests.

---

## Software Requirements

### Operating System

Ubuntu 22.04 LTS is the validated platform (matches the `intel/dlstreamer:2025.2.0-ubuntu22` base image).

### Container Runtime

| Software       | Minimum Version |
| -------------- | --------------- |
| Docker Engine  | 24.0            |
| Docker Compose | V2 (2.20+)      |

### GPU Drivers

Intel GPU drivers must be installed from [packages.intel.com](https://packages.intel.com). Verify the GPU is accessible to Docker:

```bash
ls /dev/dri/
docker run --rm --device /dev/dri intel/openvino_dev:latest python3 -c \
  "from openvino.runtime import Core; print(Core().available_devices)"
```

Expected output includes `GPU`.

---

## Network Requirements

### Port Configuration

| Service            | Port | Purpose                         |
| ------------------ | ---- | ------------------------------- |
| Order Accuracy API | 8000 | REST API                        |
| OVMS VLM           | 8001 | Model inference                 |
| Gradio UI          | 7860 | Web interface                   |
| MinIO API          | 9000 | S3-compatible storage           |
| MinIO Console      | 9001 | Storage admin UI                |
| Semantic Service   | 8080 | Semantic matching               |
| RTSP Streamer      | 8554 | Video streaming (parallel mode) |

### RTSP Requirements (Parallel Mode)

| Requirement | Specification     |
| ----------- | ----------------- |
| Protocol    | RTSP/RTP over TCP |
| Codec       | H.264             |
| Resolution  | 720p–1080p        |
| Frame Rate  | 15–30 FPS         |
