# Getting Started

### **NOTE:** 

By default the application runs by pulling the pre-built images. If you want to build the images locally and then run the application, set the flag:

```bash
REGISTRY=false

usage: make <command> REGISTRY=false (applicable for all commands like benchmark, benchmark-stream-density..)
Example: make run-demo REGISTRY=false
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

4. Run the order accuracy application

    ```bash
    make run-render-mode
    ```

- The above series of commands can be executed using only one command:
    
  ```bash
  make run-demo
  ```

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
    make run-demo REGISTRY=false
    ```

6. Verify Docker containers

    ```bash
    docker ps --all
    ```
    Result:
    ```bash
    NAMES                    STATUS                     IMAGE
    src-ClientGst-1          Up 17 seconds (healthy)    dlstreamer:dev
    model-downloader         Exited(0) 17 seconds       model-downloader:latest
    ```

7. Verify Results

    After starting Order Accuracy you will begin to see result files being written into the results/ directory. Here are example outputs from the 3 log files.

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

8. Stop the containers:
    When pre-built images are pulled-
    ```bash
    make down
    ```
    When images are built locally-
    ```bash
    make down REGISTRY=false
    ```

## [Proceed to Advanced Settings](advanced.md)    
