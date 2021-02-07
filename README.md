# Hyperparemeter Optimization with MLflow

I have an Apple Silicon ARM Mac and I had to do some work to spin up my Data Science environment to e.g. get `mlflow` and `Tensorflow` to work. 

Hopefully these notes will help someone out on the internet.


The hyperparameter optimization example is copied from [mlflow/examples/hyperparam](https://github.com/mlflow/mlflow/tree/master/examples/hyperparam)
- `hyperparam` shows how to do hyperparameter tuning with MLflow and some popular optimization libraries.
- See the original `examples/hyperparam/README.rst` for details
    > This example tries to optimize the RMSE metric of a Keras deep learning model on a wine quality dataset.

# Install Conda on ARM
Install Miniforge for Mac OS X arm64 (Apple Silicon): https://github.com/conda-forge/miniforge Remember not to run this in a Rosetta Terminal, if you created one.

- Type `which python` in your terminal. It should point to your Miniforge version, which for me, was at `/Users/YOURUSERNAMEHERE/miniforge3/bin/python`

# Tensorflow
- Apple has created a TensorFlow that is optimized for their ARM Processors and GPUs. See [pre-releases](https://github.com/apple/tensorflow_macos/releases/tag/v0.1alpha2)
    > After downloading and unpacking the archive, run `/bin/bash ./tensorflow_macos/install_venv.sh --help` to see options for creating a new virtual environment with these packages installed or for installing them into an existing environment.


# Creating the conda environment
```sh
conda create --name mlflow38 python=3.8
conda activate mlflow38
# install packages
conda install -c conda-forge scikit-learn
pip3 install -r requirements.txt
```
After downloading and unpacking the archive, navigate to the `Downloads`:
```sh
# install tensorflow
./tensorflow_macos/install_venv.sh --python=/Users/YOURUSERNAMEHERE/miniforge3/envs/mlflow38/bin/python3 --prompt 
```

This opens a prompt where you are asked for a path to new or existing virtual environment
```
Path to new or existing virtual environment [default: /Users/YOURUSERNAMEHERE/tensorflow_macos_venv/]: /Users/YOURUSERNAMEHERE/miniforge3/envs/mlflow38/
```


# Hyperparameter Tuning Example
You can run any of the targets as a standard MLflow run.
```sh
mlflow experiments create -n individual_runs
```
Creates experiment for individual runs and return its experiment ID.

```sh
mlflow experiments create -n hyper_param_runs
```

Creates an experiment for hyperparam runs and return its experiment ID.

```sh
mlflow run -e train --experiment-id <individual_runs_experiment_id> examples/hyperparam --no-conda
```

Runs the Keras deep learning training with default parameters and log it in experiment `individual_runs_experiment_id`.

```sh
mlflow run -e random --experiment-id <hyperparam_experiment_id> examples/hyperparam --no-conda
```

```sh
mlflow run -e gpyopt --experiment-id <hyperparam_experiment_id> examples/hyperparam --no-conda
```

```sh
mlflow run -e hyperopt --experiment-id <hyperparam_experiment_id> examples/hyperparam --no-conda
```

Runs the hyperparameter tuning with either random search or GpyOpt or Hyperopt and log the
results under `hyperparam_experiment_id`.

You can compare these results by using `mlflow ui`.

# Note
1. Add `--no-conda` to the `mlflow run` commands.

> By default mlflow run installs all dependencies using conda. To run a project without using conda, you can provide the --no-conda option to mlflow run. In this case, you must ensure that the necessary dependencies are already installed in your Python environment. [(quickstart)](https://www.mlflow.org/docs/latest/quickstart.html)

`Tensorflow` cannot be installed with `pip` in a virtual environment running on ARM, therefore we have to use the existing `mlflow38` environment.

---

2. End of maintenance for [GPyOpt](https://github.com/SheffieldML/GPyOpt)
