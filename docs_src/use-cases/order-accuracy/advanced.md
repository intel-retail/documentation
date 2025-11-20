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
- Pull pre-built images (`REGISTRY=true`)<br>
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
- `REGISTRY=true`<br>
- `DEVICE_ENV=res/all-cpu.env`<br>
- `PIPELINE_COUNT=1`<br>

You can override these values through the following Environment Variables.

| Variable | Description | Values |
|:----|:----|:---|
|`RENDER_MODE` | for displaying pipeline and overlay CV metadata | 1, 0 |
|`REGISTRY` | to pull pre-built images from public registry | false, true |
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

### Benchmark command to build images locally

```bash
make REGISTRY=false benchmark
```

## See the benchmarking results.

```sh
make consolidate-metrics

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


## Configure the system proxy

Please follow the below steps to configure the proxy

### 1. Configure Proxy for the Current Shell Session

```bash
export http_proxy=http://<proxy-host>:<port>
export https_proxy=http://<proxy-host>:<port>
export HTTP_PROXY=http://<proxy-host>:<port>
export HTTPS_PROXY=http://<proxy-host>:<port>
export NO_PROXY=localhost,127.0.0.1,::1
export no_proxy=localhost,127.0.0.1,::1
export socks_proxy=http://<proxy-host>:<port>
export SOCKS_PROXY=http://<proxy-host>:<port>
```

### 2. System-Wide Proxy Configuration

System-wide environment (/etc/environment)
(Run: sudo nano /etc/environment and add or update)

```bash
http_proxy=http://<proxy-host>:<port>
https_proxy=http://<proxy-host>:<port>
ftp_proxy=http://<proxy-host>:<port>
socks_proxy=http://<proxy-host>:<port>
no_proxy=localhost,127.0.0.1,::1

HTTP_PROXY=http://<proxy-host>:<port>
HTTPS_PROXY=http://<proxy-host>:<port>
FTP_PROXY=http://<proxy-host>:<port>
SOCKS_PROXY=http://<proxy-host>:<port>
NO_PROXY=localhost,127.0.0.1,::1
```
### 3. Docker Daemon & Client Proxy Configuration

Docker daemon drop-in (/etc/systemd/system/docker.service.d/http-proxy.conf)
Create dir if missing:
sudo mkdir -p /etc/systemd/system/docker.service.d
sudo nano /etc/systemd/system/docker.service.d/http-proxy.conf

```bash
[Service]
Environment="http_proxy=http://<proxy-host>:<port>"
Environment="https_proxy=http://<proxy-host>:<port>"
Environment="no_proxy=localhost,127.0.0.1,::1"
Environment="HTTP_PROXY=http://<proxy-host>:<port>"
Environment="HTTPS_PROXY=http://<proxy-host>:<port>"
Environment="NO_PROXY=localhost,127.0.0.1,::1"
Environment="socks_proxy=http://<proxy-host>:<port>"
Environment="SOCKS_PROXY=http://<proxy-host>:<port>"

# Reload & restart:
sudo systemctl daemon-reload
sudo systemctl restart docker

#  Docker client config (~/.docker/config.json)
#  mkdir -p ~/.docker
#  nano ~/.docker/config.json
{
  "proxies": {
    "default": {
      "httpProxy": "http://<proxy-host>:<port>",
      "httpsProxy": "http://<proxy-host>:<port>",
      "noProxy": "localhost,127.0.0.1,::1"
    }
  }
}
```