# System Requirements

Hardware, software, and network requirements for deploying Dine-In Order Accuracy.

---

## Hardware Requirements

### Development / Single Station

| Component | Requirement                                    |
| --------- | ---------------------------------------------- |
| CPU       | 8+ cores                                       |
| RAM       | 16 GB                                          |
| GPU       | Intel Arc A770 (16 GB) or equivalent Intel GPU |
| Storage   | 50 GB SSD                                      |

### Production

| Component | Requirement                                       |
| --------- | ------------------------------------------------- |
| CPU       | 16+ cores                                         |
| RAM       | 32 GB                                             |
| GPU       | Intel Data Center GPU (for concurrent validation) |
| Storage   | 200 GB NVMe SSD                                   |

**GPU VRAM guidance:** The Qwen2.5-VL-7B INT8 model requires ~6–8 GB of VRAM.

## Software Requirements

### Operating System

Ubuntu 22.04 LTS is the validated platform (matches the `python:3.13-slim` base image running on the host GPU driver stack).

### Container Runtime

| Software       | Minimum Version |
| -------------- | --------------- |
| Docker Engine  | 24.0            |
| Docker Compose | V2 (2.20+)      |

### GPU Drivers

Intel GPU drivers must be installed from <https://dgpu-docs.intel.com/driver/installation.html>. Verify the GPU is accessible to Docker:

```bash
ls /dev/dri/
docker run --rm --device /dev/dri intel/openvino_dev:latest python3 -c \
  "from openvino.runtime import Core; print(Core().available_devices)"
```

Expected output includes `GPU`.

---

## Network Requirements

### Port Configuration

| Service           | Port | Purpose                      |
| ----------------- | ---- | ---------------------------- |
| Gradio UI         | 7861 | Web interface                |
| REST API          | 8083 | FastAPI endpoints            |
| OVMS VLM          | 8002 | Model inference (external)   |
| Semantic Service  | 8081 | Semantic matching (external) |
| Metrics Collector | 8084 | System metrics               |
