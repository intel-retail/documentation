# Computer Vision Pipeline Benchmarking

The provided Python-based script works with Docker Compose to get pipeline performance 
metrics like video processing in frames-per-second (FPS), memory usage, power
consumption, and so on.

# Prerequisites 

- Python environment v3.12.2
  - could use [Miniconda](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-python.html) and create a [Python 3.12.2 env](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-python.html)
- Python packages listed in requirements.txt
- Docker
- Docker Compose
- Make
- Git
- Code from [Retail Use Cases Repo](https://github.com/intel-retail/retail-use-cases) and [Performance Tools Repo](https://github.com/intel-retail/performance-tools)

# Benchmark a CV Pipeline

1. Change directories, build the benchmark docker container, and change back to the benchmark-scripts directory.
   ```bash
   cd benchmark-scripts/docker
   make build-igt
   cd ..
   ```
2. Choose a CV pipeline from the Retail Use Cases Repo and note the file paths to the docker compose files.
3. Run the benchmarking script using the docker compose file(s) as inputs to the script (sample command shown below).

    '''
   python benchmark.py --compose_file ../../retail-use-cases/use-cases/gst_capi/add_camera-simulator.yml --compose_file ../../retail-use-cases/use-cases/gst_capi/add_gst_capi_yolov5_ensemble.yml
    '''

# Modifying Additional Benchmarking Variables

## Change Power Profile

- For Ubuntu, follow this [documentation](https://help.ubuntu.com/stable/ubuntu-help/power-profile.html.en) to change the power profile.
- For Windows, follow this [documentation](https://support.microsoft.com/en-us/windows/change-the-power-mode-for-your-windows-pc-c2aff038-22c9-f46d-5ca0-78696fdf2de8) to change the power mode.