# Intel® Automated Self-Checkout Reference Package

## Overview

As Computer Vision becomes more and more mainstream, especially for industrial & retail use cases, development and deployment of these solutions becomes more challenging. Vision workloads are large and complex and need to go through many stages. For instance, in the pipeline below, the video data is ingested, pre-processed before each inferencing step, inferenced using two models - YOLOv5 and EfficientNet, and post processed to generate metadata and show the bounding boxes for each frame. This pipeline is just an example of the supported models and pipelines found within this reference.

[![Vision Data Flow](./images/vision-data-flow.jpg)](./images/vision-data-flow.jpg)

Automated self-checkout solutions are complex, and retailers, independent software vendors (ISVs), and system integrators (SIs) require a good understanding of hardware and software, the costs involved in setting up and scaling the system, and the configuration that best suits their needs. Vision workloads are significantly larger and require systems to be architected, built, and deployed with several considerations. Hence, a set of ingredients needed to create an automated self-checkout solution is necessary. More details are available on the [Intel Developer Focused Webpage](https://www.intel.com/content/www/us/en/developer/articles/reference-implementation/automated-self-checkout.html) and on this [LinkedIn Blog](https://www.linkedin.com/pulse/retail-innovation-unlocked-open-source-vision-enabled-mohideen/)

The Intel® Automated Self-Checkout Reference Package provides critical components required to build and deploy a self-checkout use case using Intel® hardware, software, and other open-source software. This reference implementation provides a pre-configured automated self-checkout pipeline that is optimized for Intel® hardware. 

## Next Steps

!!! Note
    If coming from the catalog please follow the [Catalog Getting Started Guide](./catalog/Overview.md).

To begin using the automated self-checkout solution you can follow the [Getting Started Guide](./getting_started.md). 

## Releases

For the project release notes, refer to the [GitHub* Repository](../../releasenotes.md).
