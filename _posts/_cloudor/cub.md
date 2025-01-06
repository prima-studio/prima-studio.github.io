#### A C++ User Case

This example shows how to build a CUDA accelerated C++ application and run it in `cloudor`.
More specifically, we walk through the steps to build [cub](https://github.com/nvlabs/cub) in develop container and release to a runtime container that can be executed on `cloudor`:

1. Clone the cub repository
   ```bash
   git clone https://github.com/NVlabs/cub.git
   ```
2. Build in a develop container
   ```bash
   docker build . -t cub_test:11.0-devel-ubuntu18.04
   ```
   where the Dockerfile is:
   ```Dockerfile
   FROM nvidia/cuda:11.0-devel-ubuntu18.04
   COPY ./cub/test /opt/cub/test
   COPY ./cub/common.mk /opt/cub/common.mk
   WORKDIR /opt/cub/test
   RUN make test_iterator sm=600,610,620,700
   ```
   Here we use the official cuda container `nvidia/cuda:11.0-devel-ubuntu18.04`. We only copy the necessary directories to build one of the test binaries (test\_iterator). This intermediate image is named as "cub\_test:11.0-devel-ubuntu18.04".
3. Next we replace to a much smaller run-time container (`nvidia/cuda:11.0-runtime-ubuntu18.04`) for testing and release purposes.
   First, we copy the binary directory from the devel image in the last step to local host:
   ```bash
   docker create -ti --name temp cub_test:11.0-devel-ubuntu18.04 bash
   docker cp temp:/opt/cub/test ./test
   docker rm -f temp
   ```
   We should have a directory `test` under the current path.
   Then, we copy the directory to the new run-time image for testing:
   ```bash
   docker build . -t cub_test:11.0-runtime-ubuntu18.04 -f Dockerfile_runtime
   ```
   where the other Dockerfile is:
   ```Dockerfile
   FROM nvidia/cuda:11.0-runtime-ubuntu18.04
   COPY ./test/bin /opt/cub/test/bin
   WORKDIR /opt/cub/test/bin
   CMD ./test_iterator_sm600-610-620-700_nvvm_11.0_abi_nocdp_x86_64_thorough
   ```
4. Launch it in `cloudor`.

   We can either push it to dockerhub or save the docker image locally for cloudor. For the former method, suppose the pushed docker name is `user/cub_test:11.0-runtime-ubuntu18.04`:
   ```bash
   $ cloudor run user_name/cub_test:11.0-runtime-ubuntu18.04 --vendor aws --region us-west-2 --instance-type p3.2xlarge
   2020/08/09 16:26:52 job submitted successfully, to be running on aws/us-west-2/p3.2xlarge
   2020/08/09 16:28:50 job status "started": started running docker image user_name/cub_test:11.0-runtime-ubuntu18.04
   2020/08/09 16:29:42 job status "running": container job started running, image: user_name/cub_test:11.0-runtime-ubuntu18.04
   2020/08/09 16:30:19 job status "returned": terators: PASS
        Values: PASS
        Iterators: PASS
        Iterators: PASS
        Values: PASS
        Iterators: PASS
		...
	Testing transform iterator on type 7TestBar
        Values: PASS
        Iterators: PASS
        Iterators: PASS
        thrust::copy_if(): PASS

	Test complete
	2020/08/09 16:30:21 job status "finished": output uploaded to 2020-08-09/XXXX/20200809-1fsRS8sI6pAoe5fP807xwh9APFF/output.zip succeeded
   ```
   

The source code for this example can be found [here](TODO)

