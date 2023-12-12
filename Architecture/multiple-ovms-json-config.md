# Multiple OpenVINO Model Server Config JSON

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

Currently, we use same config.json for all instances of OpenVINO Model Server(OVMS) pipelines, which leads to some issue regarding the device mounting for OVMS server: see issue intel-retail/automated-self-checkout#322. So we need a way to have multiple or unique config json file per OVMS instance.

## Proposed Design 

<!-- Please provide a high level design of the proposed requirement. -->

Move the current device update logic from `run.sh` with the config.json file into profile-launcher in Golang. When profile-launcher about to launch a new instance of OVMS server, it then produces a unique config json file name for that instance of OVMS server.

For example, we can use the Docker container name of that OVMS server like ovms_server0, or ovms_server1,...etc to be appended into the config json as part of the file name (e.g. config_ovms_server0.json).

One example golang code for updating json file target_device and producing a new config.json is shown below:
```go
// ----------------------------------------------------------------------------------
// Copyright 2023 Intel Corp.
//
// Licensed under the Apache License, Version 2.0 (the "License");
// you may not use this file except in compliance with the License.
// You may obtain a copy of the License at
//
//	   http://www.apache.org/licenses/LICENSE-2.0
//
//	Unless required by applicable law or agreed to in writing, software
//	distributed under the License is distributed on an "AS IS" BASIS,
//	WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
//	See the License for the specific language governing permissions and
//	limitations under the License.
//
// ----------------------------------------------------------------------------------

package main

import (
	"encoding/json"
	"fmt"
	"log"
	"os"
	"reflect"
)

type OvmsConfig struct {
	ModelList []ModelConfig `json:"model_config_list"`
}

type ModelConfig struct {
	Config map[string]interface{} `json:"config"`
}

func main() {
	updateConfig("CPU")
}

func updateConfig(device string) {
	contents, err := os.ReadFile("config_template.json")
	if err != nil {
		err = fmt.Errorf("Cannot read json config %v", err)
	}

	var data OvmsConfig
	err = json.Unmarshal(contents, &data)
	if err != nil {
		log.Fatalf("failed to unmarshal configuration file configuration.yaml: %v", err)
	}

	fmt.Println(reflect.TypeOf(data.ModelList))

	for _, model := range data.ModelList {
		// fmt.Println(model)
		model.Config["target_device"] = device
		fmt.Println(model.Config["target_device"])
		fmt.Println("!!!!!!!!!!!!")
		fmt.Println(model.Config)
	}

	// convert to struct
	updateConfig, err := json.Marshal(data)
	if err != nil {
		log.Fatalf("could not marshal config to JSON: %v", err)
	}
	_ = os.WriteFile("config_ovms_server0.json", updateConfig, 0644)
}
```

This step is done before the profile-launcher calling the `start_ovms_server.sh`script.

In the profile-launcher we also set the correct  environment variable values for the `start_ovms_server.sh`script to use.  For example, `OVMS_MODEL_CONFIG_JSON` to be the unique config json file name that was produced from the above example.

For clean-up, we can do deletion of the config json files when `make clean-all` is called or `make clean-ovms-server` is called.

## References

<!-- [link](requirements-review-process.md) - useful links for the design -->

- https://docs.openvino.ai/2023.0/ovms_what_is_openvino_model_server.html
- https://github.com/openvinotoolkit/model_server
- see issue Classification profile crashed when run the 2nd instance switch from CPU to GPU.0 automated-self-checkout#322
