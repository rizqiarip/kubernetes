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

  - Instalasi NFS pada node masterFirst lets install NFS server on the host machine, and create a directory where our NFS server will serve the files:
$ sudo systemctl status nfs-server
$ sudo apt install nfs-kernel-server nfs-common portmap
$ sudo start nfs-server
$ mkdir -p /srv/nfs/mydata 
$ chmod -R 777 nfs/ # for simple use but not advised
Exporting our directory, note that this is an insecure configuration do no use in production:

$ sudo vi /etc/exports
/srv/nfs/mydata  *(rw,sync,no_subtree_check,no_root_squash,insecure)
$ sudo exportfs -rv
exporting *:/srv/nfs/mydata
$ showmount -e
/srv/nfs/mydata  *
Login to the kubernetes cluster, in this case minikube, user: docker; passwd: tcuser, making sure that minikube can ping the localhost and mounting the NFS server in minikube:

$ kubectl get nodes
$ minikube ip
192.168.99.157
$ ssh docker@192.168.99.157
# ping localhostip
$ showmount 192.168.1.7
Hosts on 192.168.1.7:
$ mount -t nfs 192.168.1.7:/srv/nfs/mydata /mnt
$ mount | grep mydata
192.168.1.7:/srv/nfs/mydata on /mnt type nfs4
$ exit
2. Now lets create an NFS persistence volume storage class, nfs.yaml:


Deploying nfs.yaml:

$ kubectl apply -f nfs.yaml
$ kubectl get pv,pvc
persistentvolume/nfs-pv   100Mi      RWX            Retain           Available
3. Creating persistence volume claim file and deploying it. The accessModes must be the same as the persistence volume that it needs to access:


$ kubectl apply -f nfs_pvc.yaml
$ kubectl get pvc,pv
persistentvolumeclaim/nfs-pvc   Bound    nfs-pv   100Mi      RWX
4. Lets create a pod to access this pvc using a simple Nginx deployment. The most important line is the claimName which must be the same as the persistence volume claim that the application will use:


Deploying Nginx:

$ kubectl apply -f nfs_pod.yaml 
$ kubectl get po
nfs-nginx-6cb55d48f7-q2bvd   1/1     Running
5. Testing

Creating a test.html in the Nginx pod:

$ kubectl exec -it nfs-nginx-6cb55d48f7-q2bvd bash
$ sudo vi /usr/share/nginx/html/test.html
<h1. this should hopefully work</h1>
Verifying that the host machine has now the same file and verifying that Nginx can read the file:

$ ls /srv/nfs/mydata$
$ cat /srv/nfs/mydata/test.html
<h1>this should hopefully work</h1>
$ kubectl expose deploy nfs-nginx --port 80 --type NodePort
$ kubectl get svc
$ nfs-nginx    NodePort    10.102.226.40   <none>        80:32669/TCP
Open the browser and input minikubeIP:32669:


Deleting everything and verifying that the test file is still saved in on our directory:

$ kubectl delete deploy nfs-nginx
$ kubectl delete pvc nf-pvc
--> kubectl delete svc nfs-nginx
$ ls /srv/nfs/mydata/
test.html

## Deploy Aplikasi Wordpress + DB (Mengunakan PVC)

## Instalasi dan Konfigurasi MetalLB untuk Load Balancer

## Deploy Aplikasi Nginx dengan ekspose akses Load Balancer

