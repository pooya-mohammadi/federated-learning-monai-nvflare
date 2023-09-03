FROM nvcr.io/nvidia/pytorch:22.09-py3

RUN python3 -m pip install -U pip
RUN python3 -m pip install -U setuptools
RUN python3 -m pip install torch torchvision tensorboard nvflare

WORKDIR /workspace/
RUN git clone https://github.com/NVIDIA/NVFlare.git
RUN python3 -m pip install -U nvflare
