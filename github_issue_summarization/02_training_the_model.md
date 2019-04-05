# Training the model with a notebook

By this point, you should have a Jupyter notebook running at http://127.0.0.1:8000.

## Download training files

Open the Jupyter notebook interface and create a new Terminal by clicking on
menu, *New -> Terminal*. In the Terminal, clone this git repo by executing:

```bash
git clone https://github.com/cybera/kubeflow-examples examples
cd examples
git checkout cybera-modifications
```

Now you should have all the code required to complete training in the `examples/github_issue_summarization/notebooks` folder. Navigate to this folder.
Here you should see two files:

*    `Training.ipynb`
*    `seq2seq_utils.py`

## Perform training

Open the `Training.ipynb` notebook. This contains a complete walk-through of
downloading the training data, preprocessing it, and training it.

Run the `Training.ipynb` notebook, viewing the output at each step to confirm
that the resulting models produce sensible predictions.

## Export trained model files

After training completes, download the resulting files to your local machine.
The following files are needed for serving results:

* `seq2seq_model_tutorial.h5` - the keras model
* `body_pp.dpkl` - the serialized body preprocessor
* `title_pp.dpkl` - the serialized title preprocessor

Issue the following command to place these three files into the `github_issue_summarization/notebooks` folder on your local machine:

```
make github/model/download
```

_(Optional)_ You can also perform training with two alternate methods:
- [Training the model using TFJob](02_training_the_model_tfjob.md)
- [Distributed training using Estimator](02_distributed_training.md)

*Next*: [Serving the model](03_serving_the_model.md)

*Back*: [Setup a kubeflow cluster](01_setup_a_kubeflow_cluster.md)
