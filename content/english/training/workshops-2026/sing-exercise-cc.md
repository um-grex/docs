---
weight: 2500
linkTitle: "Singularity in HPC - CC"
Title: "Exercise for singularity/apptainer for CC"
description: "Workshops and Training Material - 2026 - Using Containers"
#titleIcon: "fa-solid fa-cubes"
categories: ["Training"]
#tags: ["Content management"]
#draft: true
#build:
# list: false
# render: false
---

# Introduction
---

{{< alert type="info" >}}
How to use Apptainer aka Singularity on HPC systems: a generic task and two Genomics examples
{{< /alert >}}

## Getting Singularity or Apptainer
---

We do it as always, by using modules. Some systems may have Sing./Appt. in systems PATH or in an unusual place like somewhere on CVMFS.


{{< highlight bash >}}
which singularity
which apptainer

module spider apptainer

module spider singularity
{{< /highlight >}}

Assuming we have found any of the above, __module load singularity__ or whatever we have found. Then, try executing it.

{{< highlight bash >}}
singularity version
{{< /highlight >}}


## Trying the favourite lolcow container
---

The lolcow container is an example everyone uses to teach containers like Sing. Find it on a container repository.
DockerHub, Redhat Quay.io and Sylabs Library are the usual places. 

Use __singularity pull__ to download the image.

Try the three entrypoint(s) : run, exec, and shell for the image.

{{< alert type="warning" >}}
Doing a lot of pulls from an external registry like DockerHub will get us banned. Pull once, use the local image after!
{{< /alert >}}

Fallback: use the image from __/home/shared/__ on MagicCastle. 

## Genomics example 1: BWA Indexing
---

Lets create a working directory  __chr20__ and pull a Genome into it.

{{< highlight bash >}}
mkdir chr20
cd chr20
echo  Download chr20 reference 
wget https://hgdownload.soe.ucsc.edu/goldenPath/hg38/chromosomes/chr20.fa.gz
gunzip chr20.fa.gz
ls -al
du -h chr20.fa
{{< /highlight >}}

We want to use BWA-MEM2 code to index the genome. Find a singularity image somewhere?
Bioconductor and StaPH repositories on Quay.io are good places to start.


{{< highlight bash >}}
#apptainer pull docker://quay.io/biocontainers/bwa-mem2:2.3--he70b90d_0
#ls -lrt
# should get something like  bwa-mem2_2.3--he70b90d_0.sif
#
ln -s /home/shared/sing/bwa-mem2_2.3--he70b90d_0.sif ./bwa-mem2_2.3--he70b90d_0.sif
{{< /highlight >}}



Now we have the image and can "exec" the code from inside container. Note that we'd want to _bind-mount_ a particular directory the container expects! $PWD is the current directory.

{{< highlight bash >}}
apptainer exec --bind $PWD:/ref  bwa-mem2_2.3--he70b90d_0.sif bwa-mem2 index /ref/chr20.fa
{{< /highlight >}}

## Genomics example 2: Following Google Deepvariant tutorual.
---

