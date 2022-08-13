# Kubernetes Orchestration Documentation

Dokumentasi Lab Kubernetes Orchestration Container oleh Rizqi Arif Wibowo - 11 Agustus 2022

## Topologi Arsitektur Kubernetes
  
  Gambar topologi arsitektur kubernetes

## Instalasi Minikube menggunakan driver kvm2

  - Menginstal KVM, Libvirt dan Qemu
  
  ```console
  sudo apt update
  sudo apt install -y qemu-kvm libvirt-dev bridge-utils libvirt-daemon-system libvirt-daemon virtinst bridge-utils libosinfo-bin libguestfs-tools virt-top
  ```
  - Memuat dan mengaktifkan `modulevhost-net`
  
  ```console
  sudo modprobe vhost_net
  sudo lsmod | grep vhost
  echo "vhost_net" | sudo tee -a /etc/modules
  ```
  
  - Menginstal paket untuk mengizinkan `apt` agar dapat menggunakan repositori dari `HTTPS`
  
  ```console
  sudo apt install -y lsb-release gnupg apt-transport-https ca-certificates curl software-properties-common
  ```
  
  - Menambahkan Docker official GPG key
  
  ```console
  curl -fSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/docker.gpg
  ```
  
  - Menambahkan stable repository
   
  ```console
  sudo add-apt-repository "deb [arch=$(dpkg --print-architecture)] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
  ```
  
  - Menginstal docker ce
  
  ```console
  sudo apt update
  sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
  ```
  
  - Memasukan user ke dalam group `docker` agar dapat menggunakan `docker` sebagai non-root
  
  ```console
  sudo usermod -aG docker $USER
  newgrp docker
  ```
  
  - Melihat versi `docker`
  
  ```console
  docker version
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184464784-b6ce3408-cd4c-41c5-a53c-67cadda2243d.png)
  
  - Melihat versi `docker compose`
  
  ```console
  docker compose version
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184465567-910bf877-6e13-4688-865e-7b0561e699d9.png)
  
  -  Mengunduh binary `minikube` dan memindahkan nya ke direktori `/usr/local/bin`
  
  ```console
  wget https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
  chmod +x minikube-linux-amd64
  sudo mv minikube-linux-amd64 /usr/local/bin/minikube
  ```
  
  - Mengecek versi `minikube`

  ```console
  minikube version
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184465776-c4281cee-7773-4a6d-b1fe-33c60d3058a3.png)

  - Menginstal `kubectl` untuk mendeploy dan me *manage* aplikasi kubernetes
  
  ```console
  curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
  ```
  
  - Mengubah file `kubectl` dan memindahkan nya ke direktori /usr/local/bin

  ```console
  chmod +x kubectl
  mv kubectl /usr/local/bin
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184470860-1b24bc96-0afc-4b0e-bbeb-4045297b2ed2.png)

  - Memeriksa versi `kubectl`

  ```console
  kubectl version -o json
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184470872-7f998ca3-6be5-4bea-aef1-7eeae35107fd.png)

  - Menginstal Docker machine driver kvm

  ```console
  curl -LO https://storage.googleapis.com/minikube/releases/latest/docker-machine-driver-kvm2
  ```
  
  -Mengubah file 'docker-machine-driver-kvm2' dan memindahkannya ke direktori /usr/local/bin
  
  ```console
  chmod +x docker-machine-driver-kvm2
  sudo mv docker-machine-driver-kvm2 /usr/local/bin
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184470942-c56551d1-910e-451d-ac54-9b780ba6a0ba.png)

  - Melihat versi `docker-machine-driver-kvm2`

  ```console
  docker-machine-driver-kvm2 version
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184470958-89b6a4f1-3612-42be-97c9-4eee48aaab04.png)

  - Menambahkan user ke dalam grup `libvirt`

  ```console
  sudo usermod -aG libvirt $USER
  newgrp libvirt
  ```
  
  - Menjadikan kvm sebagai default driver `minikube`

  ```console
  minikube config set vm-driver kvm2
  ```
  
  - Menjalankan `minikube`

  ```console
  minikube start
  ```
  
  - Melihat VM yang berjalan dengan perintah `virsh`

  ```console
  sudo virsh list
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184470990-060c256e-9121-46bf-82c3-a95f8a14fb00.png)

  - Mengecek status cluster

  ```console
  kubectl cluster-info
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184471015-2524e199-6701-44b3-b931-a259616adcd2.png)

  - Melihat konfigurasi `minikube` yang berada di direktori ~/.minikube/machines/minikube/config.json

  ```console
  kubectl config view
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184471026-2bfc6230-436a-4caa-9915-eac88249c7d9.png)

  - Mengecek node

  ```console
  kubectl get nodes
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184471038-5db95e51-b8e5-453a-a8df-7ef55d9b0abf.png)

  - Mengakses `minikube` menggunakan ssh dan mengecek versi os yang digunakan

  ```console
  minikube ssh
  cat /etc/os-release
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184471054-7200b337-2b41-4a9d-bd1a-08a5cbcf6f90.png)

  - Melihat daftar addons pada `minikube`

  ```console
  minikube addons list
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184471068-35f5f652-c7f9-4d36-b95a-e566de799654.png)

  - Mengakses dashboard kubernetes

  ```console
  minikube dashboard --url
  ```
  
  - Tunneling ke server











## Instalasi dan konfigurasi Ingress Controller Nginx
  - Mengunduh file deploy Nginx Ingress Controller
  
  ```console
  wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/baremetal/deploy.yaml
  ```

  - Menambahkan parameter `--watch-ingress-without-class=true` pada file deploy.yaml
  
  ```
  containers:
      - args:
        - /nginx-ingress-controller
        - --election-id=ingress-controller-leader
        - --controller-class=k8s.io/ingress-nginx
        - --ingress-class=nginx
        - --configmap=$(POD_NAMESPACE)/ingress-nginx-controller
        - --validating-webhook=:8443
        - --validating-webhook-certificate=/usr/local/certificates/cert
        - --validating-webhook-key=/usr/local/certificates/key
        - --watch-ingress-without-class=true
  ```
  
  - Mendeploy Nginx Ingress Controller melalui file deploy.yaml
  
  ```console
  kubectl apply -f deploy.yaml
  ```

  - Memverifikasi Nginx Ingress Controller
  
  ```console
  kubectl get pod -n ingress-nginx
  kubectl -n ingress-nginx get service
  ```
  
  - Memastikan bahwa pod berjalan dengan baik
  
  ```console
  kubectl log -n ingress-nginx (nama pod)
  ```
  
  - Membuat 2 deployment nginx dan apache
  
  ```console
  kubectl create deployment nginx-tes --image nginx
  kubectl create deployment apache-tes --image httpd
  ```
  
  - Memverifikasi deployment
  
  ```console
  kubectl get deployment
  ```
  
  - Ekspos deployment
  
  ```console
  kubectl expose deployment nginx-tes --port 80
  kubectl expose deployment apache-tes --port 80
  ```
  
  - Memverifikasi service
  
  ```console
  kubectl get service
  ```
  
  - Membuat file ingress-arip.yaml dengan isi file sebagai berikut:
  
  ```
    apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: ingress-nginx
    annotations:
      nginx.ingress.kubernetes.io/rewrite-target: /$1
  spec:
    rules:
      - host: nginx-tes.arip
        http:
          paths:
            - path: /
              pathType: Prefix
              backend:
                service:
                  name: nginx-tes
                  port:
                   number: 80
      - host: apache-tes.arip
        http:
          paths:
            - path: /
              pathType: Prefix
              backend:
                service:
                  name: apache-tes
                  port:
                    number: 80
  ```
  
  - Membuat ingress dari file ingress-arip.yaml

  ```console
  kubectl create -f ingress-arip.yaml
  ```
  
  - Memverifikasi ingress
  
  ```console
  kubectl get ingress
  ```
  
  - Melihat service dari semua namespace
  
  ```console
  kubectl get svc --all-namespaces
  ```
  
  - Mengubah service ingress-nginx-controller dengan editor nano
  
  ```console
  KUBE_EDITOR="nano" kubectl edit svc -n ingress-nginx ingress-nginx-controller
  ```
  
  - Menambahkan ip node master sebagai ip eksternal
  
  ```
    selector:
      app.kubernetes.io/component: controller
      app.kubernetes.io/instance: ingress-nginx
      app.kubernetes.io/name: ingress-nginx
    externalIPs:
    - IP-MASTER-LOCAL
  ```
  
  ### Testing
  
  **Linux**
  
  - Tambahkan baris baru di /etc/hosts
  
  ```
  IP-MASTER-LOCAL nginx-tes.arip apache-tes.arip
  ```
  
  - Akses menggunakan `curl`
  
  ```console
  curl nginx-tes.arip
  curl apache-tes.arip
  ```
   
  **Windows**
  
  - Tambahkan baris baru pada file host di direktori C:\Windows\System32\Drivers\etc\hosts, ubah file menggunakan notepad dengan izin administrator
  
  ```
  IP-MASTER-LOCAL nginx-tes.arip apache-tes.arip
  ```
  
  - Tunneling menggunakan *command prompt* agar dapat mengakses domain
  
  ```console
  ssh -D [sock] arip.id
  ```
  
  - Mengubah pengaturan proxy pada mozilla yang akan digunakan untuk mengakses domain
  
  GAMBAR PENGATURAN PROXY MOZILLA
  
  - Akses domain nginx-tes.arip
  
  GAMBAR AKSES NGINX
  
  - Akses domain apache-tes.arip

  GAMBAR AKSES APACHE

## Membuat Dynamic Storage Class dengan NFS

  - 

## Deploy Aplikasi Wordpress dan MySQL menggunakan Persistent Volume Claim (PVC)

  - Membuat file bernama kustomization.yaml untuk menyimpan password database
  
  ```
  secretGenerator:
  - name: mysql-pass
    literals:
    - password=arip123
  ```
  
  - Membuat file yang berisi konfigurasi untuk menambahkan resource service, pvc, dan deployment MySQL dengan file bernama mysql-deployment.yaml, variabel `MYSQL_ROOT_PASSWORD` mengambil password atau kata sandi dari secretGenerator dengan nama mysql-pass
    
  ```
  apiVersion: v1
