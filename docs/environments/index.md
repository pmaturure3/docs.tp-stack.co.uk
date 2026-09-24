# Environments

## Python Environments

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

!!! tip
    Don't install packages in your home directory — use `/projects/` so your team can share them.
