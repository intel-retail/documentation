# Getting Started

## 📋 Prerequisites

- Ubuntu 24.04 or newer (Linux recommended), Desktop edition (or Server edition with GUI installed).
- [Docker](https://docs.docker.com/engine/install/)
- [Make](https://www.gnu.org/software/make/) (`sudo apt install make`)
- **Python 3** (`sudo apt install python3`) - required for video download and validation scripts
- Intel hardware (CPU, iGPU, dGPU, NPU)
- Intel drivers:
    - [Intel GPU drivers](https://dgpu-docs.intel.com/driver/client/overview.html)
    - [NPU](https://dlstreamer.github.io/dev_guide/advanced_install/advanced_install_guide_prerequisites.html#prerequisite-2-install-intel-npu-drivers)
- Sufficient disk space for models, videos, and results


### **NOTE:** 

By default the application runs by pulling the pre-built images. If you want to build the images locally and then run the application, set the flag:

```bash
REGISTRY=false

usage: make <command> REGISTRY=false (applicable for all commands like benchmark, benchmark-stream-density..)
Example: make run-lp REGISTRY=false
```

By default the application runs in Headless mode. If you want to run in Visual Mode run the application, by setting the flag:

```bash
RENDER_MODE=1

usage: make <command> RENDER_MODE=1 (applicable for all commands like benchmar,benchmark-stream-density..)
Example: make run-lp RENDER_MODE=1
```

(If this is the first time, it will take some time to download videos, models, docker images and build images)

## Step by step instructions:

1. Clone the repo with the below command
    ```
    git clone -b <release-or-tag> --single-branch https://github.com/intel-retail/loss-prevention
    ```
    >Replace <release-or-tag> with the version you want to clone (for example, **v4.0.0**).
    ```
    git clone -b v4.0.0 --single-branch https://github.com/intel-retail/loss-prevention
    ```

2. To run loss prevention from pre-built images,follow the below steps:

    ```bash
    #Download the models using download_models/downloadModels.sh
    make download-models
    #Update github submodules
    make update-submodules
    #Download sample videos used by the performance tools
    make download-sample-videos
    #Run the LP application
    make run-render-mode
    ```

    **NOTE:- User can directly run single make command that internally called all above command and run the Loss Prevention application.**

   - Run Loss Prevention appliaction with single command.   

    ```bash
    make run-lp RENDER_MODE=1
    ```

    By default, Loss Prevention 6 default workloads are executed. Refer to the [Pre-Configured Workloads](#pre-configured-workloads) section for more details.

3. To build the images locally step by step:
    - Follow the following steps:
      ```bash
      make download-models REGISTRY=false
      make update-submodules REGISTRY=false
      make download-sample-videos
      make run-render-mode REGISTRY=false
      ```
      
    - The above series of commands can be executed using only one command:
    
      ```bash
      make run-lp REGISTRY=false RENDER_MODE=1
      ```
4. Verify Results

    After starting Loss Prevention you will begin to see result files being written into the results/ directory. Here are example outputs from the 3 log files.

    gst-launch_<time>_gst.log
    ```
    /GstPipeline:pipeline0/GstGvaWatermark:gvawatermark0/GstCapsFilter:capsfilter1: caps = video/x-raw(memory:VASurface), format=(string)RGBA
    /GstPipeline:pipeline0/GstFPSDisplaySink:fpsdisplaysink0/GstXImageSink:ximagesink0: sync = true
    Got context from element 'vaapipostproc1': gst.vaapi.Display=context, gst.vaapi.Display=(GstVaapiDisplay)"\(GstVaapiDisplayGLX\)\ vaapidisplayglx0", gst.vaapi.Display.GObject=(GstObject)"\(GstVaapiDisplayGLX\)\ vaapidisplayglx0";
    Progress: (open) Opening Stream
    Pipeline is PREROLLED ...
    Prerolled, waiting for progress to finish...
    Progress: (connect) Connecting to rtsp://localhost:8554/camera_0
    Progress: (open) Retrieving server options
    Progress: (open) Retrieving media info
    Progress: (request) SETUP stream 0
    ```

    pipeline<time>_gst.log
    ```
    14.58
    14.58
    15.47
    15.47
    15.10
    15.10
    14.60
    14.60
    14.88
    14.88
    ```

    r<time>_gst.jsonl
```json

{
    "objects": [
        {
            "detection": {
                "bounding_box": {
                    "x_max": 0.7873924346958825,
                    "x_min": 0.6701603093745723,
                    "y_max": 0.7915918938548927,
                    "y_min": 0.14599881349270305
                },
                "confidence": 0.8677337765693665,
                "label": "apple",
                "label_id": 39
            },
            "h": 697,
            "region_id": 610,
            "roi_type": "apple",
            "w": 225,
            "x": 1287,
            "y": 158
        },
        {
            "detection": {
                "bounding_box": {
                    "x_max": 0.3221945836811382,
                    "x_min": 0.19950163649114616,
                    "y_max": 0.7924592239981934,
                    "y_min": 0.1336837231479251
                },
                "confidence": 0.8625879287719727,
                "label": "apple",
                "label_id": 39
            },
            "h": 711,
            "region_id": 611,
            "roi_type": "apple",
            "w": 236,
            "x": 383,
            "y": 144
        },
        {
            "detection": {
                "bounding_box": {
                    "x_max": 0.5730873789069046,
                    "x_min": 0.42000878963365595,
                    "y_max": 0.9749763191740435,
                    "y_min": 0.12431765065780453
                },
                "confidence": 0.854443371295929,
                "label": "apple",
                "label_id": 39
            },
            "h": 919,
            "region_id": 612,
            "roi_type": "apple",
            "w": 294,
            "x": 806,
            "y": 134
        }
    ],
    "resolution": {
        "height": 1080,
        "width": 1920
    },
    "timestamp": 755106652
}

```
> [!NOTE]
> If unable to see results folder or files, please refer to the [Troubleshooting](#troubleshooting) section for more details.

5.  Stop the containers:

    When pre-built images are pulled-
   
    ```bash
    make down-lp
    ```

    When images are built locally-
   
    ```bash
    make down-lp REGISTRY=false
    ```

## Pre-configured Workloads
The preconfigured workload supports multiple hardware configurations out of the box. Use the `CAMERA_STREAM` and `WORKLOAD_DIST` variables to customize which cameras and hardware (CPU, GPU, NPU) are used by your pipeline.

**How To Use:**
- Specify the appropriate files as environment variables when running or benchmarking:
    ```sh
    CAMERA_STREAM=<camera_stream> WORKLOAD_DIST=<workload_dist> make run-lp
    ```
    Or for benchmarking:
    ```sh
    CAMERA_STREAM=<camera_stream> WORKLOAD_DIST=<workload_dist> make benchmark
    ```
### Loss Prevention

| Description             | CAMERA_STREAM                  | WORKLOAD_DIST                        |
|-------------------------|-------------------------------|--------------------------------------|
| CPU (Default)           | camera_to_workload.json        | workload_to_pipeline.json            |
| GPU                     | camera_to_workload.json        | workload_to_pipeline_gpu.json        |
| NPU + GPU               | camera_to_workload.json        | workload_to_pipeline_gpu-npu.json    |
| Heterogeneous           | camera_to_workload.json        | workload_to_pipeline_hetero.json     |

> [!NOTE]
> The following sub-workloads are automatically included and enabled in the configuration:
> 
> `items_in_basket`
  `hidden_items`
  `fake_scan_detection`
  `multi_product_identification`
  `product_switching`
  `sweet_heartening`

### Automated Self Check Out

| Description                                    | CAMERA_STREAM                  | WORKLOAD_DIST                        |
|------------------------------------------------|-------------------------------|--------------------------------------|
| Object Detection (GPU)                         | camera_to_workload_asc_object_detection.json       | workload_to_pipeline_asc_object_detection_gpu.json        |
| Object Detection (NPU)                         | camera_to_workload_asc_object_detection.json       | workload_to_pipeline_asc_object_detection_npu.json        |
| Object Detection & Classification (GPU)        | camera_to_workload_asc_object_detection_classification.json        | workload_to_pipeline_asc_object_detection_classification_gpu.json     |
| Object Detection & Classification (NPU)        | camera_to_workload_asc_object_detection_classification.json        | workload_to_pipeline_asc_object_detection_classification_npu.json     |
| Age Prediction & Face Detection (GPU)          | camera_to_workload_asc_age_verification.json        | workload_to_pipeline_asc_age_verification_gpu.json     |
| Age Prediction & Face Detection (NPU)          | camera_to_workload_asc_age_verification.json        | workload_to_pipeline_asc_age_verification_npu.json     |
| Heterogenous                  | camera_to_workload_asc_hetero.json        | workload_to_pipeline_hetero.json     |



### User Defined Workloads
The application is highly configurable via JSON files in the `configs/` directory

**To try a new camera or workload:**

1. Create new camera to workload mapping in `configs/camera_to_workload_custom.json` to add your camera and assign workloads.
    - **camera_to_workload_custom.json**: Maps each camera to one or more workloads. 
        - To add or remove a camera, edit the `lane_config.cameras` array in the file.
        - Each camera entry can specify its video source, region of interest, and assigned workloads.
        Example:
          ```json
              {
                "lane_config": {
                 "cameras": [
                {
                  "camera_id": "cam1",
                  "fileSrc": "sample-media/video1.mp4",
                  "workloads": ["custom_workload_1"],
                  "region_of_interest": {"x": 100, "y": 100, "x2": 800, "y2": 600}              
                },
                {
                  "camera_id": "cam2",
                  "fileSrc": "sample-media/video2.mp4",
                  "workloads": ["custom_workload_2", "custom_workload_3"],
                  "region_of_interest": {"x": 100, "y": 100, "x2": 800, "y2": 600}              
                }
              ]
            }
          }
          ```
If adding new videos, place your video files in the directory **performance-tools/sample-media/** and update the `fileSrc` path.

2. Create new `configs/workload_to_pipeline_custom.json` to define pipeline for your workload.
    - **workload_to_pipeline_custom.json**: Maps each workload name to a pipeline definition (sequence of GStreamer elements and models). 
        Example:
      
      ```json
      {
        "workload_pipeline_map": {
          "custom_workload_1": [
            {"type": "gvadetect", "model": "yolo11n", "precision": "INT8", "device": "CPU"},
            {"type": "gvaclassify", "model": "efficientnet-v2-b0", "precision": "INT8", "device": "CPU"}
          ],
          "custom_workload_2": [
            {"type": "gvadetect", "model": "yolo11n", "precision": "INT16", "device": "NPU"},
            {"type": "gvaclassify", "model": "efficientnet-v2-b0", "precision": "INT16", "device": "NPU"}
          ],
          "custom_workload_3": [
            {"type": "gvadetect", "model": "yolo11n", "precision": "INT8", "device": "GPU"},
            {"type": "gvaclassify", "model": "efficientnet-v2-b0", "precision": "INT8", "device": "GPU"}
          ]
        }
      }
      ```
3. Run validate configs command, to verify configuration files
   ```sh
   make validate-all-configs
   ```
4. Re-run the pipeline as described above.
     
> [!NOTE]
> Since the GStreamer pipeline is generated dynamically based on the provided configuration(camera_to_workload and workload_to_pipeline json),
> the pipeline.sh file gets updated every time the user runs make run-lp or make benchmark. This ensures that the pipeline reflects the latest changes.
  ```sh
        src/pipelines/pipeline.sh
  ```


## Troubleshooting

+ If results folder is empty, check Docker logs for errors:
    + List the docker containers
      ```sh
        docker ps -a
      ```
    + Verify Docker containers if it is running or no errors in container logs

    ```bash
    docker ps --all
    ```
    Result:
    ```bash
    NAMES                    STATUS                     IMAGE
    src-pipeline-runner-1    Up 17 seconds (healthy)    pipeline-runner:lp
    model-downloader         Exited(0) 17 seconds       model-downloader:lp
    ```

    + Check each container logs
      ```sh
        docker logs <container_id>
      ```
+ If the file content in  `<loss-prevention-workspace>/results/pipeline_stream*.log` is empty, check GStreamer output file for errors:
    +  `<oss-prevention-workspace>/results/gst-launch_*.log`
  

## [Proceed to Advanced Settings](advanced.md)    
