# Getting Started for K8s C-API YOLOV8

## Instructions for Running K8s C-API YOLOv8 using minikube:

### Presiquistes:

- Ununtu 22.04 +
- [minikube](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)
- [kompose](https://github.com/kubernetes/kompose?tab=readme-ov-file#binary-installation)

### Build C-API YOLOv8 Container Image

change directory to `use-cases/gst_capi/helm` while you are at [the retail-use-cases project base directory](https://github.com/intel-retail/retail-use-cases/):

    ```bash
    cd ./use-cases/gst_capi/helm
    ```

and then build the container image for minikube to run:

    ```bash
    make minikube_build_capi_yolov8_ensemble
    ```
and you should see all built successfully.

### Run C-API YOLOv8 in minikube

Run the following command while you are still on the directory `use-cases/gst_capi/helm`:

    ```bash
    make run_capi_yolov8_ensemble
    ```
and you should see all K8s pods running up.

### Verify Pods Running

    ```bash
    kubectl get pod
    ```
    Result:
    ```console
    NAME                                  READY   STATUS    RESTARTS       AGE
    camera-simulator-8cffdc4ff-nbcd2      1/1     Running   0              84s
    camera-simulator0-65779f499-fkk7h     1/1     Running   2 (72s ago)    84s
    camera-simulator1-8684b659cc-8b4lg    1/1     Running   2 (72s ago)    84s
    capiyolov8ensemble-57b9ff7d6f-w5hj4   1/1     Running   0              83s
    intel-gpu-plugin-kzxlh                1/1     Running   44 (66m ago)   2d19h
    mqtt-broker-9b597cd94-vxprd           1/1     Running   0              83s
    ```

### Verify Results

    After starting C-API YOLOv8 ensemble k8s deployment, you will begin to see the FPS results being published into MQTT-broker serice as it can be seen in the log file via running the command:

    ```bash
    make minikube_pod_log
    ```
    
    ```console
    ...
    cid: 20241018174229464559017
    PIPELINE_PROFILE: capi_yolov8_ensemble  DEVICE: CPU
    DC: 0 INPUTSRC: rtsp://camera-simulator:8554/camera_1 USE_VPL: 0 RENDER_MODE: 0 RENDER_PORTRAIT_MODE: 0
    CODEC_TYPE: 1 WINDOW_WIDTH: 1280 WINDOW_HEIGHT: 720 DETECTION_THRESHOLD: 0.5
    RESULT_USE_MQTT=1 MQTT_BROKER=mqtt-broker MQTT_PORT=1883
    publish pipeline results using MQTT: mqtt-broker
    _videoStreamPipeline: rtsp://camera-simulator:8554/camera_1
    _use_onevpl: 0
    _render: 0
    _renderPortrait: 0
    videoType: 1
    _window_width: 1280
    _window_height: 720
    use mqtt: 1
    mqttBroker: mqtt-broker
    mqtt port =  1883
    ...
    libva info: VA-API version 1.22.0
    libva info: Trying to open /usr/lib/x86_64-linux-gnu/dri/iHD_drv_video.so
    libva info: Found init function __vaDriverInit_1_14
    libva info: va_openDriver() returns 0
    --------------------------------------------------------------
    Opening Media Pipeline: rtspsrc location=rtsp://camera-simulator:8554/camera_1 ! rtph264depay ! h264parse ! vah264dec ! video/x-raw(memory:VAMemory),format=NV12  ! vapostproc !  video/x-raw, width=416, height=416  ! videoconvert ! video/x-raw,format=RGB ! queue ! appsink drop=1 sync=0
    --------------------------------------------------------------
    ovmsCofigJsonFilePath: /app/gst-ovms/pipelines/yolov8_ensemble/config-yolov8.json
    ...
    Starting thread: 131360809276992
    2 object(s) detected at 2024-10-18.17:42:53
    Avg. Pipeline Throughput FPS: 30.000000
    Avg. Pipeline Latency (ms): 58
    Max. Pipeline Latency (ms): 72
    Min. Pipeline Latency (ms): 36
    Connected to MQTT broker: tcp://mqtt-broker:1883
    publishing messages to MQTT broker: tcp://mqtt-broker:1883
    topic: capiyolov8ensemble/FPS messages: 30.000000
    waiting for up to 10 seconds for publishing...
    delivered token: 1 status code = 0
    2 object(s) detected at 2024-10-18.17:42:55
    Avg. Pipeline Throughput FPS: 20.000000
    Avg. Pipeline Latency (ms): 44
    Max. Pipeline Latency (ms): 55
    Min. Pipeline Latency (ms): 39
    publishing messages to MQTT broker: tcp://mqtt-broker:1883
    topic: capiyolov8ensemble/FPS messages: 20.000000
    waiting for up to 10 seconds for publishing...
    delivered token: 2 status code = 0
    ...
    ```

### Shutdown Running Containers

    ```bash
    make down_capi_yolov8_ensemble
    ```
