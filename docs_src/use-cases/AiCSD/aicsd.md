# AI Connect for Scientific Data (AiCSD)

This reference implementation is pulled in as a solution for pipeline management
and distributed image processing. This solution offers an EdgeX based secure,
two-system setup with microservices for managing data transfer and pipeline processing.
Visit the solution's [GitHub Pages](https://intel.github.io/AiCSD/index.html) for
more information.

As an extension to this project, a new service, as-pipeline-grpc-go, allows for 
the processing of simulated camera data using a gRPC call to an OVMS server running 
yolo11n. For information on how the service works and how to get started, visit the
[GRPC Yolov5s Pipeline](./pipeline-grpc-go.md) page.