### Run with Custom Environment Variables

## Editing the Environment Files

Environment variable files can be used to persist environment variables between deployments. You can find these files in `src/res/` folder with our default environment variables for Automated Self Checkout.

| Environment File                          | Description                                                             |
|-------------------------------------------|-------------------------------------------------------------------------|
| `src/res/all-cpu.env`                     | Runs pipeline on **CPU** for decoding, pre-processing, and inferencing |
| `src/res/all-gpu.env`                     | Runs pipeline on **GPU** for decoding, pre-processing, and inferencing |
| `src/res/all-dgpu.env`                    | Runs pipeline on **discrete GPU** for decoding, pre-processing, and inferencing |
| `src/res/all-npu.env`                     | Runs pipeline on **NPU** for inferencing only                          |
| `src/res/yolov5-cpu-class-gpu.env`        | Uses **CPU** for detection and **GPU** for classification              |
| `src/res/yolov5-gpu-class-cpu.env`        | Uses **GPU** for detection and **CPU** for classification              |


After modifying or creating a new .env file you can load the .env file through the make command or docker compose up

!!! Example  "Make"
    ```bash
    make PIPELINE_SCRIPT=yolo11n_effnetb0.sh DEVICE_ENV=res/all-gpu.env run-render-mode    
    ```
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
