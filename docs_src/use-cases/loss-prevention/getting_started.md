# Getting Started

### **NOTE:** 

By default the application runs by pulling the pre-built images. If you want to build the images locally and then run the application, set the flag:

```bash
REGISTRY=false

usage: make <command> REGISTRY=false (applicable for all commands like benchmark, benchmark-stream-density..)
Example: make run-lp REGISTRY=false
```

(If this is the first time, it will take some time to download videos, models, docker images and build images)

## Step by step instructions:

1. Download the models using download_models/downloadModels.sh

    ```bash
    make download-models
    ```

2. Update github submodules

    ```bash
    make update-submodules
    ```

3. Download sample videos used by the performance tools

    ```bash
    make download-sample-videos
    ```

4. Run the LP application

    ```bash
    make run-render-mode
    ```

    **NOTE:- User can directly run single make command that internally called all above command and run the Loss Prevention application.**

- Run Loss Prevention appliaction with single command.   

    ```bash
    make run-lp
    ```

    - Running Loss Prevention application with ENV variables:
      ```bash
      CAMERA_STREAM=camera_to_workload_full.json WORKLOAD_DIST=workload_to_pipeline_cpu.json make run-lp
      ```
      `CAMERA_STREAM=camera_to_workload_full.json`: runs all 6 workloads. <br>
      `WORKLOAD_DIST=workload_to_pipeline_cpu.json`: all workloads run on CPU. <br>

5. To build the images locally step by step:
    - Follow the following steps:
      ```bash
      make download-models REGISTRY=false
      make update-submodules REGISTRY=false
      make download-sample-videos
      make run-render-mode REGISTRY=false
      ```
      
    - The above series of commands can be executed using only one command:
    
      ```bash
      make run-lp REGISTRY=false
      ```
      
6. View the Dynamically Generated GStreamer Pipeline.
    >*Since the GStreamer pipeline is generated dynamically based on the provided configuration(camera_to_workload and workload_to_pipeline json), the pipeline.sh file gets updated every time the user runs make run-lp or make benchmark. This ensures that the pipeline reflects the latest changes.*
    ```sh

    src/pipelines/pipeline.sh

    ```

7. Verify Docker containers

    ```bash
    docker ps --all
    ```
    Result:
    ```bash
    NAMES                    STATUS                     IMAGE
    src-pipeline-runner-1    Up 17 seconds (healthy)    pipeline-runner:lp
    model-downloader         Exited(0) 17 seconds       model-downloader:lp
    ```

8. Verify Results

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

9. Stop the containers:

    When pre-built images are pulled-
   
    ```bash
    make down-lp
    ```

    When images are built locally-
   
    ```bash
    make down-lp REGISTRY=false
    ```

## [Proceed to Advanced Settings](advanced.md)    
