# TP-Stack HPC — User Guide

Welcome to the TP-Stack HPC cluster. This guide takes you from your invitation email to running jobs on the cluster. Follow the steps in order.

## Step 1: Accept Your Invitation

You received an email from `tpstackwaldur@gmail.com` with the subject **"TP-Stack Lab Invitation"**.

1. Open the email.
2. Click the invitation link.
3. You land on the TP-Stack portal.
4. Click **"Accept invitation"**.
5. Click **"TP-Stack SSO"** to log in.

If you already have an account, log in. If you're new, click **"Register"** (see Step 2).

> **Note:** The invitation link expires after 7 days. Check your spam/promotions folder if you don't see the email.

## Step 2: Register Your Account

On the Keycloak login page, click **"Register"** (link below the login form).

Fill in the following:

| Field | What to enter |
| --- | --- |
| Username | Choose a short username (e.g. `jsmith`). This becomes your SSH login — keep it lowercase, no spaces, no special characters. |
| Email | Your real email (must match the invitation) |
| First name | Your first name |
| Last name | Your last name |
| Password | Choose a strong password |

Click **Register**. You'll be redirected to the TP-Stack portal and should see your project.

> **Important:** Remember your username and password — you'll need them for SSH access later.

## Step 3: Explore the Portal

After logging in you'll see your organisation and project. You can view project members, resource allocations, and cluster status.

- **Portal:** https://waldur-portal.tp-stack.co.uk
- **Monitoring:** https://grafana-monitoring.tp-stack.co.uk

To log in again later, click **"TP-Stack SSO"** and enter your username and password.

## Step 4: Set Up SSH Access

Your admin will confirm SSH access within a day of your registration. You need to do three things (all one-time setup).

### A. Install cloudflared

macOS:

```bash
brew install cloudflared
```

Ubuntu/Debian:

```bash
curl -L -o cloudflared.deb \
  https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared.deb
```

