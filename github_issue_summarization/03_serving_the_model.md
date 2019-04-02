# Serving the model

We are going to use [Seldon Core](https://github.com/SeldonIO/seldon-core) to serve the model. [IssueSummarization.py](notebooks/IssueSummarization.py) contains the code for this model. We will wrap this class into a seldon-core microservice which we can then deploy as a REST or GRPC API server.

> The model is written in Keras and when exported as a TensorFlow model seems to be incompatible with TensorFlow Serving. So we're using Seldon Core to serve this model since seldon-core allows you to serve any arbitrary model. More details [here](https://github.com/kubeflow/examples/issues/11#issuecomment-371005885).

#  Building a model server

You have two options for getting a model server

1. You can use the public model server image `gcr.io/kubeflow-examples/issue-summarization-model`

  * This server has a copy of the model and supporting assets baked into the container image
  * So you can just run this image to get a pre-trained model
  * Serving your own model using this server is discussed below

1. You can build your own model server as discussed below. For this you will need to install the [Source2Image executable s2i](https://github.com/openshift/source-to-image).


## Wrap the model into a Seldon Core microservice

Run:

```
make github/model/build-image DOCKER_USERNAME=username TAG=0.1
```

This will use [S2I](https://github.com/openshift/source-to-image)  to wrap the inference code in `IssueSummarization.py` so it can be run and managed by Seldon Core.

Now you should see an image named `<username>/issue-summarization:0.1` in your docker images.

You can push the image by running:

```
make github/model/push-image DOCKER_USERNAME=username TAG=0.1
```

> You can find more details about wrapping a model with seldon-core [here](https://github.com/SeldonIO/seldon-core/blob/master/docs/wrappers/python.md)


# Deploying the model to your kubernetes cluster

Now that we have an image with our model server, we can deploy it to our kubernetes cluster. We need to first deploy seldon-core to our cluster.

## Deploying the actual model

Now that you have seldon core deployed, you can deploy the model using the
`seldon-serve-simple-v1alpha2` prototype.

```
make github/model/serve-image DOCKER_USERNAME=username TAG=0.1
```

# Sample request and response

Seldon Core uses ambassador to route its requests. To send requests to the model, you can port-forward the ambassador container locally:

```
make forward/dashboard
```

Then run:

```
curl -X POST -H 'Content-Type: application/json' -d '{"data":{"ndarray":[["issue overview add a new property to disable detection of image stream files those ended with -is.yml from target directory. expected behaviour by default cube should not process image stream files if user does not set it. current behaviour cube always try to execute -is.yml files which can cause some problems in most of cases, for example if you are using kuberentes instead of openshift or if you use together fabric8 maven plugin with cube"]]}}' http://localhost:8080/seldon/issue-summarization/api/v0.1/predictions
```

Response

```
{
  "meta": {
    "puid": "2rqt023g11gt7vfr0jnfkf1hsa",
    "tags": {
    },
    "routing": {
    }
  },
  "data": {
    "names": ["t:0"],
    "ndarray": [["add a new property"]]
  }
}
```

*Next*: [Querying the model](04_querying_the_model.md)

*Back*: [Training the model](02_training_the_model.md)
