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
  
  ![image](https://user-images.githubusercontent.com/89076954/184474498-09612da1-5efa-4af9-92f1-b024a4cc1e97.png)

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

  - Mengecek node yang ada di dalam cluster

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


## Instalasi dan konfigurasi Ingress Controller Nginx
  
  - Mengaktifkan addons ingress pada `minikube`

  ```console
  minikube addons enable ingress
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184480671-dfbbad31-5f79-4bff-a495-09669a3a1450.png)
  
  - Mengecek pod yang ada di dalam namespace `ingress-nginx`
  
  ```console
  kubectl get pod -n ingress-nginx
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184493098-7fa3c4bb-390d-4083-9364-6ff4484c5f75.png)
  
  - Membuat 2 deployment nginx dan apache
  
  ```console
  kubectl create deployment nginx-arip --image nginx
  kubectl create deployment apache-arip --image httpd
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184473171-96032cc3-2e2b-44a9-952c-2269b6cc3687.png)

  - Memverifikasi deployment
  
  ```console
  kubectl get deployment
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184473194-80a2a9bf-34e7-4c21-9c07-2d7f1a33bc1d.png)

  - Ekspos deployment
  
  ```console
  kubectl expose deployment nginx-arip --type=NodePort --port 80
  kubectl expose deployment apache-arip --type=NodePort --port 80
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184493076-d798cfbe-b1ca-49fb-bae4-f6191ec500ca.png)
  ![image](https://user-images.githubusercontent.com/89076954/184493069-850fb363-4193-4f67-8ae4-0f9c59bb11e6.png)

  - Memverifikasi service
  
  ```console
  kubectl get service
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184493109-00bcd7a8-296c-417f-885c-f51317c793cc.png)

  - Membuat file `ingress` bernama ingress-arip.yaml dengan isi sebagai berikut:
  
  ```
  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: ingress-arip
    annotations:
      nginx.ingress.kubernetes.io/rewrite-target: /$1
  spec:
    rules:
      - host: nginx.arip
        http:
          paths:
            - path: /
              pathType: Prefix
              backend:
                service:
                  name: nginx-arip
                  port:
                    number: 80
     - host: apache.arip  
        http:
          paths:
            - path: /
              pathType: Prefix
              backend:
                service:
                  name: apache-arip
                 port:
                    number: 80
  ```
  
  - Membuat `ingress` dari file ingress-arip.yaml
  
  ```console
  kubectl apply -f ingress-arip.yaml
  ```
  
  - Mengecek ip `ingress`
  
  ```console
  kubectl get ingress
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184493334-f3faf104-4b1d-4767-8cb1-d06e5b621438.png)

  - Menambahkan baris baru pada `/etc/hosts` agar dapat mengenali domain
  
  ```
  192.168.39.134 nginx.arip apache.arip
  ```
  
  - Tes akses menggunakan perintah curl
  
  ```console
  curl nginx.arip
  curl apache.arip
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184493435-7ef951ef-ba33-4b81-8c0b-217ff6b1845f.png)

