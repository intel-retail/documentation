# GRPC Yolov5s Pipeline Service

## Overview

This EdgeX-based application service provides an interface to use gRPC to call 
OVMS to run the input data through a specified model. The service may be set up 
to use different topics to trigger different pipelines and call different models.
The current implementation makes a call to OVMS running the Yolov5s model.

## Getting Started

1. Clone the Aicsd code as a submodule in the directory retail-use-cases/use-cases
    ```shell
    git submodule add https://github.com/intel/AiCSD
    ```
   
2. Change directories to retail-use-cases
    ```shell
    cd ..
    ```
3. Download models and sample media 
    ```shell
    make download-models download-sample-media
    ```
5. Build the OVMS Docker container
    ```shell
    make build-ovms-server
    ``` 
   
7. change directories to use-cases/aicsd
    ```shell
    cd use-cases/aicsd
    ```
8. Build the Pipeline Validator and Pipeline grpc-go services 
    ```
    make docker-pipeline-val docker-pipeline-grpc-go
    ```
9. Modify docker-compose-pipeline-val.yml evnironment variables to have the following settings
    ```shell
    APPLICATIONSETTINGS_PIPELINEHOST: pipeline-grpc-go
    APPLICATIONSETTINGS_PIPELINEPORT: 59790
    ```
8. Run the pipeline validator and pipeline grpc-go services along with their dependencies
    ```shell
    make run-pipeline-grpc-go
    ```
9. Verify that the pipeline-grpc-go container has started by checking the log for the message
    ```log
    level=INFO ts=2024-05-30T20:06:58.037173489Z app=app-pipeline-grpc-go source=messaging.go:125 msg="Waiting for messages from the MessageBus on the 'ovms-grpc/yolov5' topic"
    ```
9. Send a POST request to http://localhost:59788/api/v1/launchPipeline with the following body
    ```json
    {
        "InputFileLocation":"rtsp://camera-simulator:8554/camera_0",
        "PipelineTopic":"ovms-grpc/yolov5",
        "OutputFileFolder":"0.0.0.0:8555"
    }
    ```
10. Verify that the pipeline-grpc-go container has output in its log. This shows that the pipeline is running.
    ```log
    level=INFO ts=2024-05-30T20:06:58.037173489Z app=app-pipeline-grpc-go source=messaging.go:125 msg="Waiting for messages from the MessageBus on the 'ovms-grpc/yolov5' topic"
    level=INFO ts=2024-05-30T20:08:37.753137304Z app=app-pipeline-grpc-go source=functions.go:265 msg="RunOvmsModel: Processing time: 45 ms; fps: 16.70864819479429\n"
    level=INFO ts=2024-05-30T20:08:37.685039109Z app=app-pipeline-grpc-go source=functions.go:265 msg="RunOvmsModel: Processing time: 37 ms; fps: 16.72014862354332\n"
    level=INFO ts=2024-05-30T20:08:40.689475521Z app=app-pipeline-grpc-go source=functions.go:265 msg="RunOvmsModel: Processing time: 49 ms; fps: 16.368045264717768\n"
    level=INFO ts=2024-05-30T20:08:40.801842876Z app=app-pipeline-grpc-go source=functions.go:265 msg="RunOvmsModel: Processing time: 29 ms; fps: 16.37919507955609\n"
    ```
11. To stop the data stream from coming in, stop the camera-simulator and camera-simulator0 containers.
12. Verify that the pipeline-grpc-go container has successfully finished processing by verifying the log says
    ```log
    level=DEBUG ts=2024-05-30T20:10:56.351778319Z app=app-pipeline-grpc-go source=triggermessageprocessor.go:196 msg="trigger successfully processed message 'OVMS Pipeline' in pipeline 64a47e72-3e89-452e-9010-2d64c059c108"
    ```
13. Verify that the job status from the pipeline validator service making a GET request to http://localhost:59788/api/v1/job which should return an entry as follows
    ```json
    [
        {
            "Id": "0",
            "Owner": "none",
            "InputFile": {
                "Hostname": "gateway",
                "DirName": "rtsp://camera-simulator:8554/",
                "Name": "camera_0",
                "Extension": "",
                "ArchiveName": "",
                "Viewable": "",
                "Attributes": {}
            },
            "PipelineDetails": {
                "TaskId": "de9ee7bd-aacb-4e6a-ac91-a3c3be3b09c3",
                "Status": "PipelineComplete",
                "QCFlags": "passed",
                "OutputFileHost": "",
                "OutputFiles": null,
                "Results": "ovms-server0:9001"
            },
            "LastUpdated": 1717025357138890553,
            "Status": "Complete",
            "ErrorDetails": null,
            "Verification": 0
        }
    ]
    ```
14. Repeat by restarting the camera-simulator services and sending the POST request to `launchPipeline`.

## Tearing Down

To tear down the services and clean up any data created, run 
```bash
make down clean-files clean-volumes
```
