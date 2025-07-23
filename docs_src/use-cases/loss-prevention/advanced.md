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

- `make validate-all-configs` — Validate all configuration files
- `make clean-images` — Remove dangling Docker images
- `make clean-containers` — Remove stopped containers
- `make clean-all` — Remove all unused Docker resources


## 4.⚙️ Configuration

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