Referensi : [https://kubernetes.io/docs/tasks/access-application-cluster/ingress-minikube/] (https://kubernetes.io/docs/tasks/access-application-cluster/ingress-minikube/)

## Membuat Dynamic Storage Class dengan NFS

  
  - Menginstal NFS server
  
  ```console
  sudo apt-get update
  sudo apt install nfs-kernel-server nfs-common portmap
  sudo systemctl start nfs-server
  sudo systemctl status nfs-server
  ```
  
  - Membuat dan Mengekspor direktori dengan menambah baris baru pada file `/etc/exports`
  
  ```console
  mkdir -p /data
  sudo nano /etc/exports
  /data  *(rw,sync,no_subtree_check,no_root_squash,insecure)
  sudo exportfs -rv
  showmount -e
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184535054-4634dc2a-f812-43a9-9f66-022c386f270e.png)
  
  - Me *mounting* nfs melalui ssh
  
  ```console
  minikube ip
  ssh docker@192.168.39.192 (pass: tcuser)
  mount -t nfs 192.168.39.1:/data /mnt
  mount | grep mydata
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184533014-4dd58062-8ba6-4679-9d7c-834f1396f59d.png)
  
  - Membuat file bernama nfs.yaml untuk membuat `persistence volume`
  
  ```
  apiVersion: v1
  kind: PersistentVolume
  metadata:
    name: nfs-pv
    labels:
      name: mynfs
  spec:
    storageClassName: manual
    capacity:
      storage: 200Mi
    accessModes:
      - ReadWriteMany
    nfs:
      server: 192.168.39.1
      path: "/data"
  ```
  
  - Mendeploy dan memverifikasi `persistent volume` nfs 
  
  ```console
  kubectl apply -f nfs.yaml
  kubectl get pv nfs-pv
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184533168-4829d75b-68e5-4558-b184-a6088a9b76cd.png)

  - Membuat file bernama nfs_pvc.yaml untuk membuat `persistence volume claim` 
  
  ```
  apiVersion: v1
  kind: PersistentVolumeClaim
  metadata:
   name: nfs-pvc
  spec:
    storageClassName: manual
    accessModes:
     - ReadWriteMany
    resources:
      requests:
      storage: 50Mi
  ```
  
  - Mendeploy dan memverifikasi `persistent volume claim` nfs
  
  ```console
  kubectl apply -f nfs_pvc.yaml
  kubectl get pvc nfs-pvc
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184533378-62e1abb2-502c-4dfa-abd3-27a4d5032c8f.png)
  
  - Membuat file deployment `nginx` dengan pvc `nfs-pvc` dan mountpath `/usr/share/nginx/html` bernama nginx.yaml
  
  ```
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    creationTimestamp: null
    labels:
      app: nginx
    name: nginx-with-pvc
  spec:
    progressDeadlineSeconds: 2147483647
    replicas: 1
    revisionHistoryLimit: 2147483647
    selector:
      matchLabels:
        app: nginx
    strategy:
      rollingUpdate:
        maxSurge: 1
        maxUnavailable: 1
      type: RollingUpdate
    template:
      metadata:
        creationTimestamp: null
        labels:
          app: nginx
      spec:
        containers:
        - image: nginx
          imagePullPolicy: Always
          name: webserver
          ports:
          - containerPort: 80
            protocol: TCP
          resources: {}
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
          volumeMounts:
          - mountPath: /usr/share/nginx/html
            name: webservercontent
        dnsPolicy: ClusterFirst
        restartPolicy: Always
        schedulerName: default-scheduler
        securityContext: {}
        terminationGracePeriodSeconds: 30
        volumes:
        - name: webservercontent
          persistentVolumeClaim:
            claimName: nfs-pvc
  status: {}  
  ```

  - Mendeploy dan memverifikasi `nginx` melalui file `nginx.yaml`
  
  ```console
  kubectl apply -f nginx.yaml
  kubectl get pod
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184534277-2a69ef73-0b0a-4a7f-b5d1-d6d01e6825fd.png)

  - Membuat file bernama nginx-svc.yaml untuk membuat `service` bernama nginx-server 
  
  ```
  apiVersion: v1
  kind: Service
  metadata:
    name: nginx-server
  spec:
    type: NodePort
    ports:
    - port: 80
      protocol: TCP
    selector:
      app: nginx
  ```
  
  - Mendeploy dan memverifikasi `service` melalui file nginx-svc.yaml
  
  ```console
  kubectl apply -f nginx-svc.yaml
  kubectl get svc
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184534285-e89b9633-978b-4996-9ea5-6cdcc95dc36c.png)

  - Membuat file `index.html` pada direktori `/data`
  
  ![image](https://user-images.githubusercontent.com/89076954/184533740-f7b2b8ac-cfd1-497a-97ba-6f0f8f096839.png)

  - Tes `nginx` yang telah dibuat menggunakan perintah curl
  
   ```console
   minikube service nginx-server --url
   curl http://192.168.39.192:32152
   ```
   
   ![image](https://user-images.githubusercontent.com/89076954/184534357-6e13a4a0-52a2-4c61-a4d2-848a7e3e6118.png)

  - Testing nfs dengan mengubah file `index.html` pada direktori `/data` dan mengakses ulang `nginx`
  
  ```console
  sudo nano /data/index.html
  tes storage nfs rizqiarip ! v2
  curl http://192.168.39.192:32152
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184534422-38a79dd8-14e5-43ee-b4f1-718e5cab1012.png)

  - Melihat file `index.html` yang ada di dalam container nginx
  
  ```console
  kubectl exec -it nginx-with-pvc-6ccbd669c8-lvz6s bash
  cat /usr/share/nginx/html/index.html
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184534635-2eec7f15-f3e0-494f-a5e7-acebfcc31c36.png)

  - Menghapus semua resource menggunakan `kustomization` dan mengecek file `index.html`
  
  ```console
  sudo nano kustomization.yaml
  kubectl delete -k ./
  cat /data/index.html
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184534728-f9a30e79-fdf5-4f45-a8dd-8b521b5e5885.png)
  
  Dynamic NFS provisioning
  
  Masalah dengan metode sebelumnya adalah bahwa seseorang harus membuat setiap volume persistensi untuk semua permintaan pvc, yang dapat memakan waktu. Cara untuk mengatasi ini adalah dengan memiliki klien NFS yang secara otomatis akan melakukannya untuk kami. Meminta klaim volume persistensi secara otomatis akan memicu pembuatan volume persistensi. Agar hal ini terjadi, klien NFS harus diinstal di kluster kubernetes, dan akses harus diberikan kepada klien menggunakan akun layanan.
  
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
  type: NodePort
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
  ```
  
  - Menambahkan baris baru berisi nama file deployment pada file `kustomization.yaml`
  
  ```
  resources:
    - mysql-deployment.yaml
    - wordpress-deployment.yaml
  ```
  
  - Mengaplikasikan situs `wordpress` dan database `mysql`
  
  ```console
  kubectl apply -k ./
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184496165-6d698423-02d8-4b8a-85dc-84846556d82d.png)

  - Memverifikasi resource `secrets` yang menyimpan kata sandi atau password
  
  ```console
  kubectl get secrets
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184496177-8b50810b-511d-4cb9-af77-83bb76ed5e68.png)

  - Memverifikasi resource service wordpress
  
  ```console
  kubectl get svc
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184496187-e33eac76-e2ce-491a-994b-f1384afdf600.png)

  - Memverifikasi resource persistent volume claim
  
  ```console
  kubectl get pvc
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184496196-8131973e-8a24-4aa6-a6e9-6395768f3217.png)

  - Memverifikasi resource pods
  
  ```console
  kubectl get pod
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184496212-1e99c238-0c83-4214-9f8f-5b768de7f185.png)

  - Melihat url service wordpress menggunakan perintah `minikube`
  
  ```console
  minikube service wordpress --url
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184496234-cd642ea6-1c5b-4575-9082-dc1ea49719a6.png)

  - Tunneling melalui cmd agar dapat mengakses wordpress
  
  ```console
  ssh 34.121.197.14 -l rizqiarifwibowo19 -D32227
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184495709-cc2b3187-99ba-464f-8266-510d3e24fc9c.png)

  - Atur pengaturan proxy pada mozilla sesuai port yang akan diakses
  
    ![image](https://user-images.githubusercontent.com/89076954/184495799-63343c90-acb6-4f48-8159-a7e163abbc2b.png)
  
  - Tes akses wordpress dengan memasukan url http://192.168.39.134:32227
  
    ![image](https://user-images.githubusercontent.com/89076954/184496342-e4fffde0-f4ce-44db-89ba-f2c59268490a.png)
  
  - Instalasi wordpress
  
    ![image](https://user-images.githubusercontent.com/89076954/184496359-de4df4aa-3bf1-4df8-9095-f756f66817c7.png)

  - Tampilan dashboard wordpress
  
    ![image](https://user-images.githubusercontent.com/89076954/184496629-cca48431-a2f0-4c61-a639-60cfba5ced2e.png)

Referensi : [https://kubernetes.io/docs/tutorials/stateful-application/mysql-wordpress-persistent-volume/](https://kubernetes.io/docs/tutorials/stateful-application/mysql-wordpress-persistent-volume/)

## Instalasi dan Konfigurasi MetalLB untuk Load Balancer
  
  - Mengecek status addons `metallb` pada minikube
  
  ```console
  minikube addons list
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184503120-c6387ed7-addc-4907-b86d-14311de0801f.png)

  - Mengaktifkan addons `metallb` yang ada pada minikube
  
  ```console
  minikube addons enable metallb
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184503009-f54748b8-b3bd-4e3f-b796-eb7ac4b58837.png)

  - Mengonfigurasi ip address yang digunakan `metallb` untuk layanan loadbalancer dalam kasus ini penulis menggunakan jangkuan ip 192.168.39.150 - 192.168.39.170
  
  ```console
  minikube addons configure metallb
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184503227-a96f4e8c-c572-4795-a615-6effeaa831e6.png)

  - Melihat konfigurasi yang telah dibuat
  
  ```console
  kubectl get configmap/config -n metallb-system -o yaml
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184503279-207eef4b-b1aa-4c81-836d-8587ea140c06.png)

### Deploy Aplikasi Nginx dengan ekspos akses Load Balancer

  - Membuat deployment bernama nginx dengan image nginx
  
  ```console
  kubectl create deploy nginx --image nginx
  ```
  
    - Memverifikasi pod nginx

  ```console
  kubectl get pod
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184503426-a0eafed2-e851-477c-9608-8d62ff8a6960.png)

  - Mengekspos deployment nginx dengan tipe loadbalancer

  ```console
  kubectl expose deploy nginx --port 80 --type LoadBalancer
  ```
  
  - Memastikan bahwa ip eksternal dari service nginx diambil dari loadbalancer metallb

  ```console
  kubectl get svc
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184503598-766e4fcc-958d-47af-929a-403850149bde.png)

  - Testing nginx menggunakan curl
  
  ```console
  curl http://192.168.39.150
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184503655-00d53732-2d7a-48bb-84bd-d185c36bb038.png)
  
  - Testing nginx melalui browser mozilla
  
  ![image](https://user-images.githubusercontent.com/89076954/184503689-9ac8b69c-a346-4748-ae85-b0c845992715.png)


- Testing tambahan menggunakan deployment `apache` (kondisi yang harusnya terjadi adalah deployment apache mendapatkan ip 192.168.39.151)
  
  ```console
  kubectl create deployment apache-metallb --image httpd
  kubectl expose deployment apache-metallb --type LoadBalancer --port 80
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184504021-64fdf772-295b-48b1-92ea-f15592c4279a.png)

  - Mengecek ip eksternal yang didapat `apache`
  
  ```console
  kubectl get svc apache-metallb
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184504085-6434cf91-f897-4c44-9e33-1851af298b51.png)

  - Tes akses ip load balancer menggunakan curl
  
  ```console
  curl 192.168.39.151
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184504102-e1d9a439-0622-4e09-bd57-6b0fb1ab3574.png)


Referensi: https://kubebyexample.com/en/learning-paths/metallb/install

  ===================================================================================
  
  









