## Monitor your job

### View status

```
make k8s/tfjob NAME=tf-training-job
```

### View logs of individual pods

```
make k8s/pods
make k8s/logs NAME=name-of-master-pod
```

**NOTE:** When the job finishes, the pods will be automatically terminated.

While the job is running, you should see something like this in your master pod logs:

```
INFO:tensorflow:Saving checkpoint to path /pets_data/train/model.ckpt
INFO:tensorflow:Recording summary at step 819.
INFO:tensorflow:global step 819: loss = 0.8603 (19.898 sec/step)
INFO:tensorflow:global step 822: loss = 1.9421 (18.507 sec/step)
INFO:tensorflow:global step 825: loss = 0.7147 (17.088 sec/step)
INFO:tensorflow:global step 828: loss = 1.7722 (18.033 sec/step)
INFO:tensorflow:global step 831: loss = 1.3933 (17.739 sec/step)
INFO:tensorflow:global step 834: loss = 0.2307 (16.493 sec/step)
INFO:tensorflow:Recording summary at step 839
```

When the job finishes, you should see something like this in your completed/terminated master pod logs:

```
INFO:tensorflow:Starting Session.
INFO:tensorflow:Saving checkpoint to path /pets_data/train/model.ckpt
INFO:tensorflow:Starting Queues.
INFO:tensorflow:global_step/sec: 0
INFO:tensorflow:Recording summary at step 200006.
INFO:tensorflow:global step 200006: loss = 0.0091 (9.854 sec/step)
INFO:tensorflow:Stopping Training.
INFO:tensorflow:Finished training! Saving model to disk.
```

Now you have a trained model!! find it at `/pets_data/train` inside pvc `pets-pvc``.

> Of course, it took approximately 500 hours.
>
> If you don't want to wait 500 hours, run the following and wait until you see a ckpt file other than `0`:
>
> kubectl -n kubeflow exec tf-training-job-master-0 -- ls /pets_data/train

### Delete job

```
make pets/tf/delete
```

## Next
[Export the TensorFlow Graph and Serve the model with TF Serving](./export_tf_graph.md)
