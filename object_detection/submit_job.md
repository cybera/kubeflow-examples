# Launch a distributed object detection training job
## Requirements

 - Docker
 - Docker Registry
 - Object Detection Training Docker Image

Build the TensorFlow object detection training image, or use the pre-built image `lcastell/pets_object_detection` in Docker hub.

## To build the image:

```
make pets/tf/image
```

## Create  training TF-Job deployment and launching it

> Note: This command uses the pre-build image. Modify the `Makefile` task to use the image you made above.

```
make pets/tf/deploy
```

Here is a quick description for the `tf-training-job` component parameters used in the `Makefile` task:

- `image` string, docker image to use
- `mountPath` string, Volume mount path
- `numGpu` number, optional param, default to 0
- `numPs` number, Number of Parameter servers to use
- `numWorkers` number, Number of workers to use
- `pipelineConfigPath` string, the path to the pipeline config file in the volume mount
- `pvc` string, Persistent Volume Claim name to use
- `trainDir` string, Directory where the training outputs will be saved

To see the default values for the `tf-training-job` component params, please take a look at the [params.libsonnet](./ks-app/components/params.libsonnet) file.

## Next
[Monitor your job](monitor_job.md)
