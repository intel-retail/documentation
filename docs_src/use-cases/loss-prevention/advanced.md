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


