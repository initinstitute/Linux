Class 42 – Kubernetes Installation on Master and Worker Nodes
class url: https://youtu.be/d6mEYTof67g
1. Introduction

In this class, we will install Kubernetes on:

Master Node (Control Plane)
Worker Nodes

We will use Ubuntu Linux machines.

Kubernetes Cluster
                Kubernetes Cluster
                       |
          +------------+------------+
          |                         |
     Master Node              Worker Nodes
   (Control Plane)          +----------------+
                            | Worker Node 1  |
                            +----------------+
                            | Worker Node 2  |
                            +----------------+
2. Prerequisites

Create EC2 instances or virtual machines.

Example:

Node	Role	Example IP
Master	Control Plane	172.31.12.204
Worker 1	Worker	172.31.9.184
Worker 2	Worker	172.31.x.x

All machines should:

Use Ubuntu
Have network connectivity with each other
Have hostname configured
Have required Kubernetes ports allowed

installation commands
master node:
curl -s https://raw.githubusercontent.com/AdidelaHarishReddy/installations/refs/heads/main/k8s_master_worker_new_1 | bash -s master
worker node:
curl -s https://raw.githubusercontent.com/AdidelaHarishReddy/installations/refs/heads/main/k8s_master_worker_new_1 | bash -s worker
master worker joining:
generate the token by using below command in master
" kubeadm token create --print-join-command "
generated token command in worker after switch to root user



