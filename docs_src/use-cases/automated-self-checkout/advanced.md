# Advanced Settings

## Applying Environment Variables(EV) to Run Pipeline

EV can be applied in two ways:

    1. As a Docker Compose environment parameter input 
    2. In the env files

The input parameter will override the one in the env files if both are used.

### Run with Custom Environment Variables

Environment variables with make commands

!!! Example

    ```bash
    make PIPELINE_SCRIPT=yolo11n_effnetb0.sh RESULTS_DIR="../render_results"  run-render-mode
    ```

Environment variable with docker compose up

!!! Example

    ```bash
    PIPELINE_SCRIPT=yolo11n_effnetb0.sh RESULTS_DIR="../render_results" docker compose -f src/docker-compose.yml --env-file src/res/yolov5-cpu.env up -d
    ```

!!! Note
        The environment variables set like this are known as command line environment overrides and are applied to this run only.
        They will override the default values in env files and docker-compose.yml.

### Editing the Environment Files

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

!!! Example "Docker compose"
    ```bash
    docker compose -f src/docker-compose.yml --env-file src/res/yolov5-cpu-class-gpu.env up -d
    ```

## Environment Variables (EVs)

The table below lists the environment variables (EVs) that can be used as inputs for the container running the inferencing pipeline.

=== "Docker Compose EVs"
    This list of EVs is for running through the make file or docker compose up

    | Variable | Description | Values |
    |:----|:----|:---|
    |`DEVICE_ENV` | Path to device specific environment file that will be loaded into the pipeline container | res/all-gpu.env |    
    |`DOCKER_COMPOSE` | The docker-compose.yml file to run | src/docker-compose.yml |
    |`RETAIL_USE_CASE_ROOT` | The root directory for Automated Self Checkout in relation to the docker-compose.yml | .. |
    |`RESULTS_DIR` | Directory to output results | ../results |

=== "Docker Compose Parameters"
    This list of parameters that can be set when running docker compose up

    | Variable | Description | Values |
    |:----|:----|:---|
    |`-v` | Volume binding for containers in the Docker Compose | -v results/:/tmp/results |
    |`-e` | Override environment variables inside of the Docker Container | -e LOG_LEVEL debug |

=== "Common EVs"
    This list of EVs is common for all profiles.

    | Variable | Description | Values |
    |:----|:----|:---|
    |`BARCODE_RECLASSIFY_INTERVAL` | time interval in seconds for barcode classification | Ex: 5 |
    |`BATCH_SIZE` | number of frames batched together for a single inference to be used in [gvadetect batch-size element](https://dlstreamer.github.io/elements/gvadetect.html) | 0-N |
    |`CLASSIFICATION_OPTIONS` | extra classification pipeline instruction parameters | "", "reclassify-interval=1 batch-size=1 nireq=4 gpu-throughput-streams=4" |
    |`DETECTION_OPTIONS` | extra object detection pipeline instruction parameters | "", "ie-config=NUM_STREAMS=2 nireq=2" |
    |`GST_DEBUG` | for running pipeline in gst debugging mode | 0, 1 |
    |`LOG_LEVEL` | log level to be set when running gst pipeline | ERROR, INFO, WARNING, and [more](https://gstreamer.freedesktop.org/documentation/tutorials/basic/debugging-tools.html?gi-language=c#the-debug-log) |
    |`OCR_RECLASSIFY_INTERVAL` | time interval in seconds for OCR classification | Ex: 5 |
    |`REGISTRY` | Option to pull the pre-built images rather than creating them locally (by default:true) | false, true |
    |`RENDER_MODE` | for displaying pipeline and overlay CV metadata | 1, 0 |
    |`PIPELINE_COUNT` | Number of Automated Self Checkout Docker container instances to launch | Ex: 1 |
    |`PIPELINE_SCRIPT` | Pipeline script to run. | yolo11n.sh, yolo11n_effnetb0.sh, yolo11n_full.sh |


## Available Pipelines

- `yolo11n.sh` - Runs object detection only.
- `yolo11n_full.sh` - Runs object detection, object classification, text detection, text recognition, and barcode detection.
- `yolo11n_effnetb0.sh` - Runs object detection, and object classification.
- `obj_detection_age_prediction.sh` - Runs two parallel streams:<br>
        &emsp;Stream 1: Object detection and classification on retail video. <br>
        &emsp;Stream 2: Face detection and age/gender recognition on age prediction video.

### Models used

- Age/Gender Recognition - `age-gender-recognition-retail-0013`
- Face Detection - `face-detection-retail-0004`
- Object Classification - `efficientNet-B0`
- Object Detection - `YOLOv11n`
- Text Detectoin - `horizontal-text-detection-0002`
- Text Recognition - `text-recognition-0012`

## Using a Custom Model

You can replace the default detection model with your own trained model by following these steps:

1. Clone the `automated-self-checkout` repository. This will create a folder named `automated-self-checkout`.

2. Inside the `automated-self-checkout` folder, ensure there is a `models` directory. If it doesn’t exist, create one.

3. Copy your custom model files into the `models` directory. For example, use this structure:

    ```text
    ./automated-self-checkout/models/object_detection/<my_custom_model>/INT8/<my_custom_model.xml>
    ```

4. Open the `yolo11n.sh` script. Locate the `gstLaunchCmd` line and update the `model` path to point to your custom model:

    !!! Example

        ```bash
        model=/home/pipeline-server/models/object_detection/<my_custom_model>/INT8/<my_custom_model.xml>
        ```

5. Run the pipeline as usual to start using your custom model.

When you add a custom model, it replaces the default detection model used by the pipeline.

!!! Note
    If your custom model includes a `labels` (`.txt`) file or a `model-proc` (`.json`) file, place them in the same folder as your `.xml` file. Then set the variables in `gstLaunchCmd` as shown below before running the pipeline.

    ```bash
    model-proc=/home/pipeline-server/models/object_detection/<my_custom_model>/INT8/<my_custom_model_proc.json>
    ```

    ```bash
    labels=/home/pipeline-server/models/object_detection/<my_custom_model>/INT8/<my_custom_model_labels.txt>
    ```

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