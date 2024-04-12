# Computer Vision Pipeline Benchmarking

The provided Python-based script works with Docker Compose to get pipeline performance 
metrics like video processing in frames-per-second (FPS), memory usage, power
consumption, and so on.

# Prerequisites 

- Python environment v3.12.2
  
    !!! Note
        This could be accomplished using [Miniconda](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-python.html) and creating a [Python 3.12.2 env](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-python.html)

- Python packages listed in [performance-tools/benchmark-scripts/requirements.txt](https://github.com/intel-retail/performance-tools/blob/main/benchmark-scripts/requirements.txt)

    !!! Note
        Use `pip install -r requirements.txt`

- Docker
- Docker Compose
- Make
- Git
- Code from [Retail Use Cases Repo](https://github.com/intel-retail/retail-use-cases) and its submodule [Performance Tools Repo](https://github.com/intel-retail/performance-tools)

    !!! Note
        To install the submodule, run `make update-submodules` from the root of the retail-use-cases repo.

# Benchmark a CV Pipeline

1. Build the benchmark container and change into the benchmark-scripts directory.

   ```bash
   make build-benchmark-docker
   cd benchmark-scripts
   ```
2. Choose a CV pipeline from the Retail Use Cases Repo and note the file paths to the docker compose files.
3. Run the benchmarking script using the docker compose file(s) as inputs to the script (sample command shown below).

    ```bash
    python benchmark.py --compose_file ../../use-cases/gst_capi/add_camera-simulator.yml --compose_file ../../use-cases/gst_capi/add_gst_capi_yolov5_ensemble.yml
    ```

## Benchmark Stream Density for CV Pipelines

Benchmarking a pipeline can also discover the maximum number of workloads or streams that can be run in parallel for a given target FPS. This information is useful to determine the hardware required to achieve the desired performance for CV pipelines.

To run the stream density functionality use `--target_fps` and/or `--density_increment` as inputs to the `benchmark.py` script:

   ```bash
    python benchmark.py  --retail_use_case_root ../../retail-use-cases --target_fps 14.95 --density_increment 1 --init_duration 40   --compose_file ../../retail-use-cases/use-cases/grpc_python/docker-compose_grpc_python.yml
   ```

where the parameters:
- `target_fps` is the given target frames per second (fps) to achive for maximum number of pipelines
- `density_increment` is to configure the benchmark logic to increase the number of pipelines each time while trying to find out the maximum number of pipelines before reach the given target fps.
- `init_duration` is the initial duration period in second before pipeline performance metrics are taken


    !!! Note
        It is recommended to set --target_fps to a value lesser than your target FPS to account for real world variances in hardware readings.

# Modifying Additional Benchmarking Variables

## Change Power Profile

- For Ubuntu, follow this [documentation](https://help.ubuntu.com/stable/ubuntu-help/power-profile.html.en) to change the power profile.
- For Windows, follow this [documentation](https://support.microsoft.com/en-us/windows/change-the-power-mode-for-your-windows-pc-c2aff038-22c9-f46d-5ca0-78696fdf2de8) to change the power mode.


# Developer Resources

## Python Testing

To run the unit tests for the performance tools:

```bash
cd benchmark-scripts
make python-test
```

To run the unit tests and determine the coverage:

```bash
cd benchmark-scripts
make python-coverage
```