# Performance Testing & Benchmarking

Test your Loss Prevention and Automated Self-Checkout pipeline performance on various hardware configurations. This guide covers everything from quick performance checks to comprehensive system capacity testing.

## Quick Start (5 minutes)

**Goal**: Run a basic performance test to verify your system works correctly

### 1. Initialize Performance Tools
```bash
make update-submodules
```

### 2. Run Quick Benchmark  
```bash
make benchmark-quickstart
```

**What this does:**
- Tests GPU performance with 6 different loss prevention workloads
- Runs headless (no display needed)
- Uses pre-built Docker images for faster setup
- Automatically generates consolidated metrics

**Expected results**: You'll see FPS metrics and resource utilization for each workload.

## Understanding Performance Testing Types

### Basic Performance Testing

**Default Benchmark Command:**
```bash
make benchmark
```

**Configuration:**
- Single pipeline instance (`PIPELINE_COUNT=1`) 
- CPU-only processing (`WORKLOAD_DIST=workload_to_pipeline.json`)
- Standard camera setup (`CAMERA_STREAM=camera_to_workload.json`)
- No visual output (`RENDER_MODE=0`)

### Environment Variables Reference

| Category | Variable | Description | Common Values |
|----------|----------|-------------|---------------|
| **Display** | `RENDER_MODE` | Show/hide visual output | `0` (headless), `1` (visual) |
| **Performance** | `PIPELINE_COUNT` | Number of parallel pipeline instances | `1`, `2`, `4` (higher = more stress) |
| **Hardware** | `WORKLOAD_DIST` | Target processing hardware | `workload_to_pipeline_cpu.json`, `workload_to_pipeline_gpu.json`, `workload_to_pipeline_gpu-npu.json` |  
| **Camera Setup** | `CAMERA_STREAM` | Camera configuration | `camera_to_workload.json`, `camera_to_workload_full.json` |
| **Build** | `REGISTRY` | Use pre-built vs local images | `true` (faster), `false` (custom builds) |

| **Build** | `REGISTRY` | Use pre-built vs local images | `true` (faster), `false` (custom builds) |

## Workload Configuration Options

### Camera Stream Configurations

**Standard Setup** (`camera_to_workload.json`):
| Camera | Workloads |
|:-------|:----------|
| cam1 | items_in_basket + multi_product_identification |
| cam2 | hidden_items + product_switching |
| cam3 | fake_scan_detection |

**Full Workload Testing** (`camera_to_workload_full.json`):
| Camera | Workload |
|:-------|:---------|
| cam1 | items_in_basket |
| cam2 | hidden_items |
| cam3 | fake_scan_detection |
| cam4 | multi_product_identification |
| cam5 | product_switching |
| cam6 | sweet_heartening |

### Hardware Distribution Options

| Configuration | File | Best For |
|:--------------|:-----|:---------|
| **CPU Only** | `workload_to_pipeline_cpu.json` | Testing, development environments |
| **GPU Only** | `workload_to_pipeline_gpu.json` | Production, high performance |
| **Mixed GPU/NPU** | `workload_to_pipeline_gpu-npu.json` | Latest Intel hardware |
| **Heterogeneous** | `workload_to_pipeline_hetero.json` | Maximum performance across all hardware |
| **Default Mixed** | `workload_to_pipeline.json` | Balanced CPU/GPU/NPU distribution |

## Advanced Performance Testing (15-30 minutes)

### GPU Performance Testing
```bash
make benchmark WORKLOAD_DIST=workload_to_pipeline_gpu.json CAMERA_STREAM=camera_to_workload_full.json
```

### Multi-Pipeline Stress Testing
```bash
# Test with 2 parallel pipelines
make PIPELINE_COUNT=2 benchmark

# High stress test with 4 pipelines
make PIPELINE_COUNT=4 benchmark
```

### Custom Hardware Configuration
```bash
# Test heterogeneous workload distribution
make benchmark WORKLOAD_DIST=workload_to_pipeline_hetero.json CAMERA_STREAM=camera_to_workload_full.json REGISTRY=false
```

### Automated Self-Checkout Performance
```bash
# Object detection workload
CAMERA_STREAM=camera_to_workload_asc_object_detection.json WORKLOAD_DIST=workload_to_pipeline_asc_object_detection_gpu.json make benchmark

# Age verification workload  
CAMERA_STREAM=camera_to_workload_asc_age_verification.json WORKLOAD_DIST=workload_to_pipeline_asc_age_verification_gpu.json make benchmark
```

## Viewing Results

### Generate Consolidated Metrics
```bash
make consolidate-metrics
```

**Output**: `benchmark/metrics.csv` containing:
- FPS (frames per second) for each pipeline
- CPU/GPU/NPU utilization percentages  
- Memory usage statistics
- Power consumption data
- Latency measurements

### View Results
```bash
cat benchmark/metrics.csv
```

## Stream Density Testing (30+ minutes)

**Goal**: Find the maximum number of parallel pipelines your system can handle while maintaining target performance.

### Basic Stream Density Test
```bash
make benchmark-stream-density
```

**Default behavior:**
- Tests until FPS drops below 14.95 target
- Uses OOM protection to prevent system crashes
- Reports maximum sustainable pipeline count

