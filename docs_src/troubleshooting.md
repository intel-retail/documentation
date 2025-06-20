# Troubleshooting

## Q: Why is the performance on CPU sometimes better than on GPU, when running pipeline benchmarking like stream density?

A: The performance of pipeline benchmarking strongly depends on the models.  Specifically for `yolo11n` object detection, it is recommended to use the model precision FP32 when it is running on device `GPU`.  If supported, then you can change the model precision by updating the pipeline script to point to the precision of your choice.  For example, you can change the model of `FP16` to `FP32` assuming the precision `FP32` of the target model is available:  
        
```bash
    src/pipelines/yolo11n.sh

    ...
    model=models/object_detection/yolo11n/FP16-INT8/yolo11n.xml
    ...
```

## Q: What happens if the system keeps crashing when building the `dlstreamer-realsense` image?

A: Some systems may run into issues with memory when building the `dlstreamer-realsense` image. In the `Dockerfile.dlstreamer` file, change the make command to not use the `-j` threading option.

```diff
- make -j"$(cat < /proc/cpuinfo |grep -c proc)" &&
+ make &&
```

## Q: What happens if the RTSP source is not found?

A: Depending on your systems performance it is possible that the RTSP simulator may take additional time to initialize and start streaming. To avoid issues you can add a waiting period before the pipeline starts. For example you can add a 5 second sleep timer to /src/entrypoint.sh

```bash
sleep 5 # sleep for 5 seconds before starting the pipeline
eval $gstLaunchCmd
```

## Q: How can I use an Intel RealSense Camera as the input?

A: To use a RealSense camara as an input for any of the retail use cases, you need to obtain the serial number first. Follow the instructions <a href="https://github.com/IntelRealSense/librealsense/tree/master/tools/enumerate-devices" target="__blank">here</a> to run `rs-enumerate-devices` and obtain the following information:

```
Device Name                   Serial Number       Firmware Version
Intel RealSense D415          725112060400        05.12.02.100
Device info:
    Name                          :     Intel RealSense D415
    Serial Number                 :     725112060400
    Firmware Version              :     05.12.02.100
    Recommended Firmware Version  :     05.12.02.100
    Physical Port                 :     \\?\usb#vid_8086&pid_0ad3&mi_00#6&2a371216&0&0000#{e5323777-f976-4f5b-9b55-b94699c46e44}\global
    Debug Op Code                 :     15
    Advanced Mode                 :     YES
    Product Id                    :     0AD3
    Camera Locked                 :     NO
    Usb Type Descriptor           :     3.2
    Product Line                  :     D400
    Asic Serial Number            :     012345678901
    Firmware Update Id            :     012345678901
```

Simply add the serial number to the `INPUTSRC` argument when calling the pipelines:

```
INPUTSRC=725112060400 make run-demo
```