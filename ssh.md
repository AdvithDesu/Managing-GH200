# SSH Connection Guide

## Server Architecture

- **Master Node** (`qsim`): `10.24.6.249`
- **Slave Nodes (GPUs)**:
  - `qcommthub1`
  - `qcommthub2`

### Connect to Master Node

```bash
ssh <username>@10.24.6.249
```

### Connect to Slave Nodes

From the master node, connect to either GPU node:

```bash
ssh qcommthub1
ssh qcommthub2
```

## Port Forwarding for Jupyter Notebook

When you need to run Jupyter Notebook on the slave nodes and access it from your local machine, use SSH port forwarding in two steps:

### Step 1: Forward from Local to Master Node

```bash
ssh -L 8890:127.0.0.1:8890 <username>@10.24.6.249
```

### Step 2: Forward from Master to Slave Node

Once connected to the master node, run either:

```bash
ssh -L 8890:127.0.0.1:8890 qcommthub1
ssh -L 8890:127.0.0.1:8890 qcommthub2
```

### Step 3: Open Jupyter Notebook

After setting up the port forwarding chain, access Jupyter using:

```bash
python -m jupyter notebook --no-browser --port 8890
```

## Notes

- Replace `<username>` with your actual username
- Port `8890` is used to avoid conflicts with default Jupyter port `8888`
- The double port forwarding allows you to tunnel through the master node to reach the slave nodes
