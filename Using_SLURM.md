# SLURM Job Submission Guide

## Overview

SLURM (Simple Linux Utility for Resource Management) is used to manage and schedule jobs on the server cluster.

## Server-Specific Information

### Where to Submit Jobs

**All SLURM commands and job submissions must be done from the master node** (`10.24.6.249`).

1. First, SSH into the master node:
   ```bash
   ssh <username>@10.24.6.249
   ```

2. From the master node, submit your SLURM jobs

The SLURM scheduler will automatically allocate your job to the appropriate slave nodes (`qcommthub1` or `qcommthub2`) based on availability and your resource requirements.

### Requesting Specific GPU Nodes

To request a job on a specific slave node, use the `--nodelist` option:

**Submit to qcommthub1:**
```bash
sbatch --nodelist=qcommthub1 job_script.sh
```

**Submit to qcommthub2:**
```bash
sbatch --nodelist=qcommthub2 job_script.sh
```

**Interactive session on specific node:**
```bash
srun --nodelist=qcommthub1 --pty --gres=gpu:1 bash
```

### In Your Job Script

Add the nodelist directive to your SLURM script:

```bash
#!/bin/bash

#SBATCH --job-name=my_job
#SBATCH --nodelist=qcommthub1        # Request specific node
#SBATCH --gres=gpu:1
#SBATCH --mem=16G
#SBATCH --cpus-per-task=4

# Your commands here
python my_script.py
```

**Note:** If you don't specify a node, SLURM will automatically assign your job to whichever GPU node (qcommthub1 or qcommthub2) is available.

## Basic SLURM Commands

### Check Cluster Status

View all nodes and their status:

```bash
sinfo
```

View detailed node information:

```bash
sinfo -N -l
```

### Check Job Queue

View all jobs in the queue:

```bash
squeue
```

View only your jobs:

```bash
squeue -u <username>
```

View detailed job information:

```bash
scontrol show job <job_id>
```

## Submitting Jobs

### Interactive Job

Request an interactive session on a GPU node:

```bash
srun --pty --gres=gpu:1 bash
```

Request specific resources:

```bash
srun --pty --gres=gpu:1 --mem=16G --cpus-per-task=4 bash
```

### Batch Job Submission

Submit a batch job script:

```bash
sbatch job_script.sh
```

## Creating a SLURM Job Script

### Basic Job Script Template

Create a file named `job_script.sh`:

```bash
#!/bin/bash

#SBATCH --job-name=my_job           # Job name
#SBATCH --output=output_%j.log      # Output file (%j = job ID)
#SBATCH --error=error_%j.log        # Error file
#SBATCH --ntasks=1                  # Number of tasks
#SBATCH --cpus-per-task=4           # CPU cores per task
#SBATCH --mem=16G                   # Memory per node
#SBATCH --time=24:00:00             # Time limit (HH:MM:SS)
#SBATCH --gres=gpu:1                # Number of GPUs
#SBATCH --partition=gpu             # Partition name (if applicable)

# Load modules or activate environment
source ~/anaconda3/bin/activate myenv

# Run your program
python my_script.py
```

### GPU-Specific Job Script

```bash
#!/bin/bash

#SBATCH --job-name=gpu_training
#SBATCH --output=logs/output_%j.log
#SBATCH --error=logs/error_%j.log
#SBATCH --gres=gpu:1
#SBATCH --mem=32G
#SBATCH --cpus-per-task=8
#SBATCH --time=48:00:00

# Print job information
echo "Job ID: $SLURM_JOB_ID"
echo "Node: $SLURM_NODELIST"
echo "GPU: $CUDA_VISIBLE_DEVICES"

# Activate environment
source ~/anaconda3/bin/activate pytorch_env

# Run training script
python train_model.py --epochs 100 --batch-size 32
```

## Job Management

### Cancel a Job

```bash
scancel <job_id>
```

Cancel all your jobs:

```bash
scancel -u <username>
```

### Hold and Release Jobs

Put a job on hold:

```bash
scontrol hold <job_id>
```

Release a held job:

```bash
scontrol release <job_id>
```

## Monitoring Jobs

### Check Job Status

```bash
squeue -j <job_id>
```

### View Job Details

```bash
scontrol show job <job_id>
```

### Check Resource Usage

View accounting information for completed jobs:

```bash
sacct -j <job_id> --format=JobID,JobName,MaxRSS,Elapsed,State
```

View detailed statistics:

```bash
sstat -j <job_id> --format=AveCPU,AveRSS,MaxRSS,AveVMSize
```

## Useful SLURM Variables

These environment variables are available in your job scripts:

- `$SLURM_JOB_ID` - Job ID
- `$SLURM_JOB_NAME` - Job name
- `$SLURM_SUBMIT_DIR` - Directory from which job was submitted
- `$SLURM_NODELIST` - List of nodes allocated to the job
- `$SLURM_CPUS_PER_TASK` - Number of CPUs per task
- `$CUDA_VISIBLE_DEVICES` - GPU devices assigned to the job

## Common SBATCH Options

| Option | Description |
|--------|-------------|
| `--job-name` | Name for the job |
| `--output` | Standard output file |
| `--error` | Standard error file |
| `--ntasks` | Number of tasks |
| `--cpus-per-task` | CPU cores per task |
| `--mem` | Memory per node |
| `--mem-per-cpu` | Memory per CPU |
| `--time` | Time limit (D-HH:MM:SS) |
| `--gres=gpu:N` | Request N GPUs |
| `--partition` | Partition to submit to |
| `--nodelist` | Specific nodes to use (e.g., qcommthub1) |
| `--exclude` | Nodes to exclude |
| `--mail-type` | Email notifications (BEGIN,END,FAIL,ALL) |
| `--mail-user` | Email address for notifications |

## Tips and Best Practices

1. **Always specify resource requirements** - Request only what you need to allow better scheduling
2. **Use job arrays** for multiple similar jobs - More efficient than submitting individually
3. **Check node availability** before submitting with `sinfo`
4. **Monitor your jobs** regularly with `squeue -u <username>`
5. **Use output logs** to debug issues - Check error_%j.log files
6. **Test interactively first** using `srun` before submitting batch jobs
7. **Create a logs directory** to keep output files organized

## Example: Job Array

Submit multiple jobs with different parameters:

```bash
#!/bin/bash

#SBATCH --job-name=param_sweep
#SBATCH --output=logs/job_%A_%a.log
#SBATCH --array=0-9
#SBATCH --gres=gpu:1
#SBATCH --mem=16G

# Use array task ID as parameter
python train.py --learning-rate 0.00$SLURM_ARRAY_TASK_ID
```

This creates 10 jobs with learning rates from 0.000 to 0.009.