### Custom Target FPS
```bash
# Test for different performance thresholds
make TARGET_FPS=13.5 benchmark-stream-density

# With custom pipeline configuration
make PIPELINE_SCRIPT=yolo11n_effnetb0.sh TARGET_FPS=13.5 benchmark-stream-density
```

### Stream Density Environment Variables

| Variable | Description | Values |
|:---------|:------------|:--------|
| `TARGET_FPS` | Minimum FPS threshold | `14.95` (default), `13.5`, `20.0` |
| `OOM_PROTECTION` | Prevent out-of-memory crashes | `1` (enabled), `0` (disabled) |

> ⚠️ **Warning**: Setting `OOM_PROTECTION=0` may crash your system requiring a hard reboot.

### Expected Output
```
Total averaged FPS per stream: 15.210442307692306 for 26 pipeline(s)
```

## Visualization & Analysis

### Generate Performance Graphs
```bash
make plot-metrics
```

**Output**: `benchmark/plot_metrics.png` showing:
- 🧠 CPU Usage Over Time
- ⚙️ NPU Utilization Over Time  
- 🎮 GPU Usage for each GPU device found

### Useful Maintenance Commands
```bash
make validate-all-configs    # Validate configuration files
make clean-images           # Remove dangling Docker images
make clean-containers       # Remove stopped containers  
make clean-all             # Remove all unused Docker resources
```
make clean-all             # Remove all unused Docker resources
```

## Custom Configuration (Advanced)

### Creating Custom Workloads

The application is highly configurable via JSON files in the `configs/` directory:

#### Camera Configuration (`camera_to_workload.json`)
```json
{
  "lane_config": {
    "cameras": [
      {
        "camera_id": "cam1",
        "fileSrc": "sample-media/video1.mp4",              
        "workloads": ["items_in_basket", "multi_product_identification"],
        "region_of_interest": {"x": 100, "y": 100, "x2": 800, "y2": 600}
      }
    ]
  }
}
```

#### Pipeline Configuration (`workload_to_pipeline.json`)  
```json
{
  "workload_pipeline_map": {
    "items_in_basket": [
      {"type": "gvadetect", "model": "yolo11n", "precision": "INT8", "device": "CPU"},
      {"type": "gvaclassify", "model": "efficientnet-v2-b0", "precision": "INT8", "device": "CPU"}
    ]
  }
}
```

### To Add Custom Workloads:
1. Edit `configs/camera_to_workload.json` to add your camera and assign workloads
2. Edit `configs/workload_to_pipeline.json` to define the pipeline for your workload
3. Place your video files in `performance-tools/sample-media/` and update the `fileSrc` path
4. Run `make validate-all-configs` to verify your configuration
5. Re-run the pipeline: `make benchmark`

## Detailed Hardware Distribution (Reference)

### Heterogeneous Configuration Breakdown
The `workload_to_pipeline_hetero.json` distributes workloads across multiple processing units:

| Workload | Object Detection | Classification | Inference |
|:---------|:----------------|:---------------|:----------|
| items_in_basket | GPU | GPU | - |
| hidden_items | GPU | CPU | - |
| fake_scan_detection | GPU | CPU | - |
| multi_product_identification | GPU | CPU | - |
| product_switching | GPU | GPU | - |
| sweet_heartening | NPU | - | NPU |

### Mixed Configuration Details  
The `workload_to_pipeline.json` balances workloads across available hardware:
- **CPU**: items_in_basket, multi_product_identification, sweet_heartening
- **GPU**: product_switching, hidden_items  
- **NPU**: fake_scan_detection

## Project Structure (Reference)

- `configs/` — Configuration files (camera/workload mapping)
- `docker/` — Dockerfiles for containers
- `download-scripts/` — Model and video download scripts
- `src/` — Pipeline runner scripts
- `performance-tools/benchmark-scripts/results/` — Performance test results
- `Makefile` — Build automation commands

---

## Appendix: System Configuration

### Proxy Configuration (If Required)

If your organization requires proxy settings for internet access:

#### Shell Session Proxy
```bash
export http_proxy=http://<proxy-host>:<port>
export https_proxy=http://<proxy-host>:<port>
export HTTP_PROXY=http://<proxy-host>:<port>
export HTTPS_PROXY=http://<proxy-host>:<port>
export NO_PROXY=localhost,127.0.0.1,::1
```

#### System-Wide Proxy (`/etc/environment`)
```bash
sudo nano /etc/environment
# Add the following lines:
http_proxy=http://<proxy-host>:<port>
https_proxy=http://<proxy-host>:<port>
HTTP_PROXY=http://<proxy-host>:<port>
HTTPS_PROXY=http://<proxy-host>:<port>
NO_PROXY=localhost,127.0.0.1,::1
```

#### Docker Proxy Configuration
Create `/etc/systemd/system/docker.service.d/http-proxy.conf`:
```bash
sudo mkdir -p /etc/systemd/system/docker.service.d
sudo nano /etc/systemd/system/docker.service.d/http-proxy.conf

[Service]
Environment="http_proxy=http://<proxy-host>:<port>"
Environment="https_proxy=http://<proxy-host>:<port>"
Environment="no_proxy=localhost,127.0.0.1,::1"

# Reload and restart
sudo systemctl daemon-reload
sudo systemctl restart docker
```