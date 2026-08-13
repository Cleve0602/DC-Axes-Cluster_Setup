# Rocky Linux HPC Cluster
A basic High-Performance Computing (HPC) cluster built using Rocky Linux, VirtualBox and a real life cluster.

This project documents the initial setup of a small HPC cluster consisting of one head node and one compute node. The cluster uses a private network for node-to-node communication and NFS to share the `/home` directory between nodes.

## Cluster Architecture
<img width="1406" height="767" alt="cluster" src="https://github.com/user-attachments/assets/3e31fc79-521a-4943-a93c-36f7fc185398" />

## Cluster Nodes

| Node         | Hostname | IP Address     | Role                    |
| ------------ | -------- | -------------- | ----------------------- |
| Head Node    | `head`   | `192.168.15.1` | Management / NFS Server / Slurm |
| Compute Node | `com1`   | `192.168.15.2` | Benchmark and Tasks     |
| Compute Node | `com2`   | `192.168.15.5` | Benchmark and Tasks     |

The cluster uses the 192.168.15.0/24 private network for communication between the nodes.

## Technologies
- Rocky Linux
- VirtualBox
- OpenSSH
- NFS
- Chrony
- nftables
- Ansible
- Lmod
- OpenMPI

Lmod and OpenMPI are part of the later cluster configuration. This repository currently focuses on the initial setup through NFS.
