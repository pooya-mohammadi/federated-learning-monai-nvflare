# We use projectmonai/monai:0.8.1 as base image. Do not modify!
# Please install any additional dependencies on top of it.
FROM projectmonai/monai:0.8.1
ENV DEBIAN_FRONTEND noninteractive

# install dependencies
# RUN python -m pip install --upgrade pip
RUN pip3 install tensorboard sklearn torchvision
RUN pip3 install monai==0.8.1
RUN pip3 install nvflare==2.0.16
RUN pip3 install protobuf==3.20.*
# mount nvflare from source
#RUN pip install tenseal
#WORKDIR /code
#RUN git clone https://github.com/NVIDIA/NVFlare.git
#ENV PYTHONPATH=${PYTHONPATH}:"/code/NVFlare"

# add your code to container
COPY code /code

# add code to path
ENV PYTHONPATH=${PYTHONPATH}:"/code"

ENV TORCH_HOME=/opt/torch
# RUN python3 /code/pt/utils/download_model.py --model_url=https://download.pytorch.org/models/resnet18-f37072fd.pth

# specify the server FQDN as commandline argument
ARG server_fqdn
RUN echo "Setting up FL workspace wit FQDN: ${server_fqdn}"

# prepare FL workspace
WORKDIR /code
RUN sed -i "s|{SERVER_FQDN}|${server_fqdn}|g" fl_project.yml
RUN python3 -m nvflare.lighter.provision -p fl_project.yml
RUN cp -r workspace/fl_project/prod_00 fl_workspace
RUN mv fl_workspace/${server_fqdn} fl_workspace/server
RUN apt-get update -y
RUN apt install telnet -y
RUN apt install iputils-ping -y
RUN apt install net-tools -y
RUN apt-get install dos2unix -y
RUN dos2unix

EXPOSE 8002
EXPOSE 8003