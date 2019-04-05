
## Export the TensorFlow Graph

Before exporting the graph we first need to identify a checkpoint candidate in the `pets-pvc` pvc under
`${MOUNT_PATH}/train` which is where the training job is saving the checkpoints.

To see what's being saved in `${MOUNT_PATH}/train` while the training job is running you can use:

```
kubectl -n kubeflow exec tf-training-job-master-0 -- ls /pets_data/train
```

This will list the contents of the train directory. The output should something like this:

```
checkpoint
events.out.tfevents.1534465587.tf-training-job-chief-0
events.out.tfevents.1534525812.tf-training-job-chief-0
graph.pbtxt
model.ckpt-0.data-00000-of-00001
model.ckpt-0.index
model.ckpt-0.meta
model.ckpt-167.data-00000-of-00001
model.ckpt-167.index
model.ckpt-167.meta
model.ckpt-334.data-00000-of-00001
model.ckpt-334.index
model.ckpt-334.meta
pipeline.config
```

Once you have identified the checkpoint next step is to configure the checkpoint in the `export-tf-graph-job` component and apply it.

```
make pets/model/export CHECKPOINT=model.ckpt-<number>
```

Where `model.ckpt-<number>` is a file under the `/pets_data/train` directory.

Wait until the job has finished:

```
make k8s/pods

export-tf-graph-job-x9hk2                                         0/1     Completed   0          81s
```

Then run:

```
make pets/model/move
```

To see the default values and params of the `export-tf-graph-job` component look at: [params.libsonnet](./ks-app/components/params.libsonnet)

Once the job is completed a new directory called `exported_graphs` under `/pets_data` in the pets-data-claim PVC
will be created containing the model and the frozen graph.

## Next

- Serve the model with [GPU](./tf_serving_gpu.md)
