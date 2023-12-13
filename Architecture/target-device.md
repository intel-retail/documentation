# Distributed Architecture

<!--ts-->

- [Status](#status)
- [Decision](#decision)
- [Context](#context)
- [Proposed Design](#proposed-design)
- [Consequences](#consequences)
- [References](#references)

<!--te-->

## Decision

<!-- Requirements approval board will update this section with justification for approval or rejection -->

## Context  

<!-- Please provide context to the requirement. -->

The platform parameter is inconsistent with the target device being used in the pipeline. To be consistent with OVMS we want to use the target_device parameter in the script to match the target_device setting in the OVMS config file.

## Proposed Design 

<!-- Please provide a high level design of the proposed requirement. -->

Update the platform parameter to match the target_device standard used by OpenVINO Model Server. This will provide clarity to the device being used for the inferencing portion of the pipeline. The following are the acceptance criteria for the change.

- Replace platform parameter with target_device using CPU as the default device.
- Update the docker_run script to make it run with minimal changes to the profiles.
- Confirm that the benchmark script works with the target_device parameter update.
- Update unit tests
- Update documentation
- Convert $DEVICE to $TARGET_DEVICE for internal environment variables.
- Add option to use existing config file and not override all target_devices to support models with different target_device values.

### Target Device list

| Device                           | Parameter       | Description                                                                                                            | Links |
| -------------------------------  | --------------- | ---------------------------------------------------------------------------------------------------------------------- |------ |
| CPU                              | CPU             | Use CPU only                                                                                                           | [OVMS Parameters](https://docs.openvino.ai/2023.1/ovms_docs_parameters.html) |
| GPU                              | GPU             | Use default GPU                                                                                                        | [OVMS Parameters](https://docs.openvino.ai/2023.1/ovms_docs_parameters.html) |
| Specified GPU                    | GPU.x           | Use a specific GPU. ex. GPU.0 = integrated GPU, GPU.1 = discrete Arc GPU                                               | [OVMS Parameters](https://docs.openvino.ai/2023.1/ovms_docs_parameters.html) |
| Mixed Contifuration              | MULTI:x,y       | Use a combination of devices for inferencing ex. MULTI:CPU,GPU.1 will use the CPU and discrete Arc GPU for inferencing | [OVMS Parameters](https://docs.openvino.ai/2023.1/ovms_docs_parameters.html) |
| Automatic Device Selection       | AUTO            | Allow OpenVINO to automatically select the optimal device for inferencing                                              | Possibly depricated? |
| Automatic Device Selection       | AUTO            | Allow OpenVINO to automatically select the optimal device for inferencing                                              | Possibly depricated? |
| Heterogeneous Execution          | HETERO          | Allows OpenVINO to execute inference on multiple devices                                                               | [Heterogeneous Execution](https://docs.openvino.ai/2023.1/openvino_docs_OV_UG_Hetero_execution.html) |
| Heterogeneous Execution Priority | HETERO:x,y      | Allows OpenVINO to execute inference on multiple devices and set the priority of device. ex. HETERO:CPU,GPU.1 will prioritize CPU and discrete Arc GPU for inferencing | [Heterogeneous Execution](https://docs.openvino.ai/2023.1/openvino_docs_OV_UG_Hetero_execution.html) |

## Applicable Repos

[automated-self-checkout](https://github.com/intel-retail/automated-self-checkout)

## Consequences

<!-- Please provide a description of what consequences this requirement will have on the project. This includes breaking and non-breaking changes to all microservices -->

Removing the platform parameter will break any existing test and benchmark scripts. The change will clarify which device you are targeting for the inference.

## References

<!-- [link](requirements-review-process.md) - useful links for the design -->

https://docs.openvino.ai/2023.0/ovms_what_is_openvino_model_server.html
https://github.com/openvinotoolkit/model_server