kind: Service
metadata:
  name: wordpress-mysql
  labels:
    app: wordpress #label
spec:
  ports:
    - port: 3306
  selector:
    app: wordpress
    tier: mysql
  clusterIP: None
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
  labels:
    app: wordpress
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 20Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-mysql
  labels:
    app: wordpress
spec:
  selector:
    matchLabels:
      app: wordpress
      tier: mysql
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: wordpress
        tier: mysql
    spec:
      containers:
      - image: mysql:5.6
        name: mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-pass
              key: password
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
  ```
  
  - Membuat file yang berisi konfigurasi untuk menambahkan resource service, pvc, dan deployment Wordpress dengan file bernama wordpress-deployment.yaml, direktori persistent volume diarahkan pada `/var/www/html`
  
  ```
  apiVersion: v1
kind: Service
metadata:
  name: wordpress
  labels:
    app: wordpress
spec:
  ports:
    - port: 80
  selector:
    app: wordpress
    tier: frontend
  type: LoadBalancer
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: wp-pv-claim
  labels:
    app: wordpress
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 20Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress
  labels:
    app: wordpress
spec:
  selector:
    matchLabels:
      app: wordpress
      tier: frontend
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: wordpress
        tier: frontend
    spec:
      containers:
      - image: wordpress:4.8-apache
        name: wordpress
        env:
        - name: WORDPRESS_DB_HOST
          value: wordpress-mysql
        - name: WORDPRESS_DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-pass
              key: password
        ports:
        - containerPort: 80
          name: wordpress
        volumeMounts:
        - name: wordpress-persistent-storage
          mountPath: /var/www/html
      volumes:
      - name: wordpress-persistent-storage
        persistentVolumeClaim:
          claimName: wp-pv-claim
  ```
  
  - Menambahkan parameter resource berisi nama file konfigurasi yang telah dibuat
  
  ```
  resources:
    - mysql-deployment.yaml
    - wordpress-deployment.yaml
  ```
  
  - Mengaplikasikan situs wordpress dan database mysql
  
  ```console
  kubectl apply -k ./
  ```
  
  - Memverifikasi resource secrets yang menyimpan kata sandi atau password
  
  ```console
  kubectl get secrets
  ```
  
  - Memverifikasi resource persistent volume claim
  
  ```console
  kubectl get secrets
  ```
  
  - Memverifikasi resource pods
  
  ```console
  kubectl get secrets
  ```
  
  - Memverifikasi resource service wordpress
  
  ```console
  kubectl get secrets
  ```
  
  - Melihat url wordpress melalui service wordpress
  
  ```console
  kubectl get secrets
  ```
  
  - Testing wordpress menggunakan perintah curl
  
  ```console
  curl (url) atau (ip:port)
  ```
  
  - Testing wordpress melalui browser mozilla
  
  GAMBAR DASHBOARD WORDPRESS
  
  - aaaa

## Instalasi dan Konfigurasi MetalLB untuk Load Balancer
  
  - Mengunduh file konfigurasi metallb dari situs [raw.githubusercontent.com](raw.githubusercontent.com)

  ```console
  wget https://raw.githubusercontent.com/metallb/metallb/v0.12.1/manifests/namespace.yaml
  wget https://raw.githubusercontent.com/metallb/metallb/v0.12.1/manifests/metallb.yaml
  ```
  
  - Menginstal metallb versi 0.12.1 menggunakan file metallb-native.yaml
  
  ```console
  kubectl apply -f namespace.yaml
  kubectl apply -f metallb.yaml
  ```
  
  - Memverifikasi namespace metallb
  
  ```
  kubectl get namespace
  kubectl get all --namespace metallb-system
  ```
  
  - Membuat file konfigurasi configmap bernama metallb-cm.yaml dengan isi sebagai berikut:
  
  ```
  apiVersion: v1
kind: configMap
metadata:
  name: config
  namespace: metallb-system
data:  
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - range ip ( 10.1.1 - 10.1.100 ) sesuai network
  ```
  
  - Membuat resource configmap dari file metallb-cm.yaml

  ```console
  kubectl create -f metallb-cm.yaml
  ```
   
## Deploy Aplikasi Nginx dengan ekspos akses Load Balancer

  - Membuat deployment nginx
  
  ```console
  kubectl create deploy nginx --image nginx
  ```
  
  - Memverifikasi pod nginx

  ```console
  kubectl get pod
  ```
  
  - Ekspos deployment nginx dengan tipe loadbalancer

  ```console
  kubectl expose deploy nginx --port 80 --type LoadBalancer
  ```
  
  - Memastikan bahwa ip eksternal dari service nginx diambil dari loadbalancer metallb

  ```console
  kubectl get svc
  ```
  
  - Testing nginx menggunakan browser menggunakan ip dari loadbalancer

  GAMBAR BROWSING IP NGINX LOAD BALANCER
  
  
  
  ==========================================================================================
  
  









