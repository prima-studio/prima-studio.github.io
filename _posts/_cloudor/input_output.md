#### Train a Model With Input and Output

Most, if not all, real-world applications consume inputs and generate outputs. 
Currently, `cloudor` supports uploading and downloading them from local directory as if the job has been run locally.

Here we use a [pytorch example](https://github.com/pytorch/examples/tree/master/word_language_model) to show how to 
train a machine learning model with a `cloudor` job.

The docker images is pushed to "cloudorio/pytorch_examples:1.6.0-cuda10.1-cudnn7-runtime". 
The [docker file](https://github.com/cloudor-io/dockers/blob/master/pytorch_examples/Dockerfile) is simple:

```Dockerfile
FROM pytorch/pytorch:1.6.0-cuda10.1-cudnn7-runtime
COPY ./word_language_model /opt/pytorch/examples/word_language_model
```

Notice that we only copied the code to the image. 
Now suppose the training data is stored locally at `/tmp/data/wikitext-2`. We can set up the `cloudor` yaml file as follows:

```yaml
kind: job
version: v1alpha
spec:
  image: cloudorio/pytorch_examples:1.6.0-cuda10.1-cudnn7-runtime
  command: python
  args:
    - /opt/pytorch/examples/word_language_model/main.py
    - --cuda
    - --data
    - /mnt/wikitext-2
    - --onnx-export
    - /mnt/output_model/model.onnx
  input_mounts:
    - /mnt/wikitext-2
  output_mount: /mnt/output_model
vendors:
  - tag: us_west_v100
    name: aws
    instance_type: p3.2xlarge
    region: us-west-2
    inputs: # matches input_mounts
      - local_dir: /tmp/data/wikitext-2 
    output: # matches output_mount
      local_dir: ./output_model
```

In the vendor configuration, the files specifies that the input is from a local dir and the output will be downloaded to "./output_model".
They corresponds to the input\_mounts and output\_mount in the job spec section. 

When we run the above job:

```bash
$ cloudor run -f examples/input_example.yaml
2020/08/14 01:16:37 uploading local dir /tmp/data/wikitext-2
2020/08/14 01:16:53 job submitted successfully, to be running on aws/us-west-2/p3.2xlarge with timoue 30 minutes
2020/08/14 01:18:28 job status "started": started running docker image cloudorio/pytorch_examples:1.6.0-cuda10.1-cudnn7-runtime
2020/08/14 01:20:34 job status "running": container job started running, image: cloudorio/pytorch_examples:1.6.0-cuda10.1-cudnn7-runtime
2020/08/14 01:40:50 job status "returned": -------------------------
| end of epoch  39 | time: 28.80s | valid loss  4.74 | valid ppl   114.99
-----------------------------------------------------------------------------------------
| epoch  40 |   200/ 2983 batches | lr 0.02 | ms/batch  9.27 | loss  4.21 | ppl    67.64
...
| epoch  40 |  1800/ 2983 batches | lr 0.02 | ms/batch  9.23 | loss  4.11 | ppl    61.12
| epoch  40 |  2000/ 2983 batches | lr 0.02 | ms/batch  9.20 | loss  4.15 | ppl    63.40
| epoch  40 |  2200/ 2983 batches | lr 0.02 | ms/batch  9.23 | loss  4.03 | ppl    56.32
| epoch  40 |  2400/ 2983 batches | lr 0.02 | ms/batch  9.24 | loss  4.06 | ppl    58.14
| epoch  40 |  2600/ 2983 batches | lr 0.02 | ms/batch  9.24 | loss  4.10 | ppl    60.26
| epoch  40 |  2800/ 2983 batches | lr 0.02 | ms/batch  9.26 | loss  4.03 | ppl    56.20
-----------------------------------------------------------------------------------------
| end of epoch  40 | time: 28.82s | valid loss  4.74 | valid ppl   114.98
-----------------------------------------------------------------------------------------
=========================================================================================
| End of training | test loss  4.69 | test ppl   108.59
=========================================================================================
The model is also exported in ONNX format at /mnt/output_model/model.onnx
2020/08/14 01:40:55 job status "finished": output uploaded to XXXX/20200814-1g4mNJS3IhRbGt3rsyqig85Luou/output.zip succeeded
```

Once returned, the model is automatically downloaded to "./output_model" directory.