[https://github.com/google/deepvariant/blob/r1.6/docs/deepvariant-quick-start.md](https://github.com/google/deepvariant/blob/r1.6/docs/deepvariant-quick-start.md)

Interestingly, it provides both Docker and Singularity instructions. We do not have enough GPUs so would need to use a batch job!

Fiest lets download the chr20 data for DeepVariant as per tutorial.

{{< highlight bash >}}

cd ~/scratch

INPUT_DIR="${PWD}/quickstart-testdata"
DATA_HTTP_DIR="https://storage.googleapis.com/deepvariant/quickstart-testdata"

mkdir -p ${INPUT_DIR}
wget -P ${INPUT_DIR} "${DATA_HTTP_DIR}"/NA12878_S1.chr20.10_10p1mb.bam
wget -P ${INPUT_DIR} "${DATA_HTTP_DIR}"/NA12878_S1.chr20.10_10p1mb.bam.bai
wget -P ${INPUT_DIR} "${DATA_HTTP_DIR}"/test_nist.b37_chr20_100kbp_at_10mb.bed
wget -P ${INPUT_DIR} "${DATA_HTTP_DIR}"/test_nist.b37_chr20_100kbp_at_10mb.vcf.gz
wget -P ${INPUT_DIR} "${DATA_HTTP_DIR}"/test_nist.b37_chr20_100kbp_at_10mb.vcf.gz.tbi
wget -P ${INPUT_DIR} "${DATA_HTTP_DIR}"/ucsc.hg19.chr20.unittest.fasta
wget -P ${INPUT_DIR} "${DATA_HTTP_DIR}"/ucsc.hg19.chr20.unittest.fasta.fai
wget -P ${INPUT_DIR} "${DATA_HTTP_DIR}"/ucsc.hg19.chr20.unittest.fasta.gz
wget -P ${INPUT_DIR} "${DATA_HTTP_DIR}"/ucsc.hg19.chr20.unittest.fasta.gz.fai
wget -P ${INPUT_DIR} "${DATA_HTTP_DIR}"/ucsc.hg19.chr20.unittest.fasta.gz.gzi
{{< /highlight >}}

Then we would need the Singularity image. It is too large to download! Lets make a symbolic link. On Magic Castle

{{< highlight bash >}}

echo singularity pull docker://google/deepvariant:latest-gpu
echo singularity pull docker://google/deepvariant:latest

ln -s /home/shared/sing/deepvariant_latest-gpu.sif ./deepvariant_latest-gpu.sif

ln -s /home/shared/sing/deepvariant_latest.sif ./deepvariant_latest.sif

{{< /highlight >}}

Lets use VI and save the following job script:


{{< highlight bash >}}
#!/bin/bash
#SBATCH --gpus=1
#SBATCH --partition=stamps-b --reservation=ws_gpu
#SBATCH --account=def-gshamov
#SBATCH --cpus-per-task=12 --mem-per-cpu=4gb

#https://github.com/google/deepvariant/blob/r1.6/docs/deepvariant-quick-start.md

INPUT_DIR="${PWD}/quickstart-testdata"
OUTPUT_DIR="${PWD}/quickstart-output"
mkdir -p "${OUTPUT_DIR}"

# Pull the image.

# dont!
#singularity pull docker://google/deepvariant:"${BIN_VERSION}"

module load singularity


# Run DeepVariant.
singularity run --nv -B /usr/lib/locale/:/usr/lib/locale/ \
  deepvariant_latest-gpu.sif \
  /opt/deepvariant/bin/run_deepvariant \
  --model_type=WGS \
  --ref="${INPUT_DIR}"/ucsc.hg19.chr20.unittest.fasta \
  --reads="${INPUT_DIR}"/NA12878_S1.chr20.10_10p1mb.bam \
  --regions "chr20:10,000,000-10,010,000" \
  --output_vcf="${OUTPUT_DIR}"/output.vcf.gz \
  --output_gvcf="${OUTPUT_DIR}"/output.g.vcf.gz \
  --intermediate_results_dir "${OUTPUT_DIR}/intermediate_results_dir" \
  --num_shards=12

# --model_type=WGS # **Replace this string with exactly one of the following [WGS,WES,PACBIO,ONT_R104,HYBRID_PACBIO_ILLUMINA]**
# --num_shards=12  #  **How many cores the `make_examples` step uses. Change it to the number of CPU cores you have.**
   
{{< /highlight >}}

Then submit the script with __sbatch__ command. Check if the \$OUTPUT_DIR has the expected "variants".

The tutorial on Github suggests to run another container to sanity check the results. No instructions for Singularity, but we can convert the Docker instruction.

{{< highlight bash >}}

singularity pull docker://jmcdani20/hap.py:v0.3.12

INPUT_DIR="${PWD}/quickstart-testdata"
OUTPUT_DIR="${PWD}/quickstart-output"

singularity exec  -B /usr/lib/locale/:/usr/lib/locale/  -B "${INPUT_DIR}":"/input"   -B "${OUTPUT_DIR}:/output" hap.py_v0.3.12.sif  /opt/hap.py/bin/hap.py   /input/test_nist.b37_chr20_100kbp_at_10mb.vcf.gz   /output/output.vcf.gz   -f "/input/test_nist.b37_chr20_100kbp_at_10mb.bed"   -r "/input/ucsc.hg19.chr20.unittest.fasta"   -o "/output/happy.output"   --engine=vcfeval   --pass-only   -l chr20:10000000-10010000 --threads=12

cat quickstart-output//happy.output.summary.csv
{{< /highlight >}}


<!-- Changes and update:
* Last revision: May 20, 2026.
-->
