## Setup Kubeflow
### Requirements

 - Kubernetes cluster
 - Access to a working `kubectl` (Kubernetes CLI)
 - Ksonnet CLI: [ks](https://ksonnet.io/)

### Setup
Refer to the [getting started guide](https://www.kubeflow.org/docs/started/getting-started) for instructions on how to setup kubeflow on your kubernetes cluster. Specifically, look at the [quick start](https://www.kubeflow.org/docs/started/getting-started/#quick-start) section.
For this example, we will be using ks `nocloud` environment (on premise K8s). If you plan to use `cloud` ks environment, please make sure you follow the proper instructions in the kubeflow getting started guide.

After completing the steps in the kubeflow getting started guide you will have the following:
- A ksonnet app directory called `my-kubeflow`
- A new namespace in you K8s cluster called `kubeflow`
- The following pods in your kubernetes cluster in the `kubeflow` namespace:
```
$ kubectl -n kubeflow get pods
NAME                                      READY     STATUS    RESTARTS   AGE
ambassador-7987df44b9-4pht8               2/2       Running   0          1m
ambassador-7987df44b9-dh5h6               2/2       Running   0          1m
ambassador-7987df44b9-qrgsm               2/2       Running   0          1m
tf-hub-0                                  1/1       Running   0          1m
tf-job-operator-v1alpha2-b76bfbdb-lgbjw   1/1       Running   0          1m
```

## Overview

During the course of this tutorial you will apply a set of ksonnet components that will:

1. Create a PersistentVolume to store our data and training results.
2. Download the dataset, dataset annotations, a pre-trained model checkpoint, and the training pipeline configuration file.
3. Decompress the downloaded dataset, pre-trained model, and dataset annotations.
4. Create a TensorFlow pet record since we will be training a pet detector model.
5. Execute a distributed TensorFlow object detection training job using the previous configurations.
6. Export the trained pet detector model and serve it using TF-Serving

We have prepared a ksonnet app `ks-app` with a set of components that will be used in this example.
The components can be found at the [ks-app/components](./ks-app/components) directory in case you want to perform some
customizations.

Let's make use of the app to continue with the tutorial.

## Preparing the training data

**Note:** TensorFlow works with many file systems like HDFS and S3, you can use
them to push the dataset and other configurations there and skip the Download and Decompress steps in this tutorial.

First let's create a PVC to store the data.

```
make pets/pvc/create
```

The command above will create a PVC with `ReadWriteMany` access mode if your Kubernetes cluster
does not support this feature you can modify the `accessMode` value to create the PVC in `ReadWriteOnce`
and before you execute the tf-job to train the model add a `nodeSelector:` configuration to execute the pods
in the same node. You can find more about assigning pods to specific nodes [here](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/)

This step assumes that your K8s cluster has [Dynamic Volume Provisioning](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) enabled and
the default Storage Class is created. You can check if the assumption is ready like below (a storageclass with `(default)` notation need exist):

```
$ kubectl get storageclass
NAME                 PROVISIONER               AGE
standard (default)   kubernetes.io/gce-pd      1d
gold                 kubernetes.io/gce-pd      1d
```

Otherwise you can find that the PVC remains `Pending` status.

```
$ kubectl get pvc pets-pvc -n kubeflow
NAME        STATUS    VOLUME    CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pets-pvc    Pending                                                      28s
```

If your cluster doesn't have defined default storageclass, you can create a [PersistentVolume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) manually to make the PVC work.

Now we will get the data we need to prepare our training pipeline:

```
make pets/data/create
```

The downloaded files will be dumped into the `/pets_data`

Here is a quick description for the `get-data-job` component parameters used in the `Makefile` task:

- `mountPath` string, volume mount path.
- `pvc` string, name of the PVC where the data will be stored.
- `urlData` string, remote URL of the dataset that will be used for training.
- `urlAnnotations` string, remote URL of the annotations that will be used for training.
- `urlModel` string, remote URL of the model that will be used for fine tuning.
- `urlPipelineConfig` string, remote URL of the pipeline configuration file to use.

**NOTE:** The annotations are the result of labeling your dataset using some manual labeling tool. For this example we will use
a set of annotations generated specifically for the dataset we are using for training.

Before moving to the next set of commands make sure all of the jobs to get the data were completed:

```
make k8s/pods

get-data-job-annotations-g879d                              0/1     Completed   0          3m51s
get-data-job-config-lgdbh                                   0/1     Completed   0          3m51s
get-data-job-dataset-dfx5s                                  0/1     Completed   0          3m51s
get-data-job-model-r2qc5                                    0/1     Completed   0          3m51s
```

Now we will configure and apply the `decompress-data-job` component:

```
make pets/data/decompress
```

Here is a quick description for the `decompress-data-job` component parameters used in the `Makefile` task:

- `mountPath` string, volume mount path.
- `pvc` string, name of the PVC where the data is located.
- `pathToAnnotations` string, File system path to the annotations .tar.gz file
- `pathToDataset` string, File system path to the dataset .tar.gz file
- `pathToModel` string, File system path to the pre-trained model .tar.gz file

Before moving on to the next set of commands, make sure all of the jobs to decompress the data were completed:

```
make k8s/pods

decompress-data-job-annotations-4cxc2                       0/1     Completed   0          36m
decompress-data-job-dataset-rhh9v                           0/1     Completed   0          36m
decompress-data-job-model-jrv6t                             0/1     Completed   0          36m
```

Finally, and since TensorFlow Object Detection API uses the [TFRecord format](https://www.tensorflow.org/api_guides/python/python_io#tfrecords_format_details)
we need to create the TF pet records. For that, we wil configure and apply the `create-pet-record-job` component:

```
make pets/data/record
```

Here is a quick description for the `create-pet-record-job` component parameters used in the `Makefile` task:

- `mountPath` string, volume mount path.
- `pvc` string, name of the PVC where the data is located.
- `image` string, name of the docker image to use.
- `dataDirPath` string, the directory with the images
- `outputDirPath` string, the output directory for the pet records.

To see the default values of the components used in this set of steps look at: [params.libsonnet](./ks-app/components/params.libsonnet)

Before moving on to the next set of commands, make sure all of the record jobs were completed:

```
make k8s/pods


create-pet-record-job-c4vm7                                 0/1     Completed   0          9m
```

## Next
[Submit the TF Job](submit_job.md)
