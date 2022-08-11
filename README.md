# Kubernetes Orchestration Documentation

Dokumentasi Lab Kubernetes Orchestration Container oleh Rizqi Arif Wibowo - 11 Agustus 2022

## Topologi Arsitektur Kubernetes
  
  Gambar topologi arsitektur kubernetes

## Instalasi Kubernetes Cluster

  Eksekusi perintah pada node master dan worker
  
  - Memperbarui paket
  
  ```console
  sudo apt update 
  
  sudo apt upgrade -y 
  
  sudo apt autoremove -y
  ```
  
  - Instal paket docker
  
  ```console
  sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software properties-common -y
  
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
  
  sudo apt-key fingerprint 0EBFCD88
  
  sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
  
  sudo apt-get update -y
  
  sudo apt-get install docker-ce docker-ce-cli containerd.io -y
  ```
  
  - Instal kubectl, kubelet dan kubeadm

  ```console
  sudo apt install -y apt-transport-https; curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
  ```

  - Menambahkan repositori kubectl, kubelet dan kubeadm
  
  ```console
  echo 'deb http://apt.kubernetes.io/ kubernetes-xenial main' > kubernetes.list

  sudo mv kubernetes.list /etc/apt/sources.list.d/kubernetes.list
  ```
  - Memperbarui paket dan menginstal kubectl, kubelet dan kubeadm
  
  ```console
  sudo apt update; sudo apt install -y kubectl kubelet kubeadm
  
  apt-mark hold kubelet kubeadm kubectl
  ```
  
  ### Inisialisasi Kubernetes (eksekusi perintah pada node master)
  
  - Inisialisasi kubeadm
  
  ```console
  swapon -s
  
  sudo swapoff -a
  
  sudo kubeadm init --pod-network-cidr=10.244.XX.0/16
  ```
  
  - Menyalin konfigurasi admin dari direktori kubernetes
  
  ```console
  mkdir -p $HOME/.kube
  
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
  ```
  
  - Memverifikasi config dan cluster
  
  ```console
  kubectl config view
  
  kubectl cluster-info
  ```
  
  ### Membuat Node Worker bergabung ke dalam Cluster
  
  - Menampilkan token dan token-ca-cert-hash (eksekusi perintah di node master)
  
  ```console
  sudo kubeadm token list
  
  sudo openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'
  ```
  
  - Memasukkan node worker ke dalam cluster (eksekusi perintah di node worker)
  
  ```console
  swapon -s
  
  sudo swapoff -a
  
  sudo kubeadm join --token [TOKEN] [NODE-MASTER]:6443 --discovery-token-ca-cert-hash sha256:[TOKEN-CA-CERT-HASH]
  ```
  
  - Memverifikasi jika node worker sudah bergabung ke dalam cluster kubernetes (eksekusi perintah di node master)
  
  ```console
  kubectl get nodes
  ```
  
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
  
  









