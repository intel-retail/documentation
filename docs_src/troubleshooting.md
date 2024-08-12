# Troubleshooting

Q: Why is the performance sometimes on CPU better than on GPU, when running pipeline benchmarking like stream density?

A: The performance of pipeline benchmarking strongly depends on the models.  Specifically for `yolov5s` object detection, it is recommended to use the model precision FP32 when it is running on device `GPU`.  If supported, then you can change the model precision by updating the pipeline script to point to the precision of your choice.  For example, you can change the model of `FP16` to `FP32` assuming the precision `FP32` of the target model is available:  
        
```bash
    src/pipelines/yolov5s.sh

    ...
    model=models/object_detection/yolov5s/FP16-INT8/yolov5s.xml
    ...
```

Q: What happens if the system keeps crashing when building the `dlstreamer-realsense` image?

A: Some systems may run into issues with memory when building the `dlstreamer-realsense` image. In the `Dockerfile.dlstreamer` file, change the make command to not use the `-j` threading option.

```diff
- make -j"$(cat < /proc/cpuinfo |grep -c proc)" &&
+ make &&
```

Q: What happens if the RTSP source is not found?

A: Depending on your systems performance it is possible that the RTSP simulator may take additional time to initialize and start streaming. To avoid issues you can add a waiting period before the pipeline starts. For example you can add a 5 second sleep timer to /src/entrypoint.sh

```bash
sleep 5 # sleep for 5 seconds before starting the pipeline
eval $gstLaunchCmd
```