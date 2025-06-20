# Performance Testing

The performance tools repository is included as a github submodule in this project. The performance tools enable you to test the pipeline system performance on various hardware. 

## Benchmark specific number of pipelines

Before running benchmark commands, make sure you already configured python and its dependencies. Visit the Performance tools installation guide [HERE]((../../performance-tools/benchmark.md#benchmark-a-cv-pipeline))

You can launch a specific number of Automated Self Checkout containers using the PIPELINE_COUNT environment variable. Default is to launch `one` yolo11n.sh pipeline. You can override these values through Environment Variables.

!!! Note
    The first time running this command may take few minutes. It will build all performance tools containers

After running the following commands, you will find the results in `performance-tools/benchmark-scripts/results/` folder.

### Default benchmark command

```bash
make benchmark
```

### Benchmark `2` pipelines in parallel:

```bash
make PIPELINE_COUNT=2 benchmark 
```

### Benchmark command with environment variable overrides

```bash
make PIPELINE_SCRIPT=yolo11n_effnetb0.sh DEVICE_ENV=res/all-gpu.env PIPELINE_COUNT=2 benchmark
```

Alternatively you can directly call the benchmark.py. This enables you to take advantage of all performance tools parameters. More details about the performance tools can be found [HERE](../../performance-tools/benchmark.md#benchmark-a-cv-pipeline)

```bash
cd performance-tools/benchmark-scripts && python benchmark.py --compose_file ../../src/docker-compose.yml --pipeline 2
```

## Benchmark Stream Density

To test the maximum amount of Automated Self Checkout containers/pipelines that can run on a given system you can use the TARGET_FPS environment variable. Default is to find the container threshold over 14.95 FPS with the yolo11n.sh pipeline. You can override these values through Environment Variables.

```bash
make benchmark-stream-density
```

You can check the output results for performance metrics in the `results` folder at the root level. Also, the stream density script will output the results in the console:

```
Total averaged FPS per stream: 15.210442307692306 for 26 pipeline(s)
```

### Change the Target FPS value:

```bash
make TARGET_FPS=13.5 benchmark-stream-density
```

### Environment variable overrides can also be added to the command

```bash
make PIPELINE_SCRIPT=yolo11n_effnetb0.sh TARGET_FPS=13.5 benchmark-stream-density
```

Alternatively you can directly call the benchmark.py. This enables you to take advantage of all performance tools parameters. More details about the performance tools can be found [HERE](../../performance-tools/benchmark.md#benchmark-stream-density-for-cv-pipelines)

```bash
cd performance-tools/benchmark-scripts && python benchmark.py --compose_file ../../src/docker-compose.yml --target_fps 14
```