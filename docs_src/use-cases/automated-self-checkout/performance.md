# Performance Testing

The performance tools repository is included as a github submodule in this project. The performance tools enable you to test the pipeline system performance on various hardware. 

## Benchmark Quick Start command

```bash
make benchmark-quickstart
```
The above command would:
- Run headless (no display needed: `RENDER_MODE=0`)
- Use full pipeline (`PIPELINE_SCRIPT=obj_detection_age_prediction.sh`)
- Target GPU by default (`DEVICE_ENV=res/all-gpu.env`)
- Generate benchmark metrics
- Run `make consolidate-metrics` automatically

## Understanding Benchmarking Types

Before running benchmark commands, make sure you already configured python and its dependencies. Visit the Performance tools installation guide [HERE]((../../performance-tools/benchmark.md#benchmark-a-cv-pipeline))

You can launch a specific number of Automated Self Checkout containers using the PIPELINE_COUNT environment variable. Default is to launch `one` yolo11n.sh pipeline. You can override these values through Environment Variables.

List of EVs:

    | Variable | Description | Values |
    |:----|:----|:---|
    |`BATCH_SIZE_DETECT` | number of frames batched together for a single inference to be used in [gvadetect  batch-size element](https://dlstreamer.github.io/elements/gvadetect.html) | 0-N |
    |`BATCH_SIZE_CLASSIFY` | number of frames batched together for a single inference to be used in [gvaclassify batch-size element](https://dlstreamer.github.io/elements/gvaclassify.html) | 0-N |
    |`RENDER_MODE` | for displaying pipeline and overlay CV metadata | 1, 0 |
    |`PIPELINE_COUNT` | number of Automated Self Checkout Docker container instances to launch | Ex: 1 |
    |`PIPELINE_SCRIPT` | pipeline script to run. | yolo11n_effnetb0.sh, obj_detection_age_prediction.sh, etc. |
    |`DEVICE_ENV` | device to use for classification and detection | res/all-cpu.env, res/all-gpu.env, res/det-gpu_class-npu.env, etc. |    

> **Note:**  
> Higher the `PIPELINE_COUNT`, higher the stress on the system.  
> Increasing this value will run more parallel pipelines, increasing resource usage and testing system

!!! Note
    The first time running this command may take few minutes. It will build all performance tools containers

After running the following commands, you will find the results in `performance-tools/benchmark-scripts/results/` folder.

### Default benchmark command

```bash
make benchmark
```

### Benchmark `2` pipelines in parallel:

```bash
make PIPELINE_COUNT=2 benchmark 
```

### Benchmark command with environment variable overrides

```bash
make PIPELINE_SCRIPT=yolo11n_effnetb0.sh DEVICE_ENV=res/all-gpu.env PIPELINE_COUNT=1 benchmark
```

### Benchmark command for full pipeline (age prediction+object classification) using GPU

```bash
make PIPELINE_SCRIPT=obj_detection_age_prediction.sh DEVICE_ENV=res/all-gpu.env PIPELINE_COUNT=1 benchmark
```
`obj_detection_age_prediction.sh` runs TWO video streams in parallel even with PIPELINE_COUNT=1:

Stream 1: Object detection + classification on retail video

Stream 2: Face detection + age/gender classification on age prediction video

<br><br>

Alternatively you can directly call the benchmark.py. This enables you to take advantage of all performance tools parameters. More details about the performance tools can be found [HERE](../../performance-tools/benchmark.md#benchmark-a-cv-pipeline)

```bash
cd performance-tools/benchmark-scripts && python benchmark.py --compose_file ../../src/docker-compose.yml --pipeline 2
```

## Create a consolidated metrics file 

After running the benchmark command:

```bash
make consolidate-metrics
```

`metrics.csv` provides a summary of system and pipeline performance, including FPS, latency, CPU/GPU utilization, memory usage, and power consumption for each benchmark run.
It helps evaluate hardware efficiency and resource usage during automated self-checkout pipeline tests.

## Benchmark Stream Density

To test the maximum amount of Automated Self Checkout containers/pipelines that can run on a given system you can use the TARGET_FPS environment variable. Default is to find the container threshold over 14.95 FPS with the yolo11n.sh pipeline. You can override these values through Environment Variables.

List of EVs:

    | Variable | Description | Values |
    |:----|:----|:---|
    |`TARGET_FPS` | threshold value for FPS to consider a valid stream | Ex. 14.95 |
    |`OOM_PROTECTION` | flag to enable/disable OOM checks before scaling the pipeline (enabled by default) | 1, 0 |

> **Note:**  
> If `OOM_PROTECTION` is set to 0, the system may crash or become unresponsive, requiring a hard reboot. 
    
```bash
make benchmark-stream-density
```

You can check the output results for performance metrics in the `results` folder at the root level. Also, the stream density script will output the results in the console:

```
Total averaged FPS per stream: 15.210442307692306 for 26 pipeline(s)
```

### Change the Target FPS value:

```bash
make TARGET_FPS=13.5 benchmark-stream-density
```

### Environment variable overrides can also be added to the command

```bash
make PIPELINE_SCRIPT=yolo11n_effnetb0.sh TARGET_FPS=13.5 benchmark-stream-density
```

Alternatively you can directly call the benchmark.py. This enables you to take advantage of all performance tools parameters. More details about the performance tools can be found [HERE](../../performance-tools/benchmark.md#benchmark-stream-density-for-cv-pipelines)

```bash
cd performance-tools/benchmark-scripts && python benchmark.py --compose_file ../../src/docker-compose.yml --target_fps 14
```

### Plot Utilization Graphs

After running a benchmark, you can generate a consolidated CPU, NPU, and GPU usage graph based on the collected logs using:
```bash
make plot-metrics
```
This command generates a single PNG image (**`plot_metrics.png`**) under the **`benchmark`** directory, showing:  
> 🧠 CPU Usage Over Time  
> ⚙️ NPU Utilization Over Time  
> 🎮 GPU Usage Over Time for each device found  
