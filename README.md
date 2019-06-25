# ml-app-template

An ML project template with sensible defaults:
- Dockerised dev setup
- Unit test setup
- Automated tests for model metrics
- CI pipeline as code

For infrastructure-related stuff (e.g. provisioning of CI server, deployments, etc.), please refer to https://github.com/ThoughtWorksInc/ml-cd-starter-kit.

## Getting started

1. Fork repository: https://github.com/ThoughtWorksInc/ml-app-template
2. Clone repository: `git clone https://github.com/YOUR_USERNAME/ml-app-template`
3. To develop on local environment with installed Python packages, run: `pipenv install` then activate environment with `pipenv shell` 
    3.b. to run anything without activating the virtual environment, for example, nosetests, try `pipenv run nosetests`
4. Install Docker ([Mac](https://docs.docker.com/docker-for-mac/install/), [Linux](https://docs.docker.com/install/linux/docker-ce/ubuntu/))
5. Start Docker on your desktop
6. Build image and start container:

```shell
# build docker image [Mac/Linux users]
docker build . -t ml-app-template

# build docker image [Windows users]
MSYS_NO_PATHCONV=1 docker build . -t ml-app-template

# start docker container [Mac/Linux users]
docker run -it  -v $(pwd):/home/ml-app-template \
                -p 8080:8080 \
                -p 8888:8888 \
                ml-app-template bash

# start docker container [Windows users]
winpty docker run -it -v C:\\Users\\path\\to\\your\\ml-app-template:/home/ml-app-template -p 8080:8080 -p 8888:8888 ml-app-template bash
# Note: to find the path, you can run `pwd` in git bash, and manually replace forward slashes (/) with double backslashes (\\)
```

You're ready to roll! Here are some common commands that you can run in your dev workflow. Run these in the container.

```shell
# add some color to your terminal
source bin/color_my_terminal.sh

# activate virtual environment for python
pipenv shell

# run unit tests
nosetests

# run unit tests in watch mode and color output
nosetests --with-watch --rednose --nologcapture

# train model
SHOULD_USE_MLFLOW=false python src/train.py

# start flask app in development mode
python src/app.py

# make requests to your app
# 1. In your browser, visit http://localhost:8080
# 2. Open another terminal in the running container (detailed instructions below) and run:
bin/predict.sh http://localhost:8080

# You can also use this script to test your deployed application later:
bin/predict.sh http://my-app.com
```

Here are some other commands that you may find useful
```shell
# see list of running containers
docker ps

# start a bash shell in a running container
docker exec -it <container-id> bash

# starting jupyter notebook server on http://localhost:8888
jupyter notebook --ip 0.0.0.0 --no-browser --allow-root
```

## What's in this repo?

We've created a project template to help you with the boilerplate code that we usually have to write in any typical project.

To reduce incidental complexity, we used a simple dataset (boston housing prices) to train a simple linear regression model. Replace the (i) data, (ii) data preprocessing code and (iii) model specification for your use case.

This is the project structure:

```sh
.
├── Dockerfile
├── README.md
├── requirements-dev.txt              # specify dev dependencies (e.g. jupyter) here
├── requirements.txt                  # specify app dependencies here
├── ci.gocd.yaml                      # specify your CI pipeline here
└── src                               # place your code here
    ├── app.py
    ├── app_with_logging.py           
    ├── tests                         # place your tests here
    │   ├── test.py
    │   └── test_model_metrics.py
    └── settings.py                   # define environment variables here
    └── train.py
├── bin                               # store shell scripts here
│   ├── color_my_terminal.sh
│   ├── configure_venv_locally.sh
│   ├── predict.sh
│   ├── start_server.sh
│   ├── test.sh
│   ├── test_model_metrics.sh
│   └── train_model.sh
├── docs
│   ├── FAQs.md
│   └── mlflow.md
├── models                            # serialize stuff here
│   ├── _keep
│   ├── column_order.joblib
│   └── model.joblib

```

For logging, `app_with_logging.py` contains the code for logging (i) inputs to the model, (ii) model outputs and (iii) [LIME](https://github.com/marcotcr/lime) metrics. You can refer to this file to send logs to elasticsearch using fluentd. To keep the main app simple to accessible to people who may not be familiar with these technologies, we've kept it in a separate file `app_with_logging.py` for reference.

## IDE configuration

Please refer to [FAQs](./FAQs.md) for instructions on how to configure VS Code or PyCharm to give you intellisense and auto-complete suggestions as you code.

## Infrastructure

To provision the infrastructure used in this repo (e.g. GoCD, MLFlow, EFK), please check out the [`ml-cd-starter-kit`](https://github.com/ThoughtWorksInc/ml-cd-starter-kit) repo and follow the instrutions in the README.

When you're done setting up the infrastructure, do the following:
- in `src/settings.py`, update the ip addresses with that of your own infrastructure.
- in `ci.gocd.yaml`, replace `davified/ml-app-template` with `YOUR_USERNAME/YOUR_IMAGE_NAME`

## Troubleshooting

If you encounter any errors, please refer to [FAQs](./docs/FAQs.md) for a list of common errors and how to fix them.
