---
weight: 566
linkTitle: "Local LLama.CPP in HPC"
Title: "Exercise for running local models on HPC"
description: "Workshops and Training Material - 2026 - Local Models and OpenWebUI"
#titleIcon: "fa-solid fa-cubes"
categories: ["Training"]
#tags: ["Content management"]
#draft: true
#build:
# list: false
# render: false
---

## Introduction
---

{{< alert type="info" >}}
An example running local llama.cpp AI models with OpenWebUI, using Singularity and Podman containers.
{{< /alert >}}

## Getting Singularity or Apptainer, and Podman
---

We do it as always, by using modules. Some systems may have Sing./Appt. in systems PATH or in an unusual place like somewhere on CVMFS.


{{< highlight bash >}}
which singularity

module spider apptainer

module spider singularity
{{< /highlight >}}

Assuming we have found any of the above, __module load singularity__ or whatever we have found. Then, execute it.

{{< highlight bash >}}
singularity version
{{< /highlight >}}

Later we will also need Podman loaded for running OpenWebUI.

{{< highlight bash >}}
module load podman
podman version
podman ps
{{< /highlight >}}


{{< alert type="warning" >}}
Doing a lot of pulls from an external registry like DockerHub will get us banned. Pull once, use the local image after!
{{< /alert >}}

Fallback: use the image from __/home/shared/sing__ on MagicCastle, or /global/software/sing-may2026 on Grex

## Running local LLM interactively.
---

We will need some model files. They can be obtained using Wget, but we have pre-loaded them to the __../models/__ directories of our usual workshop-2026 locations.
Lets create a directory $HOME/models and make symbolic link to the pre-downloaded models now.

{{< highlight bash >}}
mkdir ~/models
cd ~/models
# wget https://huggingface.co/bartowski/Meta-Llama-3.1-8B-Instruct-GGUF/resolve/main/Meta-Llama-3.1-8B-Instruct-Q4_K_M.gguf
ln -s /global/software/ws-may2026/models/Meta-Llama-3.1-8B-Instruct-Q4_K_M.gguf $HOME/models/Meta-Llama-3.1-8B-Instruct-Q4_K_M.gguf
# wget https://huggingface.co/bartowski/Meta-Llama-3.1-8B-Instruct-GGUF/resolve/main/Meta-Llama-3.1-8B-Instruct-Q5_K_M.gguf
ln -s /global/software/ws-may2026/models/Qwen2.5.1-Coder-7B-Instruct-Q4_K_M.gguf $HOME/models/Qwen2.5.1-Coder-7B-Instruct-Q4_K_M.gguf
# wget https://huggingface.co/bartowski/Qwen2.5.1-Coder-7B-Instruct-GGUF/resolve/main/Qwen2.5.1-Coder-7B-Instruct-Q4_K_M.gguf
ln -s /global/software/ws-may2026/models/Meta-Llama-3.1-8B-Instruct-Q5_K_M.gguf $HOME/models/Meta-Llama-3.1-8B-Instruct-Q5_K_M.gguf
ls -al
du -h 
cd 
{{< /highlight >}}

On MagicCastle it would be:

{{< highlight bash >}}
mkdir ~/models
cd ~/models
# wget https://huggingface.co/bartowski/Meta-Llama-3.1-8B-Instruct-GGUF/resolve/main/Meta-Llama-3.1-8B-Instruct-Q4_K_M.gguf
ln -s /home/shared/ws-may2026/models/Meta-Llama-3.1-8B-Instruct-Q4_K_M.gguf $HOME/models/Meta-Llama-3.1-8B-Instruct-Q4_K_M.gguf
# wget https://huggingface.co/bartowski/Meta-Llama-3.1-8B-Instruct-GGUF/resolve/main/Meta-Llama-3.1-8B-Instruct-Q5_K_M.gguf
ln -s /home/shared/ws-may2026/models/Qwen2.5.1-Coder-7B-Instruct-Q4_K_M.gguf $HOME/models/Qwen2.5.1-Coder-7B-Instruct-Q4_K_M.gguf
ls -al
du -h 
cd 
{{< /highlight >}}


Now we will need the Llama.cpp executables. It is either available as a module, or can be used in Singularity. 

 * On Grex, use module called __llama-cpp__.
 * On MagicCastle, pull both llama-server and llama-full images. Use CUDA-13 for newer H100 GPUs!

{{< highlight bash >}}
# Grex instructions 
#
module spider llama-cpp
module load cuda/12.9 arch/avx2 gcc/14.3 
module load llama-cpp

which llama-cli
#
which llama-server
#
{{< /highlight >}}

or 

{{< highlight bash >}}
# MC instructions 
#
module load apptainer/1.4.5
apptainer pull 
apptainer pull
ls *.sif
{{< /highlight >}}

