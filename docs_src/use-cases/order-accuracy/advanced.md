## Benchmark Quick Start command
```bash
make update-submodules
```
`update-submodules` ensures all submodules are initialized, updated to their latest remote versions, and ready for use.

```bash
make benchmark-quickstart
```
The above command would:<br>
- Run headless (no display needed: `RENDER_MODE=0`)<br>
- Target GPU by default (`DEVICE_ENV=res/all-gpu.env`)<br>
- Generate benchmark metrics<br>
- Run `make consolidate-metrics` automatically<br>


## Understanding Benchmarking Types

### Default benchmark command

```bash
make update-submodules
```
`update-submodules` ensures all submodules are initialized, updated to their latest remote versions, and ready for use.

```bash
make benchmark
```
Runs with:<br>
- `RENDER_MODE=0`<br>
- `DEVICE_ENV=res/all-cpu.env`<br>
- `PIPELINE_COUNT=1`<br>

You can override these values through the following Environment Variables.

| Variable | Description | Values |
|:----|:----|:---|
|`RENDER_MODE` | for displaying pipeline and overlay CV metadata | 1, 0 |
|`PIPELINE_COUNT` | number of Loss Prevention Docker container instances to launch | Ex: 1 |
|`DEVICE_ENV` | path to device specific environment file that will be loaded into the pipeline container | res/all-cpu.env, res/all-gpu.env, res/all-npu.env, res/all-dgpu.env |



### Benchmark command for GPU

```bash
make DEVICE_ENV=res/all-gpu.env benchmark
```

### Benchmark command for NPU

```bash
make DEVICE_ENV=res/all-npu.env benchmark
```

## See the benchmarking results.

```sh
make  consolidate-metrics

cat benchmark/metrics.csv
```





## Benchmark Stream Density

To test the maximum amount of Order Accuracy containers/pipelines that can run on a given system you can use the TARGET_FPS environment variable. Default is to find the container threshold over 7.95 FPS with the run-pipeline.sh pipeline. You can override these values through Environment Variables.

List of EVs:

 | Variable | Description | Values |
 |:----|:----|:---|
 |`TARGET_FPS` | threshold value for FPS to consider a valid stream | Ex. 7.95 |
 |`OOM_PROTECTION` | flag to enable/disable OOM checks before scaling the pipeline (enabled by default) | 1, 0 |

> **Note:**
> 
> An OOM crash occurs when a system or application tries to use more memory (RAM) than is available, causing the operating system to forcibly terminate processes to free up memory.<br>
> If `OOM_PROTECTION` is set to 0, the system may crash or become unresponsive, requiring a hard reboot. 
    
```bash
make benchmark-stream-density
```

You can check the output results for performance metrics in the `results` folder at the root level. Also, the stream density script will output the results in the console:



### Change the Target FPS value:

```bash
make TARGET_FPS=6.5 benchmark-stream-density
```


Alternatively you can directly call the benchmark.py. This enables you to take advantage of all performance tools parameters. More details about the performance tools can be found [HERE](../../performance-tools/benchmark.md#benchmark-stream-density-for-cv-pipelines)

```bash
cd performance-tools/benchmark-scripts && python benchmark.py --compose_file ../../src/docker-compose.yml --target_fps 7
```







## 🛠️ Other Useful Make Commands.

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
