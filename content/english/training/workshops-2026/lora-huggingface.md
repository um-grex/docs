---
weight: 1500
linkTitle: "LoRa walkthrough"
Title: "LoRa walkthrough"
description: "Workshops and Training Material - 2025 AI LoRA example"
#titleIcon: "fa-solid fa-cubes"
categories: ["Training"]
#tags: ["Content management"]
draft: true
build:
 list: false
 render: false
---

## High Performance Computing Workshop - Oct 14-17, 2025
---

This is an example of training LoRa in a batch job. 
The example uses a script from [Huggingface](https://huggingface.co) diffusers package.
We assume the partiticipant already tried simple text to image generation with SD 1-5 as per _02-text-to-image-ipynb_ notebook.

### Pull Huggingface diffusers, copy data
---


We will downolad Huggingface source code from their Github using Git. 
This is needed to use their Python example training script rather than developing ours from scrath.
We will also copy the datasets for training from __pythonai__ subdirectory of our Workshop materials.


{{< highlight bash >}}
pwd
# on Grex. lets work from Project filesystem rather than Home!
#
# do: cd $HOME/projects/def-your-pi/your-user
#
# assuming the current directory is under your project as per above
cp -r /global/software/ws-oct2025/pythonai .
cd ./pythonai
pwd && ls 
# should see /home/user/pythonai ; dataset1 dataset2 notebooks

{{< /highlight >}}

>> Magic Castle has no GPUs as of October 2025! So please ignode the MC instructions below!

{{< highlight bash >}}

# on MC
cp -r /home/shared/pythonai ~/scratch/
cd ~/scratch/pythonai
pwd && ls
# should see ; dataset1 dataset2 notebooks

{{< /highlight >}}


{{< highlight bash >}}

# clone the HF repository and scripts
git clone https://github.com/huggingface/diffusers.git
ls
# should see disffusers directory added

{{< /highlight >}}

We will more or less follow [Diffusers source installation](https://huggingface.co/docs/diffusers/installation?install=Python#install-from-source) in an interactive job.

### Start an interactive job on a GPU node
---

We will use workshop reservation __ws_gpu__ and (any of) reserved GPU partitions. Will need one GPU.
Please add _--account=_ if you have more than one active.

{{< highlight bash >}}
salloc --time=0-2:00 --partition=agro-b,mcordgpu-b --gpus=1 --cpus-per-gpu=6 --mem=50gb  --reservation=ws_gpu
{{< /highlight >}}

We should see a GPU information from _nvidia-smi_ there. Will get either a V100 or A30.

###  Create a virtualenv and instal packages
---

We will need to load CUDA and Python 3.12. To this end, use __module spider python/3.12__ and pick the version that has a CUDA dependency.

{{< highlight bash >}}

#first, load modules
#on Grex
module purge
module load SBEnv
# loading according to spider, need the CUDA version!
module load  cuda/12.4.1 arch/avx2  gcc/13.2.0 python/3.12
python --version

{{< /highlight >}}

{{< highlight bash >}}

>> Magic Castle has no GPUs as of October 2025! So please ignode the MC instructions below!

# on MC
module load StdEnv/2023 arch/avx2 cuda python/3.12 
python --version

{{< /highlight >}}

Now that modules are loaded, we can create a new virtualenv here and call it _hf_

{{< highlight bash >}}

virtualenv hf
source hf/bin/activate
#installing packages . This can be flakey! use line by line and see if no previous step failed
pip install torch==2.8.0 arrow transformers datasets peft accelerate torchvision==0.23.0
pip install git+https://github.com/huggingface/diffusers
python -c "import torch"
deactivate

{{< /highlight >}}

We got our Diffusers virtual environment now, and it should be working! Hopefully.
We will use it in interactive and batch jobs from now on.

### Run a test LoRa text-to-image training on dataset1  
---

{{< highlight bash >}}

# now actiate the environment
source hf/bin/activate
# change directory to the Examples we cloned
cd diffusers/examples/text_to_image
pwd
ls 
#must have train_text_to_image_lora.py amongst the files there
# this is what we are goung to use! Note the path to dataset1 ../../../dataset1/train
python ./train_text_to_image_lora.py  --pretrained_model_name_or_path runwayml/stable-diffusion-v1-5   --train_data_dir ../../../dataset1/train   --output_dir ../../../lora-sd-output   --resolution 256   --train_batch_size 1   --max_train_steps 200
# go to the results directory and see if there are new weights
cd  ../../../lora-sd-output
pwd
ls
# should show something like     pytorch_lora_weights.safetensors
# now exit from the salloc job!
exit
{{< /highlight >}}

Note that we use resolution 256  on the dataset1.

Debug , try to make it run and deliver the new Weight file under the output directory.

### Run production batch jobs on dataset2
---

Now that we are sure that a LoRa training environment is good, lets try to run it as a production batch job
We will change to the larger dataset 2 and use the following script (also provided under pythonai ).

Always use the same modules and virtualenv! The job script below is for Grex.

If needed  change directory to the project filesystem on Grex. 

{{< highlight bash >}}
# must be in /home/your_user/projects/def-your_project/your_user/pythonai
sbatch trainingjob.sh
# the above may need --account= if you have more than one PI
{{< /highlight >}}

The trainingjob.sh script looks as follows:

{{< highlight bash >}}

#!/bin/bash

#SBATCH --reservation=ws_gpu
#SBATCH --partition=agro-b,mcordcpu-b
#SBATCH --cpus-per-task=6
#SBATCH --gpus=1
#SBATCH --mem=40000
#SBATCH --time=0-3:0:00

# Load requested software stack
module load SBEnv
module load cuda/12.4.1 arch/avx2 gcc/13.2.0
module load python/3.12


# now actiate the environment
source hf/bin/activate

echo "Starting run at: `date`"

python ./diffusers/examples/text_to_image/train_text_to_image_lora.py  --pretrained_model_name_or_path runwayml/stable-diffusion-v1-5   --train_data_dir dataset2/train   --output_dir lora-sd-output2   --resolution 512   --train_batch_size 1   --max_train_steps 1200

echo "Program finished with exit code ${?} at: `date`"

#
{{< /highlight >}}


### use SD-1.5 with added LoRa-optimized weights

Start a Jupyter job on Grex OOD or Magic Castle.

In the _notebooks_ folder, open a _03-text-to-image-lora.ipynb_ , correct path to the updated LoRa weights and run the inference again.
Try also merging the two LoRa weights trained from dataset1 and dataset2.
