# README - GPU Job Submission on CSIL
## Overview

This document provides instructions for compiling and running GPU jobs from the Computer Science Instructional Laboratory (CSIL) machines using Slurm Workload Manager and accessing the generated data.

## Requirements

CSIL account with appropriate permissions
Code compatible with the available GPU architecture

## Preparing the Environment

Before submitting jobs, load your Conda environment and verify the availability of CUDA with the following steps:

Load Conda Environment:
```
/conda/etc/profile.d/conda.sh
conda activate <your_environment>
```
Test CUDA with PyTorch:
```
python -c "import torch; print(torch.cuda.is_available())"
```
Check NVCC and Python Versions:
```
nvcc --version
which python
```
## Slurm Script Example

Below is an example .slurm script for a GPU job:

```shell
#!/bin/bash -il
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --partition=a100
#SBATCH --gpus-per-node=1
#SBATCH --mem=30G
#SBATCH --time=10
#SBATCH --export=ALL

# Activate the Conda environment
source /opt/conda/etc/profile.d/conda.sh
conda activate simenv

# Display the environment and test CUDA availability
which conda
conda env list
python -c "import torch; print(torch.cuda.is_available())" || echo "PyTorch cannot access CUDA."

# Display versions and paths
nvcc --version
which python
echo $CUDA_HOME
echo $LD_LIBRARY_PATH

# Install dependencies here
# Example: pip install torch==2.2.0 transformers==4.37.2

# Replace with your own command to execute the code
# Example: python train_model.py

# Output the NVIDIA-smi to a logfile
time nvidia-smi >& logfile
```
## Job Submission

To submit your job to the GPU queue, use the following command:
```
sbatch <your_script>.slurm
```
## Job Status Control
You can check on the status with squeue (to see only your jobs, 'squeue' will show every job on the system):
```
squeue -u <your_username>
```
You can look at details with:
```
scontrol show job JIOBID
```
To kill a job you use:
```
scancel -i JOBID
```
## Accessing Data

Once the job is completed, the output (slurm-xxx.out), including the logfile, will be available in the directory from which the job was submitted. Ensure you have appropriate read/write permissions for the output directory.

For more detailed outputs or specific file paths, customize the script to direct the outputs to your desired location.

Replace placeholders like <your_environment> and <your_script> with your actual environment name and script filename. Adjust the paths and commands to fit your specific setup and requirements.

Refer to the [How to use Slurm](https://ucsb-engr.atlassian.net/wiki/spaces/EPK/pages/1176207407/How+to+use+Slurm) as well for more explanations.
