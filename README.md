
# Project Title

A brief description of what this project does and who it's for

# Kubernetes Project

This repository contains the configuration files, scripts, and documentation for deploying and managing applications using Kubernetes.

## Table of Contents

- [Introduction](#introduction)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [Contributing](#contributing)
- [License](#license)

## Introduction

This project demonstrates how to use Kubernetes for managing containerized applications. It includes examples of deployments, services, config maps, and more.

## Features

- Deployment of a multi-container application.
- Service configuration for internal and external communication.
- ConfigMaps and Secrets management.
- Autoscaling and load balancing.
- Monitoring and logging setup.

## Prerequisites

Before you begin, ensure you have the following installed:

- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) - Kubernetes command-line tool.
- [Docker](https://www.docker.com/) for building container images.

## Install and Set Up kubectl on Linux

1. Master Node
   ```bash
   sudo su
   apt-get update
   apt-get install apt-transport-https
   apt install docker.io -y
   docker --version
   systemctl start docker
   systemctl enable docker

   apt-get install -y apt-transport-https ca-certificates curl gnupg

   curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | gpg --dearmor -o /etc/kubernetes-apt-keyring.gpg

   echo 'deb [signed-by=/etc/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

   apt-get install -y kubelet kubeadm kubectl kubernetes-cni

   sudo kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.0/manifests/calico.yaml

   kubeadm init --v=9
    kubeadm token create --print-join-command
   ```
2. Worker node1
   ```  
   apt-get update
   apt-get install apt-transport-https
   apt install docker.io -y
   docker --version
   systemctl start docker
   systemctl enable docker

   apt-get install -y apt-transport-https ca-certificates curl gnupg

   curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key |  gpg --dearmor -o /etc/kubernetes-apt-keyring.gpg

   echo 'deb [signed-by=/etc/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' |  tee /etc/apt/sources.list.d/kubernetes.list

   apt-get install -y kubelet kubeadm kubectl kubernetes-cni
   
   kubeadm --v=9 join ip:port  --token #####\--discovery-token-ca-cert-hash sha256:##### (copy from master node after run   kubeadm --v=9 join ip:port  --token #####\--discovery-token-ca-cert-hash sha256:##### )
   ```
3. Worker node2   
```
   apt-get update
   apt-get install apt-transport-https
   apt install docker.io -y
   docker --version
   systemctl start docker
   systemctl enable docker

   apt-get install -y apt-transport-https ca-certificates curl gnupg

   curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key |  gpg --dearmor -o /etc/kubernetes-apt-keyring.gpg

   echo 'deb [signed-by=/etc/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' |  tee /etc/apt/sources.list.d/kubernetes.list

   apt-get install -y kubelet kubeadm kubectl kubernetes-cni
   
   kubeadm --v=9 join ip:port  --token #####\--discovery-token-ca-cert-hash sha256:##### (copy from master node after run   kubeadm --v=9 join ip:port  --token #####\--discovery-token-ca-cert-hash sha256:##### )
```

# Command on master
## Nodes
- List all nodes master and worker nodes
```bash
sudo kubectl get node 
kubectl get nodes -o wide
```

## Services 
- List all services port
```
sudo kubectl get services

```

## Pods
- list
```
kubectl get pods --all-namespaces -o wide 
```
## Test k8 
nginx web server run on master and access from woker node 
```
 kubectl create deployment nginx --image=nginx --replicas=3
 kubectl expose deployment nginx --type=NodePort --name=nginx-service-replica --port=80
```

## Reset
```
sudo kubeadm --v=9 reset --force

sudo mv /etc/containerd/config.toml /tmp/
sudo systemctl restart containerd.service
```

#Issue Resolve

This will be active status
```
sudo  systemctl status kubelet
```

Check log 
```
sudo journalctl -u kubelet -f
```
if occur swap Issue
```
sudo nano /var/lib/kubelet/config.yaml
add bottom this file :failSwapOn: false
sudo systemctl restart kubelet
```
if occur port timeout issue issue 
```
telnet master-ip:port  from worker node (come form master kubeadm token create --print-join-command)
sudo ufw allow port (from master node)
```
