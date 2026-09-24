# Running Jobs

The cluster uses Slurm to manage jobs. You write a script, submit it, and Slurm runs it on a compute node when resources are available.

## Quick test — CPU job

```bash
sbatch --partition=cpu \
  --wrap="echo Hello from $(hostname)" \
  --output=hello-%j.out

squeue --me          # check status
cat hello-*.out      # read output
```

## Quick test — GPU job (Tesla T4)

```bash
sbatch --partition=gpu --gres=gpu:1 \
  --wrap="nvidia-smi -L" \
  --output=gpu-%j.out

squeue --me
cat gpu-*.out
```

## Writing a job script

Create a file called `my-job.sh`:

```bash
#!/bin/bash
#SBATCH --job-name=my-experiment
#SBATCH --partition=cpu
#SBATCH --cpus-per-task=4
#SBATCH --mem=4G
#SBATCH --time=01:00:00
#SBATCH --output=logs/%x-%j.out
#SBATCH --error=logs/%x-%j.err

mkdir -p logs
source /projects/YOUR_PROJECT/venv/bin/activate
python train.py --epochs 50
```

Submit: `sbatch my-job.sh`

Cancel: `scancel JOB_ID`

## GPU job script

```bash
#!/bin/bash
#SBATCH --job-name=gpu-train
#SBATCH --partition=gpu
#SBATCH --gres=gpu:1
#SBATCH --cpus-per-task=4
#SBATCH --mem=16G
#SBATCH --time=04:00:00
#SBATCH --output=logs/%x-%j.out
#SBATCH --error=logs/%x-%j.err

mkdir -p logs
source /projects/YOUR_PROJECT/venv/bin/activate
python train.py --device cuda
```
