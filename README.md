# Kubernetes Orchestration Container Documentation

Dokumentasi Lab Kubernetes Orchestration Container

## Topologi Arsitektur Kubernetes
  
  Gambar topologi arsitektur kubernetes

## Instalasi Kubernetes Cluster, 1 Master dan 1 Worker

  Eksekusi perintah pada node master dan worker
  
  - Memperbarui paket
  
  ```console
  sudo apt update 
  
  sudo apt upgrade -y 
  
  sudo apt autoremove -y
  ```
  
  - Instal paket docker
  
  ```
  sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software properties-common -y
  
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
  
  sudo apt-key fingerprint 0EBFCD88
  
  sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
  
  sudo apt-get update -y
  
  sudo apt-get install docker-ce docker-ce-cli containerd.io -y
  ```
  
  - Instal kubectl, kubelet dan kubeadm

  ```
  sudo apt install -y apt-transport-https; curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
  ```

  - Menambahkan repository kubectl, kubelet dan kubeadm
  
  ```
  echo 'deb http://apt.kubernetes.io/ kubernetes-xenial main' > kubernetes.list

  sudo mv kubernetes.list /etc/apt/sources.list.d/kubernetes.list
  ```
  - Memperbarui paket dan menginstal kubectl, kubelet dan kubeadm
  
  ```
  sudo apt update; sudo apt install -y kubectl kubelet kubeadm
  
  apt-mark hold kubelet kubeadm kubectl
  ```
  
  ### Inisialisasi Kubernetes (eksekusi perintah pada node master)
  
  - Inisialisasi kubeadm
  
  ```
  swapon -s
  
  sudo swapoff -a
  
  sudo kubeadm init --pod-network-cidr=10.244.XX.0/16
  ```
  
  - Menyalin konfigurasi admin dari direktori kubernetes
  
  ```
  mkdir -p $HOME/.kube
  
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
  ```
  
  - Memverifikasi config dan cluster
  
  ```
  kubectl config view
  
  kubectl cluster-info
  ```
  
  ### Membuat Node Worker bergabung ke dalam Cluster
  
  - Menampilkan token dan token-ca-cert-hash (eksekusi perintah di node master)
  
  `sudo kubeadm token list`
  
  `sudo openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'`
  
  - Memasukkan node worker ke dalam cluster (eksekusi perintah di node worker)
  
  ```
  swapon -s
  
  sudo swapoff -a
  
  sudo kubeadm join --token [TOKEN] [NODE-MASTER]:6443 --discovery-token-ca-cert-hash sha256:[TOKEN-CA-CERT-HASH]
  ```
  
  - Memverifikasi jika node worker sudah bergabung ke dalam cluster kubernetes (eksekusi perintah di node master)
  
  ```
  kubectl get nodes
  ```
  
## Instalasi dan konfigurasi Ingress Controller dengan Nginx

## Membuat Dynamic Storage Class dengan NFS

## Deploy Aplikasi Wordpress + DB (Mengunakan PVC)

## Instalasi dan Konfigurasi MetalLB untuk Load Balancer

## Deploy Aplikasi Nginx dengan ekspose akses Load Balancer

