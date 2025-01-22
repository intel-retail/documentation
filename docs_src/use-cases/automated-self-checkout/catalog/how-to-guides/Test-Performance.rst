.. _test_performance:

Test Performance
===========================================

You can run the performance tests across CPU and GPU, and across all architectures, in combination and individually to provide flexibility in identifying performance needs.

You can collect the metrics for predefined pipelines or for stream density. 
You can run the script in two ways:

* Defined Pipelines: This allows you to pre-define the number of pipelines and gather the performance metrics.
* Stream Density: This allows you to pre-define the target frames per second (FPS) rate required per pipeline, and then measure metrics.   

Benchmark Predefined Pipelines
-------------------------------------------

This allows you to pre-define the number of pipelines and gather the performance metrics. By default, the ``benchmark`` command launches one ``yolov5s_full.sh`` pipeline and provides the metrics for the same:

.. code-block:: bash

   make benchmark

You can configure the number of pipelines using the ``PIPELINE_COUNT`` environment variable:

.. code-block:: bash

   make PIPELINE_COUNT=2 benchmark

You can also add environment variable overrides to the command:

.. code-block:: bash

   make PIPELINE_SCRIPT=yolov5s_effnetb0.sh PIPELINE_COUNT=2 benchmark


Benchmark Stream Density
--------------------------

This allows you to pre-define the target frames per second (FPS) rate required per pipeline, and then measure metrics. The default is to determine the container threshold for FPS above 14.95 using the ``yolov5s_full.sh`` pipeline. 


.. code-block:: bash

   make benchmark-stream-density

To test the maximum amount of Automated Self-checkout containers that can run on a system, you can use the ``TARGET_FPS`` environment variable:

.. code-block:: bash

   make TARGET_FPS=13.5 benchmark-stream-density

You can override these values using environment variables:

.. code-block:: bash

   make PIPELINE_SCRIPT=yolov5s_effnetb0.sh TARGET_FPS=13.5 benchmark-stream-density


   