## Get an interactive job on a GPU compute node with salloc

On Grex, use the Workshop's GPU reservation __ws_gpu__ !

{{< highlight bash >}}
salloc --account=def-gshamov  --mem-per-cpu=16gb  --cpus-per-task=1 --gpus=1 --reservation=ws_gpu --partition=livi-b,gpu-b,stamps-b,agro-b,mcordgpu-b
{{< /highlight >}}

Wait for it to give you and interactive prompt. Check your node name and if you have GPUs!

{{< highlight bash >}}
hostname
nvidia-smi
{{< /highlight >}}


Now we have the code, the model and the GPU, and can try loading the model into LLama.cpp and issuing a simple prompt.

{{< highlight bash >}}
llama-cli -c 4096 -ngl 60 --temp 0.7 -n 256 -m ~/models/Meta-Llama-3.1-8B-Instruct-Q4_K_M.gguf   -p "Hello, how are you?"
{{< /highlight >}}

Does it say? Try other models?

## Running local inference engine Llama.cpp as a SLURM job
---

We will need to run a persistent server job that does not quit. We will send the LLama.cpp into background with __&__ and we will __wait__.

> !!Do not forget to use __scancel JobID__ when done to prevent wasting a GPU!!

Lets use VI editor and save the following job script as __server.slurm__:


{{< highlight bash >}}
#!/bin/bash
#SBATCH --job-name=llama-server
#SBATCH --partition=gpu,livi-b,agro-b,mcordgpu-b
#SBATCH --nodes=1
#SBATCH --gpus=1 --reservation=ws_gpu
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=2 --mem-per-cpu=8gb
#SBATCH --output=llama-%j.out
#SBATCH --error=llama-%j.err


MODEL_PATH=~/models/Meta-Llama-3.1-8B-Instruct-Q4_K_M.gguf
PORT=8295 # !!!ADD YOUR user Number here prefixed by 8!!!

module load cuda/12.9.1 arch/avx2 gcc/14.3
module load llama-cpp

# Run the server
llama-server \
  -m $MODEL_PATH \
  --host 0.0.0.0 \
  --port $PORT \
  -ngl 69 \
  -c 4096 \
  --n-gpu-layers 99 \
  -np 2 \
  -n 256 --temp 0.7 \
  > server.log 2>&1 &

echo "Server started on port $PORT. Use SSH tunnel to access."
wait
   
{{< /highlight >}}

Then submit the script with __sbatch server.slurm__ command. 

Now we will need to connect to the server. From a Desktop. On Grex, lets use OOD!

 * Navigate to [https://ood.hpc.umanitoba.ca](https://ood.hpc.umanitoba.ca) , 
 * pick Desktop, _genoa_ partition, add 8 CPUs , submit and connect
 * get a Terminal
 
Then, get yourself an SSH tunnel to the node your Llama is running on. In the terminal

{{< highlight bash >}}
sq 
# note the hostname for the Llama-server job
export MYPORT=8295 # add your user number here; use the node instead of n999 below
ssh -fNL $MYPORT:n999$MYPORT n999.local
#no output expectd. Do not close the terminal
{{< /highlight >}}

Use the Firefox container and SSH port forwarding as follows.

{{< highlight bash >}}
# singularity pull docker://linuxserver/firefox
ln -s /home/software/sing-may2026/firefox_latest.sif ./firefox_latest.sif

singularity exec firefox_latest.sif firefox
{{< /highlight >}}

Then, navigate the browser to __http://localhost:MYPORT__
Must be able to  chat with the model!
Kill Fifefox when done.

## Next exercise: Connecting OpenWebUI to LLama


We need Podman to run the OpenWebUI service right in our Desktop session.
We will use Firefox to connect to it, on a port 8080 which is hardcoded in OpenWebUI.

Later we will also need Podman loaded for running OpenWebUI. Also, created the local directory for the data:

{{< highlight bash >}}
module load podman
podman version
podman ps
mkdir -p open-webui
{{< /highlight >}}

Then, use the Podman command below to fetch and run the OpenWebUI.

{{< highlight bash >}}
podman run -d  \
  -e WEBUI_AUTH=False \
  -v open-webui:/app/backend/data \
  --name open-webui \
  ghcr.io/open-webui/open-webui:main
{{< /highlight >}}

Then, start Firefox again and point it to the __http://localhost:8080__ . 
Confgure OpenWebUI to see our Llama at http://n999:MYPORT in the Web UI: Admin - Connections - Add Connection

## Use Connecting OpenWebUI to make a RAG?

TBD! Just explore the UI in Firefox at __http://localhost:8080__
"Knowledge base" can be added by uploading some Markdown files and using local builtin vectoring models in OpenWebUI.

<!-- Changes and update:
* Last revision: May 22, 2026.
-->
