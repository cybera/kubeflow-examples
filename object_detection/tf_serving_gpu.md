# Serving an object detection model with GPU

## Setup

If you followed previous steps to train the model, skip to deploy [section](#deploy-serving-component).

Alternatively, you can do the following to deploy kubeflow and get a model.

Reference
[blog](https://cloud.google.com/blog/big-data/2017/09/performing-prediction-with-tensorflow-object-detection-models-on-google-cloud-machine-learning-engine)

## Deploy serving component

> Note: If you have a limited amount of GPUs, you will need to delete some previous jobs before continuing:
>
> kubectl -n kubeflow delete tfjob tf-training-job

```
make tf/serve NAME=model1 MODEL_PATH=exported_graphs PVC=pets-pvc
```

Wait until the model is running:

```
make k8s/pods

model1-v1-754bff9578-bkv8b                                        2/2     Running     0          13m
```

## Send prediction

> Before you run the following, you need to have the following Python modules installed:
>
> pip3 install numpy
> pip3 install pil
> pip3 install PIL
> pip3 install Pillow
> pip3 install matplotlib
> pip3 install tensorflow
>

Forward traffic to the model server:

```
make forward/tf NAME=model1
```

```
cd examples/object_detection/serving_script serving_script
python3 predict.py --url=http://localhost:8000/model/model1:predict
```

The script takes an input image (by default image1.jpg) and should save the result as `output.jpg`.
The output image has the bounding boxes for detected objects.
