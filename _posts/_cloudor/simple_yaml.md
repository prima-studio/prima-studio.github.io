#### Hello World With A Simpy YAML File

The one-liner example in [quick start](./quickstart.md) can be handy. But more serious users are recommemded to use `yaml` for more feature support. Here we show we can specify a job that you can choose to run any cloud. 

Here is an example:

```yaml
# examples/device_query.yaml
kind: job
version: v1alpha
spec:
  image: cloudorio/conda_cuda:10.2
  envs: 
    - name: env1
      value: 1
  command: python 
  args:
    - /opt/examples/numba_device_query.py
vendors:
  - tag: us_west_v100
    name: aws
    instance_type: p3.2xlarge
    region: us-west-2
  - tag: us_east_k80
    name: aws
    instance_type: p2.xlarge
    region: us-east-1
  - tag: azure
    name: azure
    instance_type: NC6s_v2
    region: eastus
```

The `spec` section contains the "common" job settings such as image name and command. 
The `vendors` section contains a list of vendor configurations with name, region and instance type. 
The user can specify which vendor to use with `--tag` argument:

```bash
# to run on aws us-west-2 p3.2xlarge
$ cloudor run -f examples/device_query.yaml --tag us_west_v100 --timeout 5

# to run on azure
$ cloudor run -f examples/device_query.yaml --tag azure --timeout 5
```

The up-to-date yaml spec definition for a cloudor job can be find as [go struct](https://github.com/cloudor-io/cloudctl/blob/master/pkg/api/types.go). It is under frequent change. Please check 


##### Use local image file

You can replace the docker image name with a locally saved docker image file to by-pass the step to push image to docker hub.

```bash
# Firstly, save the docker image locally
$ docker image save cloudorio/conda_cuda:10.2 -o /tmp/conda_cuda.tar
```

```yaml
# Secondly, modify the spec/image to the file path
...
spec:
  image: /tmp/conda_cuda.tar
...
```


