# Kubernetes Orchestration Container Documentation

Dokumentasi Lab Kubernetes Orchestration Container

## Topologi Arsitektur Kubernetes
  
  gambar topologi arsitektur kubernetes

## Instalasi Kubernetes Cluster, 1 Master dan 1 Worker

  eksekusi perintah pada node master dan worker
  
  - memperbarui paket
  
  `sudo apt update` `sudo apt upgrade -y` `sudo apt autoremove -y`
  
  - instal paket docker
  
  `sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software properties-common -y`
  
  `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
  
  `sudo apt-key fingerprint 0EBFCD88`
  
  `sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"`
  
  `sudo apt-get update -y`
  
  `sudo apt-get install docker-ce docker-ce-cli containerd.io -y`
 
  - instal kubectl, kubelet & kubeadm

  `sudo apt install -y apt-transport-https; curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -`

  `echo 'deb http://apt.kubernetes.io/ kubernetes-xenial main' > kubernetes.list`

  `sudo mv kubernetes.list /etc/apt/sources.list.d/kubernetes.list`
  
  `sudo apt update; sudo apt install -y kubectl kubelet kubeadm`
  
  `apt-mark hold kubelet kubeadm kubectl`
  
  
  
## Instalasi dan konfigurasi Ingress Controller dengan Nginx

## Membuat Dynamic Storage Class dengan NFS

## Deploy Aplikasi Wordpress + DB (Mengunakan PVC)

## Instalasi dan Konfigurasi MetalLB untuk Load Balancer

## Deploy Aplikasi Nginx dengan ekspose akses Load Balancer

