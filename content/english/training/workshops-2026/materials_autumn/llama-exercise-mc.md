---
weight: 566
linkTitle: "Local LLama.CPP in HPC MC"
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
An example running local llama.cpp AI models on MagicCastle with GPUs, and using them with an agentic harness.
{{< /alert >}}


## Getting locally installed llama.cpp
---

The code needs a GPU node , so lets request one in an interactive job. The job takes one full gpu-node.

{{< highlight bash >}}
salloc --partition=gpu-node --nodes=1 --gpus=1 --cpus-per-gpu=3 --mem=0 --time=0-3:00
{{< /highlight >}}

Check if we have GPUs.

{{< highlight bash >}}
hostname
nvidia-smi
{{< /highlight >}}


Now we will need the Llama.cpp executables. It is either available as a module, or can be used in Apptainer. This time we will use a local installation on MagicCastle, so we'd need first to invoke __module use __.

{{< highlight bash >}}
module use /home/software/modules/2023/x86-64-v4/CUDA/gcc12/openmpi4/cuda12.2
module load llama.cpp
which llama-cli
{{< /highlight >}}


## Running a local LLM interactively.
---


We will also need models. Models are large files , usually downloaded from HuggingFace. Some of them are pre-downloaded, find them in the __/home/shared/models__. Let us load one of them and see if it works.

In the same interactive GPU job, lets run a model from Meta.

{{< highlight bash >}}
llama-cli -m /home/shared/models/Meta-Llama-3.1-8B-Instruct-Q4_K_M.gguf -c 4096 
{{< /highlight >}}


Now we have the code, the model and the GPU, and can try loading the model into LLama.cpp and issuing a simple prompt.

Does it say anything useful? Try other models?

## Running local inference engine Llama.cpp as a SLURM job
---

We'd want to run a persistent server job that does not quit. We will start llama-server, send the LLama.cpp into background with __&__ and we will __wait__. We will also set an authentication key.

> !!Do not forget to use __scancel JobID__ when done to prevent wasting a GPU!!

Lets use VI editor and save the following job script as __llama-server.job__:


{{< highlight bash >}}
#!/bin/bash
#SBATCH --job-name=llama-server
#SBATCH --partition=gpu-node
#SBATCH --gpus=1
#SBATCH --cpus-per-task=3 --mem=0 --time=0-3:00:00
#SBATCH --output=llama-%j.out
#SBATCH --error=llama-%j.err

#if on Grex, use --partition=gpu,stamps-b,livi-b,mcordcpu-b,agpu,lgpu --mem=16gb

#MODEL_PATH=~/models/Spark-X2.6-4B-Q6_K_S.gguf

PORT=8080
KEY="my-key"

#on Grex !!!ADD YOUR user Number here prefixed by 8!!! 8251 for PORT

#on Grex do modules below:
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

Then submit the script with __sbatch llama-server.slurm__ command. 


Connect to the model with curl to see if the server is alive. Replace the node name with what you see in __sq__ for your GPU job. (If using different PORT, replace 8080 with the port ).

{{< highlight bash >}}
curl -H 'Authorization: Bearer my-key'   http://gpu-node1:8080/health

curl -H 'Authorization: Bearer my-key'   http://gpu-node1:8080/models | jq .
{{< /highlight >}}

The model should be servable if the two above commands succeed. We also can see the models it has configured to use.

## Next exercise: Connecting PI agent to LLama

We will try to use our agentic harness now! Or at least connecting it. One lightweight, popular harness is [PI](https://pi.dev) .

The code needs some CPU and memory, but no GPUs , so lets request aniother interactive job. The job takes one full gpu-node.

{{< highlight bash >}}
salloc --partition=node --nodes=1 --cpus-per-task=2 --mem=3000 --time=0-3:00
{{< /highlight >}}

Then, lets install PI. 

{{< highlight bash >}}
# trusting random **t on Internets looks like this:
curl -fsSL https://pi.dev/install.sh | sh
which pi
{{< /highlight >}}

We need to configure it. Start __pi__ in a terminal window
Then use various command to configure the API endporint to our LLama server. 
find the server name using __sq__ command and noticing where the job runs (_gpu-nodeX_ on MC, some _gXYZ_=_ on Grex). Lets use PI's command area:

 * /login llama.cpp
 * Server URL: (when asked, enter the URL as http://gpu-nodeX:8080 )
 * APi Key: (when asked, enter the model Key used in the LLama job)
 * /llama
 * Select models to "load". Use the ones we have under __/home/shared/models__
 * /model
 * Select the model to be used (there are many; use the ones we just loaded)
 
At this point, asking the model questions and trying to perform actions should work.

## try to create a SLURM job script

Lets try to create SLURM job script for some of the applications (Gromacs?) .

## Working with skills, and prompts

You may have noticed that the agent is not very smart. It needs more guidance, and these "skills" it can self-edit and improve. It also helps to save repeateable "prompts".


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
cp -r /home/shared/agent/skills/*.md  ~/.pi/agent/skills/hpc-site/references
#
ls  ~/.pi/agent/skills/hpc-site/references
{{< /highlight >}}

## try to create a SLURM job script

Lets try to create SLURM job script for some of the applications (Gromacs?) .

## Cancel the GPU job to free the resources

Use __sq__ to find your job id, and __scancel__ to cancel it.

<!-- Changes and update:
* Last revision: May 22, 2026.
-->
