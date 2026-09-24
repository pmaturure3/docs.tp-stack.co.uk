# Welcome

Welcome to the TP-Stack HPC cluster documentation. This site covers everything you need to get started — from accepting your invitation to running jobs on the cluster.

<div class="grid cards" markdown>

- :material-server: **TP Stack**

    ---

    Key directories, monitoring, and rules of the road.

    [→ TP Stack](tp-stack/index.md)

- :material-connection: **Connecting**

    ---

    Accept your invitation, register, set up SSH, and transfer files.

    [→ Connecting](connecting/index.md)

- :material-play: **Running Jobs**

    ---

    Submit CPU and GPU jobs with Slurm.

    [→ Running Jobs](running-jobs/index.md)

- :material-language-python: **Environments**

    ---

    Set up shared Python environments for your project.

    [→ Environments](environments/index.md)

- :material-wrench: **Troubleshooting**

    ---

    Common problems and how to fix them.

    [→ Troubleshooting](troubleshooting.md)

</div>

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