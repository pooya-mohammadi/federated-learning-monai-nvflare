# Monai-NVFlare Breast Cancer

## Data
Download the data from the following address:
https://drive.google.com/file/d/1Fd9GLUIzbZrl4FrzI3Huzul__C8wwzyx/view?usp=sharing

Then add the `preprocessed` folder to `app/data/preprocessed`

Run the following code to download data lists:
```commandline
cd app
python ./code/pt/utils/download_datalists_and_predictions.py
```

## Download models
Download model before creating dockerfile, then move it to `/code/resnet18-f37072fd.pth`
```commandline
cd app/code
python pt/utils/download_model.py --model_url=https://download.pytorch.org/models/resnet18-f37072fd.pth
```
Get the model from cache and copy it to `app/checkpoints`

## Simulated training
First create `localshost` dockerfile using the following command:
```commandline
cd app
docker build --network=host -t monai-nvflare:latest --build-arg server_fqdn=localhost -f Dockerfile .
```

## Run models
First run the server:
```commandline
docker run --gpus="device=" --network=host --ipc=host --rm --shm-size=1g --ulimit memlock=-1 --ulimit stack=67108864 --name=server -e NVIDIA_VISIBLE_DEVICES="" -v "./result_server":/result -v "C:\Users\pooya\projects\federated-learning-monai-nvflare\monai_nvflare\breast-cancer-test\app\checkpoints":/opt/torch/hub/checkpoints -w /code monai-nvflare:latest /bin/bash -c "/code/start_server.sh; /code/finalize_server.sh"
```
Then the sites:
```commandline
docker run --gpus="device=0" --network=host --ipc=host --rm --shm-size=1g --ulimit memlock=-1 --ulimit stack=67108864 --name="site-1" -e NVIDIA_VISIBLE_DEVICES="0" -v "C:\Users\pooya\projects\federated-learning-monai-nvflare\monai_nvflare\breast-cancer-test\app\data":/data:ro -v "C:\Users\pooya\projects\federated-learning-monai-nvflare\monai_nvflare\breast-cancer-test\app\checkpoints":/opt/torch/hub/checkpoints -w /code monai-nvflare:latest /bin/bash -c "/code/start_site-1.sh; tail -f /dev/null"
docker run --gpus="device=0" --network=host --ipc=host --rm --shm-size=1g --ulimit memlock=-1 --ulimit stack=67108864 --name="site-2" -e NVIDIA_VISIBLE_DEVICES="0" -v "C:\Users\pooya\projects\federated-learning-monai-nvflare\monai_nvflare\breast-cancer-test\app\data":/data:ro -v "C:\Users\pooya\projects\federated-learning-monai-nvflare\monai_nvflare\breast-cancer-test\app\checkpoints":/opt/torch/hub/checkpoints -w /code monai-nvflare:latest /bin/bash -c "/code/start_site-2.sh; tail -f /dev/null"
docker run --gpus="device=0" --network=host --ipc=host --rm --shm-size=1g --ulimit memlock=-1 --ulimit stack=67108864 --name="site-3" -e NVIDIA_VISIBLE_DEVICES="0" -v "C:\Users\pooya\projects\federated-learning-monai-nvflare\monai_nvflare\breast-cancer-test\app\data":/data:ro -v "C:\Users\pooya\projects\federated-learning-monai-nvflare\monai_nvflare\breast-cancer-test\app\checkpoints":/opt/torch/hub/checkpoints -w /code monai-nvflare:latest /bin/bash -c "/code/start_site-3.sh; tail -f /dev/null"
```

The connection was complete but it didn't work. I don't know why :)