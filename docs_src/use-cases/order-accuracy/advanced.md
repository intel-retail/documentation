### 1. Run benchmarking on CPU/NPU/GPU.
>*By default, the configuration is set to use the CPU. If you want to benchmark the application on GPU or NPU, please update the device value in workload_to_pipeline.json.*

```sh
make  benchmark
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

- `configs/` — Configuration files (camera/workload mapping, pipeline mapping)
- `docker/` — Dockerfiles for downloader and pipeline containers
- `download-scripts/` — Scripts for downloading models and videos
- `src/` — Main source code and pipeline runner scripts
- `Makefile` — Build automation and workflow commands

---