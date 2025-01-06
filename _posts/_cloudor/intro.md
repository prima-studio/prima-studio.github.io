# Introduction

`cloudor` is a cloud middleware service that faciliates running container jobs on a variety of cloud vendors (aws, azure etc.). 
The alpha release focuses on supporting CUDA-accelerated, docker-based jobs, even though it can be used to run x64 container jobs.

## Why `cloudor`?

You should consider using `cloudor` service if you are developing applications that:

* are CUDA-accelerated and preferably need NVIDIA GPUs
* are not very latency-sensitive and their typical run time range from minutes to hours

Typical user cases are (but not limited to):

* to run CUDA-accelerated HPC jobs, whether they can be routinely scheduled or on-demand
* to run machine learning training jobs
* to run sandbox with open source tools such as [jupyter notebook or jupyterlab](https://jupyter.org/)

Other benefits of using `cloudor` service are:

* Users do not need to sign-up and configure for each cloud vendor
* Easily run, test, benchmark or deliver your code on a variety of hardwares
* Reduce the workload in on-premise servers
* Stay neutral on cloud platforms, preventing vendor lock-in
* Easy to run critical jobs simultaneously on different cloud vendors, regions
* Data never leaves border
* Deliver ready-to-run code to customers who do not necessarily have the preferred hardware
* Cost is more predictable and transparent

However, `cloudor` is not suitable for:

* Latency-critical applications
    * Currently, `cloudor` does not maintain a "persistent" server for each kind of instances. 
	Therefore, the user may very likely need to wait for the boot time, which can take up to several minutes.
* A continuous service (e.g. web servers)
    * `cloudor` is tailored for batch job that finishes within finite runtime

## How to use `cloudor` service?

`cloudor` maintains two tools for user:

1. A multi-platform, open sourced [command line tool](https://github.com/cloudor-io/cloudctl) 
2. A web-based [dev console](https://cloudor.dev)

Both components enable users to view jobs or credits, however:
* Job submission can only be done by the command line tool
* Credit payment can only be done in the web console
