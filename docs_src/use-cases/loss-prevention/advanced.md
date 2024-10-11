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


For enviroments variables, follow the same tutorial as the automated self checkout [HERE](../automated-self-checkout/advanced.md)


