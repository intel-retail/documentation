## 1. Run benchmarking on CPU/NPU/GPU.
### Default benchmark command

```bash
make benchmark
```

### Benchmark command for GPU

```bash
make DEVICE_ENV=res/all-gpu.env benchmark
```

### Benchmark command for NPU

```bash
make DEVICE_ENV=res/all-npu.env benchmark
```

### 2. See the benchmarking results.

```sh
make  consolidate-metrics

cat benchmark/metrics.csv
```


## 3.🛠️ Other Useful Make Commands.

- `make clean-images` — Remove dangling Docker images
- `make clean-models` — Remove all the downloaded models from the system
- `make clean-all` — Remove all unused Docker resources

## 📁 Project Structure

- `configs/` — Configuration files (workload videos URLs)
- `docker/` — Dockerfiles for downloader and pipeline containers
- `download-scripts/` — Scripts for downloading models and videos
- `src/` — Main source code and pipeline runner scripts
- `Makefile` — Build automation and workflow commands

---
