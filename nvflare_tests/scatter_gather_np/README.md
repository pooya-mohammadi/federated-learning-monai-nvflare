# Scatter and Gather with Numpy
Here I present all the test I've done to run nvflare on my windows example. 
I've used the following code to run the tests.

# Docker
First build the docker image using the following command:
```commandline
cd container_ex1
docker build -t nvflare-pt:latest -f Dockerfile .
```

Then, run the docker-compose using the following code:
```commandline
cd nvflare_tests/scatter_gather_np
docker-compose up --build
```
It will print `hello-world` at each second. Open a new terminal and run the following command:
```commandline
docker-compose exec -it scatter_gather_np bash
```

You should be able to see something like the following:
```commandline
root@c578dc34ca69:/workspace#
```

## Check nvflare installation
```commandline
nvflare simulator -h
```
You should be able to see something like the following:
```commandline
usage: nvflare [-h] [--version] {poc,preflight_check,provision,simulator,dashboard,authz_preview} ...

nvflare

optional arguments:
  -h, --help            show this help message and exit
  --version, -V         print nvflare version

subcommands:
  sub command parser

  {poc,preflight_check,provision,simulator,dashboard,authz_preview}
root@c67e14e96261:/workspace/my-workspace# nvflare simulator -h
usage: nvflare simulator [-h] [-w WORKSPACE] [-n N_CLIENTS] [-c CLIENTS] [-t THREADS] [-gpu GPU] [-m MAX_CLIENTS] job_folder

positional arguments:
  job_folder

optional arguments:
  -h, --help            show this help message and exit
  -w WORKSPACE, --workspace WORKSPACE
                        WORKSPACE folder
  -n N_CLIENTS, --n_clients N_CLIENTS
                        number of clients
  -c CLIENTS, --clients CLIENTS
                        client names list
  -t THREADS, --threads THREADS
                        number of parallel running clients
  -gpu GPU, --gpu GPU   list of GPU Device Ids, comma separated
  -m MAX_CLIENTS, --max_clients MAX_CLIENTS
                        max number of clients
```

# Copy the codes
```commandline
cd /workspace/NVFlare
git checkout main
mkdir /workspace/my-workspace
cd /workspace/my-workspace
mkdir simulator-example
cp -rf /workspace/NVFlare/examples/hello-numpy-sag simulator-example/
mkdir simulator-example/hello-numpy-sag/app/custom
cp -rf /workspace/NVFlare/nvflare/app_common/np/* simulator-example/hello-numpy-sag/app/custom/
mkdir simulator-example/workspace
```

# Start the poc mode:
To create two sites and proper server run the following.
```commandline
nvflare poc --prepare -n 2
```

If you want to change the workspace, run the following before the above-mentioned command:
```commandline
NVFLARE_POC_WORKSPACE="/tmp/nvflare/poc2"
```

Then run start the poc with admin client:
```commandline
nvflare poc --start
```


Add the name of the new np trainer to the `config_fed_client.json` file:
```commandline
nano simulator-example/hello-numpy-sag/app/config/config_fed_client.json
# Add the following line under the `args` section of `components` and `pt_learner.PTLearner`
"data_path": "/workspace/my-workspace/data"
```

Then run the following command which creates two sites for training.
```commandline
nvflare simulator -w simulator-example/workspace -n 2 -t 2 simulator-example/hello-pt-tb
```

After the training is done run the following:
```commandline
tensorboard --logdir simulator-example/workspace/simulate_job/tb_events/
```

In your local system browse the following link:
http://localhost:6006/

You should be able to see the training logs based on different sites:
![img.png](img.png)