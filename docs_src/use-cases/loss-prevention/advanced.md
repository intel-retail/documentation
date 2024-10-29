# Advanced Settings

To further customize a loss prevention pipeline, let's add more variables to the execution:

!!! Example

    ```bash
    make PIPELINE_SCRIPT=yolov8s_roi.sh RESULTS_DIR="../render_results"  run-render-mode
    ```

The above command will execute a DLStreamer pipeline using YOLOv8s model for object detection on a region of interest (ROI) with object tracking mechanism.

## Modify ROI coordinates

To modify the ROI coordinates, locate the file `roi.json` under src/pipelines/roi.json. Since the "objects" attribute is an array, it is possible to add multiple ROIs.

```json
[
    {
        "objects": [
            {
                "detection": {
                    "label": "ROI1"
                },
                "x": 0,
                "y": 0,
                "w": 620,
                "h": 1080
            }           
        ]
    }
]
```
## Class Filtering for YOLOv8 Pipeline

To detect specific classes using YOLOv8, edit the file `src/pipelines/yolov8s_roi.sh` and update the variable `CLASS_IDS="0"` to include the desired class IDs. For example, the default value is set to `"0"`, which corresponds to detecting only the "person" class. You can specify multiple class IDs using a comma-separated format like `"0,3,5,4"`, or leave the value empty (`""`) to detect all classes.

To find all supported classes by YOLOv8, you can find them in this file `src/extensions/object_filter.py`.

```json
CLASS_IDS="0,2"
```

## MQTT Inference Export and ROI Detection

This applicaiton enables monitoring object entry and exit within a defined Region of Interest (ROI), allowing real-time event tracking and external message handling.
The `yolov8s_roi.json` pipeline exports the inference data through MQTT using mosquitto broker defined in the `docker-compose.yml` file. 

To change the default MQTT URL, edit the file `src/pipelines/yolov8s_roi.sh` and update the variable `MQTT_HOST="127.0.0.1:1883"`.

We have developed a business logic application in `src/app/loss_prevention.py` that tracks objects entering and exiting a defined ROI, generating corresponding events.

To configure the app to connect to external MQTT broker, modify the `src/docker-compose.yml` and change the following env variables:

| Variable   | Default        | Description                              |
|------------|----------------|------------------------------------------|
| `MQTT_URL` | `127.0.0.1`    | MQTT Broker URL                          |
| `MQTT_PORT`| `1883`         | MQTT Broker Port                         |
| `MQTT_TOPIC` | `event/detection` | Topic for publishing inference data   |
| `ROI_NAME` | `BASKET`       | The name of the ROI used to filter objects |


The following diagram illustrates the containers running:  

[![MQTT export](./images/mqtt-diagram.jpg)](./images/mqtt-diagram.jpg)


For enviroments variables, follow the same tutorial as the automated self checkout [HERE](../automated-self-checkout/advanced.md)


