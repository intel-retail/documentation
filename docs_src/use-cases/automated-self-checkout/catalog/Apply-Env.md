# Apply Custom Environment Variables

Custom environment variables allow you to configure the reference implementation to meet your specific requirements. 

For instance, with custom environment variables, you can configure the pipeline to run on a CPU-only setup or a mixed setup of CPU and GPU.

There are two ways to apply environment variables (EVs) to the system that will run the models:

* [Running the commands](#run-commands-to-set-custom-environment-variables)  (``make`` and ``docker compose up``) with custom environment variables as input parameters.
* [Editing the environment files](#edit-environment-files).

If you use both methods, the input parameter will override the corresponding parameter in the ``.env`` file.

## Run Commands to Set Custom Environment Variables

There are two methods to apply the environment variables:

* Using the ``make`` command. For example:

    ``` bash
      make PIPELINE_SCRIPT=yolov5s_effnetb0.sh RESULTS_DIR="../render_results"  run-render-mode
    ```

* Using the ``docker compose up`` command. For example:

    ``` bash
      PIPELINE_SCRIPT=yolov5s_effnetb0.sh RESULTS_DIR="../render_results" docker compose -f src/docker-compose.yml --env-file src/res/yolov5-cpu.env up -d 
    ```

Environment variables set in this way are known as command-line environment overrides. These changes apply only to the current run and override the default values specified in ``.env`` files and ``docker-compose.yml``.

For the list of environment variables that you can use, see [Environment Variables](#environment-variables).

## Edit Environment Files

Environment files can be used to persist environment variables across deployments. For the Automated Self-Checkout reference implementation, three environment variable files contain the default settings:

* ``src/gst.env`` file: Contains shared environment variables.
* ``src/yolov5-cpu.env`` file: Configures the pipeline to run on CPU only.
* ``src/yolov5-gpu.env`` file: Configures the pipeline to run on GPU or mixed CPU/GPU set up.

After modifying or creating a new ``.env`` file, you can load the file using the ``docker compose up`` command. For example:

  ``` bash
    docker compose -f src/docker-compose.yml --env-file src/res/yolov5-cpu.env up -d
  ```
For the list of environment variables that you can use, see [Environment Variables](#environment-variables).

## Environment Variables

The following are the environment variables that you can use as inputs for the container running the inferencing pipeline:

* [Docker Compose Environment Variables](#docker-compose-environment-variables)
* [Docker Compose Parameters](#docker-compose-parameters)
* [Common Environment Variables](#common-environment-variables)
* [Automated Self-checkout Environment Variables](#automated-self-checkout-environment-variables)

## Docker Compose Environment Variables

You can use the following environment variables when running the ``Makefile`` or executing a ``docker-compose up`` command:

| Variable | Description | Values |
|----------|-------------|--------|
| ``DEVICE_ENV`` | Path to device-specific environment file that will be loaded into the pipeline container. | ``src/res/yolov5-gpu.env`` |
| ``DEVICE`` | To set the device to be used for pipeline run. | ``"GPU"``, ``"CPU"``, ``"AUTO"``, ``"MULTI:GPU,CPU"`` |
| ``DOCKER_COMPOSE`` | The ``docker-compose.yml`` file to be run. | ``src/docker-compose.yml`` |
| ``RETAIL_USE_CASE_ROOT`` | The root directory for Automated Self-Checkout in relation to the ``docker-compose.yml`` file. | ``..`` |
| ``RESULTS_DIR`` | Directory to output results. | ``../results`` |

## Docker Compose Parameters

You can use the following parameters when running the ``docker-compose up`` command:

| Variable | Description | Values |
|----------|-------------|--------|
| ``v`` | Volume binding for containers in Docker Compose. | ``-v results/:/tmp/results`` |
| ``e`` | Override environment variables inside the Docker Container. | ``-e LOG_LEVEL debug`` |

## Common Environment Variables

You can use the following environment variables that are common across all Intel DLStreamer profiles: 

| Variable | Description | Values |
|----------|-------------|--------|
| ``BARCODE_RECLASSIFY_INTERVAL`` | The time interval, in seconds, for barcode classification. | For example, 5 |
| ``BATCH_SIZE`` | The number of frames grouped for a single inference, as specified by the ``batch-size`` element in [gvadetect](https://dlstreamer.github.io/elements/gvadetect.html). | 0, 1 |
| ``CLASSIFICATION_OPTIONS`` | Additional classification parameters for configuring pipelines. | "", ``reclassify-interval=1 batch-size=1 nireq=4 gpu-throughput-streams=4`` |
| ``DETECTION_OPTIONS`` | Additional parameters for configuring object detection pipelines. | "", ``gpu-throughput-streams=4 nireq=4 batch-size=1`` |
| ``GST_DEBUG`` | Run pipeline in GStreamer (GST) debugging mode. | 0, 1 |
| ``LOG_LEVEL`` | Log level to be configured when running GST pipeline. | ERROR, INFO, WARNING, and [more](https://gstreamer.freedesktop.org/documentation/tutorials/basic/debugging-tools.html?gi-language=c#the-debug-log). |
| ``OCR_RECLASSIFY_INTERVAL`` | Time interval, in seconds, for classifying Optical Character Recognition (OCR). | For example, 5 |
| ``RENDER_MODE`` | For displaying pipelines and overlaying computer vision metadata. | 1, 0 |
| ``PIPELINE_COUNT`` | Number of Automated Self Checkout Docker container instances to be launched. | For example, 1 |
| ``PIPELINE_SCRIPT`` | Pipeline script to be run. | ``yolov5s.sh``, ``yolov5s_effnetb0.sh``, ``yolov5s_full.sh`` |

## Automated Self-checkout Environment Variables

You can use the following environment variables for the EVAM workloads.

| Variable | Description | Values |
|----------|-------------|--------|
| ``DECODE``     | Instructions for using the decoding element in ``gst-launch``. | For example, ``"decode bin force-sw-decoders=1"`` |
| ``OCR_DEVICE`` | OCR device. | CPU, GPU |
| ``PRE_PROCESS``| Pre-processing command to be included for inferencing. | ``pre-process-backend=vaapi-surface-sharing pre-process-config=VAAPI_FAST_SCALE_LOAD_FACTOR=1`` |
| ``VA_SURFACE`` | Use the video analytics surface from the shared memory, if applicable. | "", "! ``video/x-raw(memory:VASurface)`` |
