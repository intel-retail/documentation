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
- Images tag: rc1 (`TAG=rc1`)<br>
- Target GPU by default (`WORKLOAD_DIST=workload_to_pipeline_gpu.json`)<br>
- Run 6 streams, each with different workload (`CAMERA_STREAM=camera_to_workload_full.json`)<br>
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
- `TAG=rc1`<br>
- `CAMERA_STREAM=camera_to_workload.json`<br>
- `WORKLOAD_DIST=workload_to_pipeline.json`<br>
- `PIPELINE_COUNT=1`<br>
- `REGISTRY=true`<br>

You can override these values through the following Environment Variables.

| Variable | Description | Values |
|:----|:----|:---|
|`RENDER_MODE` | for displaying pipeline and overlay CV metadata | 1, 0 |
|`PIPELINE_COUNT` | number of Loss Prevention Docker container instances to launch | Ex: 1 |
|`WORKLOAD_DIST` | to define how each workload is assigned to a specific processing unit (CPU, GPU, NPU) | workload_to_pipeline_cpu.json, workload_to_pipeline_gpu.json, workload_to_pipeline_gpu-npu.json, workload_to_pipeline_hetero.json, workload_to_pipeline.json |  
|`CAMERA_STREAM` | to define camera settings and their associated workloads for the pipeline | camera_to_workload.json, camera_to_workload_full.json |    
|`REGISTRY` | option to pull the pre-built images rather than creating them locally | false, true | 
|`TAG` | tag for the images pulled | rc1 | 

> **Note:**  
> Higher the `PIPELINE_COUNT`, higher the stress on the system.  
> Increasing this value will run more parallel pipelines, increasing resource usage and testing system

### All CAMERA_STREAM options
- `camera_to_workload.json`

     | Camera_ID | Workload |
     |:----|:---|
     | cam1 | items_in_basket + multi_product_identification |
     | cam2 | hidden_items + product_switching |
     | cam3 | fake_scan_detection |
  
- `camera_to_workload_full.json`

     | Camera_ID | Workload |
     |:----|:---|
     | cam1 | items_in_basket |
     | cam2 | hidden_items |
     | cam3 | fake_scan_detection |
     | cam4 | multi_product_identification |
     | cam5 | product_switching |
     | cam6 | sweet_heartening |

### All WORKLOAD_DIST options

- `workload_to_pipeline_cpu.json` - All the workloads run on CPU.
- `workload_to_pipeline_gpu.json` - All the workloads run on GPU.
- `workload_to_pipeline_gpu-npu.json` -<br>
&nbsp; -  items_in_basket, hidden_items, multi_product_identification and product_switching run on GPU,<br>
&nbsp; -  fake_scan_detection and sweet_heartening run on NPU.<br>
- `workload_to_pipeline_hetero.json` -
  
  | Workload | gvadetect | gvaclassify | gvainference |
  |:---|:---|:---|:---|
  | items_in_basket | GPU | GPU | - |
  | hidden_items | GPU | CPU | - |
  | fake_scan_detection | GPU | CPU | - |
  | multi_product_identification | GPU | CPU | - |
  | product_switching | GPU | GPU | - |
  | sweet_heartening | NPU | - | NPU |

  
- `workload_to_pipeline.json` - <br>
&nbsp;  - items_in_basket, multi_product_identification and sweet_heartening run on CPU,<br>
&nbsp;  - product_switching and hidden_items run on GPU,<br>
&nbsp;  - fake_scan_detection runs on NPU.<br>

!!! Note
    The first time running this command may take few minutes. It will build all performance tools containers


### Benchmark command with environment variable overrides

```bash
make benchmark WORKLOAD_DIST=workload_to_pipeline_gpu-npu.json CAMERA_STREAM=camera_to_workload_full.json REGISTRY=false
```

Runs with:<br>
- `RENDER_MODE=0`<br>
- `REGISTRY=false` (Builds images locally)<br> 
- `CAMERA_STREAM=camera_to_workload_full.json`<br>
- `WORKLOAD_DIST=workload_to_pipeline_gpu-npu.json`<br>
- `PIPELINE_COUNT=1`<br>


### See the benchmarking results

```sh
make consolidate-metrics

cat benchmark/metrics.csv
```


## 🛠️ Other Useful Make Commands

- `make validate-all-configs` — Validate all configuration files
- `make clean-images` — Remove dangling Docker images
- `make clean-containers` — Remove stopped containers
- `make clean-all` — Remove all unused Docker resources


## ⚙️ Configuration

The application is highly configurable via JSON files in the `configs/` directory:

- **`camera_to_workload.json`**: Maps each camera to one or more workloads. To add or remove a camera, edit the `lane_config.cameras` array in this file. Each camera entry can specify its video source, region of interest, and assigned workloads.
    - Example:
      ```json
      {
        "lane_config": {
          "cameras": [
            {
              "camera_id": "cam1",
              "fileSrc": "sample-media/video1.mp4",              
              "workloads": ["items_in_basket", "multi_product_identification"],
              "region_of_interest": {"x": 100, "y": 100, "x2": 800, "y2": 600}
            },
            ...
          ]
        }
      }
      ```
- **`workload_to_pipeline.json`**: Maps each workload name to a pipeline definition (sequence of GStreamer elements and models). To add or update a workload, edit the `workload_pipeline_map` in this file.
    - Example:
      ```json
      {
        "workload_pipeline_map": {
          "items_in_basket": [
            {"type": "gvadetect", "model": "yolo11n", "precision": "INT8", "device": "CPU"},
            {"type": "gvaclassify", "model": "efficientnet-v2-b0", "precision": "INT8", "device": "CPU"}
          ],
          ...
        }
      }
      ```

**To try a new camera or workload:**
1. Edit `configs/camera_to_workload.json` to add your camera and assign workloads.
2. Edit `configs/workload_to_pipeline.json` to define or update the pipeline for your workload.
3. (Optional) Place your video files in the appropriate directory and update the `fileSrc` path.
4. Re-run the pipeline as described above.

## 📁 Project Structure

- `configs/` — Configuration files (camera/workload mapping, pipeline mapping)
- `docker/` — Dockerfiles for downloader and pipeline containers
- `docs/` — Documentation (HLD, LLD, system design)
- `download-scripts/` — Scripts for downloading models and videos
- `src/` — Main source code and pipeline runner scripts
- `Makefile` — Build automation and workflow commands

---
