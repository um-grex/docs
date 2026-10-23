---
weight: 566
linkTitle: "Local LLama.CPP in HPC"
Title: "Exercise for running local models on HPC"
description: "Workshops and Training Material - 2026 - Local Models and PI agent"
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
An example running local llama.cpp AI models on MagicCastle with GPUs, and using them with the PI agentic harness.
{{< /alert >}}


## Getting locally installed llama.cpp
---

The code needs a GPU node , so request one in an interactive job.

salloc --partition=gpu-node --nodes=1 --gpus=1 --cpus-per-gpu=3 --mem=0 --time=0-3:00

We do it as always, by using modules , but need first to invoke __module use __.

{{< highlight bash >}}
module use /home/software/modules/2023/x86-64-v4/CUDA/gcc12/openmpi4/cuda12.2
module load llama.cpp
which llama-cli
{{< /highlight >}}


## Running local LLM interactively.
---


We will also need models. They will be in the __/home/shared/models__. Let us load one of them and see if it works.

{{< highlight bash >}}
llama-cli -m rnj-1.5-instruct-q6_k.gguf -c 4096
{{< /highlight >}}


## Running local LLM interactively.
---

We will need some model files. They can be obtained using Wget, but we have pre-loaded them to the __../models/__ directories of our usual workshop-2026 locations.
Lets create a directory $HOME/models and make symbolic link to the pre-downloaded models now.


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

## Get an interactive job on a GPU compute node with salloc

On Grex, use the Workshop's GPU reservation __ws_gpu__ !

{{< highlight bash >}}
salloc   --mem-per-cpu=16gb  --cpus-per-task=1 --gpus=1 --reservation=ws_gpu --partition=livi-b,gpu-b,stamps-b,agro-b,mcordgpu-b
{{< /highlight >}}

( On MC, use a whole GPU VM as __salloc \-\-gpus=1 \-\-partition=gpu-node \-\-mem=0__ . Do not run it on Grex. )

Wait for it to give you and interactive prompt. Check your node name and if you have GPUs!

{{< highlight bash >}}
hostname
nvidia-smi
{{< /highlight >}}


Now we have the code, the model and the GPU, and can try loading the model into LLama.cpp and issuing a simple prompt.

{{< highlight bash >}}
llama-cli -c 4096 -ngl 60 --temp 0.7 -n 256 -m ~/models/Meta-Llama-3.1-8B-Instruct-Q4_K_M.gguf   -p "Hello, how are you?"
{{< /highlight >}}


( On MC, prefix the line above with __apptainer exec ./llama.cpp_full-cuda.sif /app/llama-cli__ . Do not run it on Grex)

Does it say? Try other models?

## Running local inference engine Llama.cpp as a SLURM job
---

We will need to run a persistent server job that does not quit. We will send the LLama.cpp into background with __&__ and we will __wait__.

> !!Do not forget to use __scancel JobID__ when done to prevent wasting a GPU!!

Lets use VI editor and save the following job script as __server.slurm__:


{{< highlight bash >}}
#!/bin/bash
#SBATCH --job-name=llama-server
#SBATCH --partition=gpu-node
#SBATCH --gpus=1
#SBATCH --cpus-per-task=3 --mem=0 --time=0-3:00:00
#SBATCH --output=llama-%j.out
#SBATCH --error=llama-%j.err


#MODEL_PATH=~/models/Spark-X2.6-4B-Q6_K_S.gguf

PORT=8080
KEY="my-key"

#on Grex !!!ADD YOUR user Number here prefixed by 8!!!

#
#module load cuda/12.9.1 arch/avx2 gcc/14.3
#module load llama-cpp

module use /home/software/modules/2023/x86-64-v4/CUDA/gcc12/openmpi4/cuda12.2
module load llama.cpp
which llama-cli

# Run the server in model router mode (directory)

llama-server   --host 0.0.0.0   --port $PORT   -c 8192 -n 4096   -ngl 99   --jinja   --temp 0.7   -t 2  --models-dir /home/shared/models  --no-models-autoload --api-key "$KEY"   > server.log 2>&1 &

echo "Server started on port $PORT. Use SSH tunnel to access."
wait
# done   
{{< /highlight >}}

Then submit the script with __sbatch server.slurm__ command. 


connect to the model with curl to see if the server is alive. Replace the node name with what you see in __sq__ for your GPU job.

curl http://gpu-node1:8080/v1/models | jq 

curl -H 'Authorization: Bearer my-key'   http://gpu-node1:8080/health

The model should be servable if the two above commands succeed.

## Next exercise: Connecting PI agent to LLama

We will try to use our agentic harness now! Or at least connecting it.
One lightweight, popular harness is PI.

{{< highlight bash >}}
# trusting random **t on Internets looks like this:
curl -fsSL https://pi.dev/install.sh | sh
which pi
{{< /highlight >}}

We need to configure it. Start __pi__ in a terminal window
Then use various command to configure the API endporint to our LLama server. 
find the server name using __sq__ command and noticing where the job runs (_gpu-nodeX_).

 * /login llama.cpp
 * Server URL: (when asked, enter the URL as http://gpu-nodeX:8080 )
 * APi Key: (when asked, enter the model Key used in the LLama job)
 * /llama
 * Select models to "load". Use the ones we have under __/home/shared/models__
 * /model
 * Select the model to be used (there are many; use the ones we just loaded)
 
At this point, asking the model questions and trying to perform actions should work.

## Working with agents, skills, and prompts

You may have noticed that the agent is not very smart. It needs more guidance, and these "skills" it can self-edit and improve. It also helps to save repeateable "prompts".
Some of these are global, per user, and some per project

{{< highlight bash >}}
# finding where the confuguration is
ls ~/.pi/agent/
# would have some 
mkdir -p ~/.pi/agent/prompts
mkdir -p ~/.pi/agent/skills
mkdir -p ~/.pi/agent/skills/hpc-site/references
{{< /highlight >}}

Use VI to create a prompt.

{{< highlight bash >}}
vim ~/.pi/agent/prompts/learn.md
#
vim ~/.pi/agent/skills/hpc-site/SKILL.md
{{< /highlight >}}

We can copy our long pre-made "skills" into the references:

{{< highlight bash >}}
cp -r /home/shared/skills/*.md  ~/.pi/agent/skills/hpc-site/references
#
ls vim ~/.pi/agent/skills/hpc-site/references
{{< /highlight >}}

## Cancel the GPU job to free the resources

Use __sq__ to find your job id, and __scancel__ to cancel it.

<!-- Changes and update:
* Last revision: May 22, 2026.
-->
