# Querying the model

In this section, you will setup a barebones web server that displays the
prediction provided by the previously deployed model.

The following steps describe how to build a docker image and deploy it locally,
where it accepts as input any arbitrary text and displays a machine-generated
summary.


## Prerequisites

Ensure that your model is live and listening for HTTP requests as described in
[serving](03_serving_the_model.md).


## Build the front-end docker image

To serve the front-end docker image, issue the following commands:

> You will need to generate a github token with no permissions enabled

```bash
make github/ui/build TOKEN=<github token> DOCKER_USERNAME=username
```

## View results from the frontend

```bash
make github/ui/forward
```

In a browser, navigate to `http://localhost:8080/`, where
you will be greeted by a basic website. Press the *Populate Random Issue*
button, then click *Generate Title* to view
a summary that was provided by your trained model.

*Next*: [Teardown](05_teardown.md)

*Back*: [Serving the Model](03_serving_the_model.md)
