# TP Stack

## Key Directories

Your admin will tell you the exact project directory name (e.g. `hpc-lab-b4d7ff24`).

| Path | Purpose | Backed up? |
| --- | --- | --- |
| `~/` | Your personal home — configs, scripts, small files | Yes |
| `/projects/YOUR_PROJECT/` | Shared project storage — code, venvs, datasets | Yes |
| `/scratch/YOUR_PROJECT/` | Temporary large files — intermediate results | No |

!!! warning
    `/scratch` is NOT backed up and may be cleaned periodically. Download important results to your own machine.

## Monitoring

View cluster health and GPU status:

URL: [https://grafana-monitoring.tp-stack.co.uk](https://grafana-monitoring.tp-stack.co.uk)

Log in with the credentials your admin provides.

| Dashboard | Shows |
| --- | --- |
| Node Exporter Full | CPU, RAM, disk, network per host |
| NVIDIA DCGM | GPU temperature, utilisation, memory |

Useful to check before submitting GPU jobs — is the GPU already in use? How much RAM is free?

## Rules of the Road

1. **Don't run computation on the login node.** The login node is for editing, submitting, and checking results. Heavy work goes through `sbatch` or `srun`.

2. **Clean up `/scratch`.** It's shared and not backed up. Delete files you no longer need.

3. **Be a good neighbour.** Only request the CPUs, memory, and GPU time your job actually needs.

4. **Don't share your SSH key.** Your private key (`~/.ssh/tpstack`) is yours alone. Never email it or put it on a shared drive.

5. **Report problems.** Something broken? Tell the admin. Don't try to fix infrastructure yourself.

6. **Back up important results.** Download final results to your own machine. The cluster is not permanent archival storage.
