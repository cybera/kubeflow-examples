# Setup Kubeflow

In this section, you will setup Kubeflow on an existing Kubernetes cluster.

## Requirements

* brew update && brew install azure-cli
* brew install source-to-image
* brew install ksonnet/tap/ks
* brew install kubernetes-cli
* pip3 install https://storage.googleapis.com/ml-pipeline/release/0.1.12/kfp.tar.gz --upgrade
* A Docker hub account with Docker installed on your workstation

Then log in to Azure:

```
az login
```

Deploy the Azure cluster:

```
make azure/setup NAME=jdoe
```

Once the cluster is running:

```
make kubeflow/setup
```

After completing that, you should have the following ready:

* A ksonnet app in a directory named `kf/ks_app`
* An output similar to this for `kubectl -n kubeflow get pods` command

```bash
NAME                                                      READY     STATUS         RESTARTS   AGE
ambassador-5cf8cd97d5-6qlpz                               1/1       Running        0          3m
ambassador-5cf8cd97d5-rqzkx                               1/1       Running        0          3m
ambassador-5cf8cd97d5-wz9hl                               1/1       Running        0          3m
argo-ui-7c9c69d464-xpphz                                  1/1       Running        0          3m
centraldashboard-6f47d694bd-7jfmw                         1/1       Running        0          3m
cert-manager-5cb7b9fb67-qjd9p                             1/1       Running        0          3m
cm-acme-http-solver-2jr47                                 1/1       Running        0          3m
ingress-bootstrap-x6whr                                   1/1       Running        0          3m
jupyter-0                                                 1/1       Running        0          3m
jupyter-chasm                                             1/1       Running        0          49s
katib-ui-54b4667bc6-cg4jk                                 1/1       Running        0          3m
metacontroller-0                                          1/1       Running        0          3m
minio-7bfcc6c7b9-qrshc                                    1/1       Running        0          3m
ml-pipeline-b59b58dd6-bwm8t                               1/1       Running        0          3m
ml-pipeline-persistenceagent-9ff99498c-v4k8f              1/1       Running        0          3m
ml-pipeline-scheduledworkflow-78794fd86f-4tzxp            1/1       Running        0          3m
ml-pipeline-ui-9884fd997-7jkdk                            1/1       Running        0          3m
ml-pipelines-load-samples-668gj                           0/1       Completed      0          3m
mysql-6f6b5f7b64-qgbkz                                    1/1       Running        0          3m
pytorch-operator-6f87db67b7-nld5h                         1/1       Running        0          3m
spartakus-volunteer-7c77dc796-7jgtd                       1/1       Running        0          3m
studyjob-controller-68c6fc5bc8-jkc9q                      1/1       Running        0          3m
tf-job-dashboard-5f986cf99d-kb6gp                         1/1       Running        0          3m
tf-job-operator-v1beta1-5876c48976-q96nh                  1/1       Running        0          3m
vizier-core-78f57695d6-5t8z7                              1/1       Running        0          3m
vizier-core-rest-7d7dd7dbb8-dbr7n                         1/1       Running        0          3m
vizier-db-777675b958-c46qh                                1/1       Running        0          3m
vizier-suggestion-bayesianoptimization-7f46d8cb47-wlltt   1/1       Running        0          3m
vizier-suggestion-grid-64c5f8bdf-2bznv                    1/1       Running        0          3m
vizier-suggestion-hyperband-8546bf5885-54hr6              1/1       Running        0          3m
vizier-suggestion-random-c4c8d8667-l96vs                  1/1       Running        0          3m
whoami-app-7b575b555d-85nb8                               1/1       Running        0          3m
workflow-controller-5c95f95f58-hprd5                      1/1       Running        0          3m
```

## Port Forwarding

Run:

```
make forward/dashboard
```

And then access http://localhost:8080 in a web browser.

Confirm you can see a dashboard.

## Launch a Jupyter Notebook;

* Click on Notebooks.
* Change the namespace to "kubeflow"
* Click the +
* Name the notebook something like `user1`
* Choose the `tensorflow-1.12.0-notebook-gpu:v0.5.0` image.
* Change CPU to 1
* Change Memory to 2.0Gi
* Set Extra Resources to `{"nvidia.com/gpu":"1"}`
* Click Spawn

Back in your terminal, wait until the notebook has launched:

```
make k8s/pods
```

You will initially see:

```
user1-0                                                     0/1     ContainerCreating   0          30s
```

Wait until it's:

```
user1-0                                                     1/1     Running   0          55s
```

Then in the web browser, click the three dots under Actions and choose "Connect"


## Summary

*   We created a ksonnet app for our kubeflow deployment: `ks_app`.
*   We deployed the default Kubeflow components to our Kubernetes cluster.
*   We created a disk for storing our training data.
*   We connected to JupyterHub and spawned a new Jupyter notebook.
*   For additional details and self-paced learning scenarios related to this
    example, see the
    [Resources](https://www.kubeflow.org/docs/started/getting-started/#resources)
    section of the
    [Getting Started Guide](https://www.kubeflow.org/docs/started/getting-started/).

*Next*: [Training the model with a notebook](02_training_the_model.md)
