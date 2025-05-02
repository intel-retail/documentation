# NPU Utilization

## Context

This feature would:

- Measure NPU utilization 
- Lightweight solution
- Containerized
- No real time graphical user interface
- generate a second by second NPU utilization log

## Proposed Design

Create a python script that can measure the runtime active time from the pci device power settings. Using this runtime, the utilization is calculated using the quotient of the difference between the current and previous active runtime over the change in time.

$$
NPU\;Utilization = \frac{current\;active\;runtime - previous\;active\;runtime}{current\;time - previous\;time}\times100
$$

This python script will compute the NPU utilization every second and write it to a file.