Windows: Download the `.msi` from the [same releases page](https://github.com/cloudflare/cloudflared/releases/latest).

Verify:

```bash
cloudflared --version
```

### B. Generate your SSH key

```bash
ssh-keygen -t ed25519 -f ~/.ssh/tpstack -C "your-email@example.com"
```

Press Enter twice (empty passphrase is fine).

This creates two files:

| File | What it is |
| --- | --- |
| `~/.ssh/tpstack` | **PRIVATE key** — never share this! |
| `~/.ssh/tpstack.pub` | **PUBLIC key** — send this to your admin |

Send your public key to the admin:

```bash
cat ~/.ssh/tpstack.pub
```

Copy the output and send it via email or chat.

### C. Configure SSH

Copy-paste this into your terminal:

```bash
mkdir -p ~/.ssh && cat >> ~/.ssh/config << 'EOF'

Host hpc.tp-stack.co.uk
    ProxyCommand cloudflared access ssh --hostname %h
    IdentityFile ~/.ssh/tpstack
EOF
chmod 600 ~/.ssh/config
```

Done. You'll never need to do this again.

## Step 5: Connect to the Cluster

Once the admin confirms your SSH key is deployed:

```bash
ssh YOUR_USERNAME@hpc.tp-stack.co.uk
```

First time per 24 hours:

1. Your browser opens automatically.
2. Enter your email address.
3. Check your email for a 6-digit code.
4. Enter the code.
5. SSH connects.

After that, for the next 24 hours, `ssh YOUR_USERNAME@hpc.tp-stack.co.uk` just works — no browser, no codes.

You should see:

```text
[jsmith@slurm-login ~]$
```

Verify everything works:

```bash
whoami        # your username
id            # your UID and groups
pwd           # your home directory
sinfo         # cluster partitions and nodes
```

## Step 6: Transfer Files

### Upload to the cluster

```bash
scp myfile.csv YOUR_USERNAME@hpc.tp-stack.co.uk:~/
scp -r my-project/ YOUR_USERNAME@hpc.tp-stack.co.uk:~/
```

### Download from the cluster

```bash
scp YOUR_USERNAME@hpc.tp-stack.co.uk:~/results/output.csv ./
scp YOUR_USERNAME@hpc.tp-stack.co.uk:~/results/*.png ./
```

### Upload to shared project space

```bash
scp dataset.csv YOUR_USERNAME@hpc.tp-stack.co.uk:/projects/YOUR_PROJECT/
```

All transfers go through the encrypted tunnel automatically.

## Step 7: Submit Your First Job

The cluster uses Slurm to manage jobs. You write a script, submit it, and Slurm runs it on a compute node when resources are available.

### Quick test — CPU job

```bash
sbatch --partition=cpu \
  --wrap="echo Hello from $(hostname)" \
  --output=hello-%j.out

squeue --me          # check status
cat hello-*.out      # read output
```

### Quick test — GPU job (Tesla T4)

```bash
sbatch --partition=gpu --gres=gpu:1 \
  --wrap="nvidia-smi -L" \
  --output=gpu-%j.out

squeue --me
cat gpu-*.out
```

### Writing a job script

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

### GPU job script

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

## Step 8: Python Environments

Create a shared Python environment in your project space (available on all nodes):

```bash
cd /projects/YOUR_PROJECT
python3 -m venv venv
source venv/bin/activate
pip install numpy pandas scikit-learn matplotlib
```

For GPU work:

```bash
pip install torch torchvision
```

The venv lives on NFS — install once, use everywhere (login node, CPU node, GPU node).

> **Tip:** Don't install packages in your home directory — use `/projects/` so your team can share them.

## Key Directories

Your admin will tell you the exact project directory name (e.g. `hpc-lab-b4d7ff24`).

| Path | Purpose | Backed up? |
| --- | --- | --- |
| `~/` | Your personal home — configs, scripts, small files | Yes |
| `/projects/YOUR_PROJECT/` | Shared project storage — code, venvs, datasets | Yes |
| `/scratch/YOUR_PROJECT/` | Temporary large files — intermediate results | No |

> **Warning:** `/scratch` is NOT backed up and may be cleaned periodically. Download important results to your own machine.

## Monitoring

View cluster health and GPU status:

URL: https://grafana-monitoring.tp-stack.co.uk

Log in with the credentials your admin provides.

| Dashboard | Shows |
| --- | --- |
| Node Exporter Full | CPU, RAM, disk, network per host |
| NVIDIA DCGM | GPU temperature, utilisation, memory |

Useful to check before submitting GPU jobs — is the GPU already in use? How much RAM is free?

## Troubleshooting

| Problem | Fix |
| --- | --- |
| `cloudflared: command not found` | Install cloudflared (see Step 4A) |
| Browser says "Forbidden" | Your email isn't on the access list — contact admin |
| `Permission denied (publickey)` | SSH key not deployed — send your `.pub` to admin |
| `websocket: bad handshake` | Run `cloudflared access login hpc.tp-stack.co.uk`, then retry `ssh YOUR_USERNAME@hpc.tp-stack.co.uk` |
| Can't see `/projects/` | Group membership not synced yet — run `id`, contact admin if missing |
| Job stuck in `PENDING` | Run `squeue --me`, check `REASON` — usually waiting for resources |
| "Module not found" in job | Activate your venv in the script: `source /projects/.../venv/bin/activate` |
| Portal won't load | Try incognito window or clear browser cache |

## Rules of the Road

1. **Don't run computation on the login node.** The login node is for editing, submitting, and checking results. Heavy work goes through `sbatch` or `srun`.

2. **Clean up `/scratch`.** It's shared and not backed up. Delete files you no longer need.

3. **Be a good neighbour.** Only request the CPUs, memory, and GPU time your job actually needs.

4. **Don't share your SSH key.** Your private key (`~/.ssh/tpstack`) is yours alone. Never email it or put it on a shared drive.

5. **Report problems.** Something broken? Tell the admin. Don't try to fix infrastructure yourself.

6. **Back up important results.** Download final results to your own machine. The cluster is not permanent archival storage.

## Quick Reference Card

### Access

```text
Connect:         ssh YOUR_USERNAME@hpc.tp-stack.co.uk
Portal:          https://waldur-portal.tp-stack.co.uk
Monitoring:      https://grafana-monitoring.tp-stack.co.uk
```

### Files

```text
Upload:          scp file.csv YOUR_USERNAME@hpc.tp-stack.co.uk:~/
Download:        scp YOUR_USERNAME@hpc.tp-stack.co.uk:~/result.csv ./
Upload folder:   scp -r project/ YOUR_USERNAME@hpc.tp-stack.co.uk:~/
```

### Directories

```text
Home:            ~/
Project:         /projects/YOUR_PROJECT/
Scratch:         /scratch/YOUR_PROJECT/
```

### Jobs

```text
Submit:          sbatch my-script.sh
Status:          squeue --me
History:         sacct --me
Cancel:          scancel JOB_ID
Nodes:           sinfo
```

### Python

```text
Create venv:     python3 -m venv /projects/X/venv
Activate:        source /projects/X/venv/bin/activate
Install:         pip install numpy pandas torch
```

### Help

```text
Your groups:     id
Job details:     scontrol show job JOB_ID
Admin:           contact your project admin
```