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
  
  - Mengubah file `kubectl` dan memindahkan nya ke direktori `/usr/local/bin`

  ```console
  chmod +x kubectl
  mv kubectl /usr/local/bin
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184542890-4f2fd53d-fbd4-49df-b4f2-df880b2151e3.png)

  - Memeriksa versi dari `kubectl`

  ```console
  kubectl version -o json
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184470872-7f998ca3-6be5-4bea-aef1-7eeae35107fd.png)

  - Menginstal `docker-machine-driver-kvm2`

  ```console
  curl -LO https://storage.googleapis.com/minikube/releases/latest/docker-machine-driver-kvm2
  ```
  
  - Mengubah file `docker-machine-driver-kvm2` dan memindahkannya ke direktori /usr/local/bin
  
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

  - Melihat `minikube` yang berjalan menggunakan perintah `virsh`

  ```console
  sudo virsh list
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184470990-060c256e-9121-46bf-82c3-a95f8a14fb00.png)

  - Mengecek status cluster

  ```console
  kubectl cluster-info
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184471015-2524e199-6701-44b3-b931-a259616adcd2.png)

  - Melihat konfigurasi `minikube` yang berada di direktori `~/.minikube/machines/minikube/config.json`

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

Referensi : https://computingforgeeks.com/how-to-run-minikube-on-kvm/#:~:text=To%20run%20Minikube%20on%20KVM%2C%20you%20need%20to%20install%20some,hosts%20with%20docker%2Dmachine%20commands

## Instalasi dan konfigurasi Ingress Controller Nginx
  
  - Mengaktifkan addons ingress pada `minikube`

  ```console
  minikube addons enable ingress
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184480671-dfbbad31-5f79-4bff-a495-09669a3a1450.png)
  
  - Mengecek `pod` yang ada di dalam `namespace` ingress-nginx
  
  ```console
  kubectl get pod -n ingress-nginx
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184493098-7fa3c4bb-390d-4083-9364-6ff4484c5f75.png)
  
  - Membuat 2 deployment `nginx` dan `apache`
  
  ```console
  kubectl create deployment nginx-arip --image nginx
  kubectl create deployment apache-arip --image httpd
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184473171-96032cc3-2e2b-44a9-952c-2269b6cc3687.png)

  - Memverifikasi deployment yang telah dibuat
  
  ```console
  kubectl get deployment
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184473194-80a2a9bf-34e7-4c21-9c07-2d7f1a33bc1d.png)

  - Ekspos deployment nginx-arip dan apache-arip
  
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

Referensi : https://kubernetes.io/docs/tasks/access-application-cluster/ingress-minikube/

## Membuat Dynamic Storage Class dengan NFS

  
  - Menginstal dan mengaktifkan paket `nfs`
  
  ```console
  sudo apt-get update
  sudo apt install nfs-kernel-server nfs-common portmap
  sudo systemctl start nfs-server
  sudo systemctl status nfs-server
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184544138-f5df5234-591d-4e3f-b305-960b2c000265.png)

  - Membuat dan Mengekspor direktori dengan menambah baris baru pada file `/etc/exports`
  
  ```console
  mkdir -p /data
  sudo nano /etc/exports
  /data  *(rw,sync,no_subtree_check,no_root_squash,insecure)
  sudo exportfs -rv
  showmount -e
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184535054-4634dc2a-f812-43a9-9f66-022c386f270e.png)
  
  - Me *mounting* direktori `/data` melalui ssh
  
  ```console
  minikube ip
  ssh docker@192.168.39.192 (pass: tcuser)
  mount -t nfs 192.168.39.1:/data /mnt
  mount | grep mydata
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184533014-4dd58062-8ba6-4679-9d7c-834f1396f59d.png)
  
  - Membuat file bernama nfs.yaml untuk membuat `persistentvolume` dengan isi sebagai berikut:
  
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
  
  - Mendeploy dan memverifikasi `persistent volume` bernama nfs-pv
  
  ```console
  kubectl apply -f nfs.yaml
  kubectl get pv nfs-pv
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184533168-4829d75b-68e5-4558-b184-a6088a9b76cd.png)

  - Membuat file bernama nfs_pvc.yaml untuk membuat `persistentvolumeclaim` 
  
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
  
  - Mendeploy dan memverifikasi `persistentvolumeclaim` bernama nfs-pvc
  
  ```console
  kubectl apply -f nfs_pvc.yaml
  kubectl get pvc nfs-pvc
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184533378-62e1abb2-502c-4dfa-abd3-27a4d5032c8f.png)
  
  - Membuat file deployment bernama nginx.yaml yang menggunakan nfs-pvc sebagai `persistentvolumeclaim` dan mountpath pada direktori `/usr/share/nginx/html`
  
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

  - Mendeploy dan memverifikasi `pod`
  
  ```console
  kubectl apply -f nginx.yaml
  kubectl get pod
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184534277-2a69ef73-0b0a-4a7f-b5d1-d6d01e6825fd.png)

  - Membuat file bernama nginx-svc.yaml untuk mengekspos deployment
  
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
  
  - Mendeploy dan memverifikasi `service`
  
  ```console
  kubectl apply -f nginx-svc.yaml
  kubectl get svc
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184534285-e89b9633-978b-4996-9ea5-6cdcc95dc36c.png)

  - Membuat file index.html pada direktori `/data`
  
  ```console
  sudo nano /data/index.html
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184533740-f7b2b8ac-cfd1-497a-97ba-6f0f8f096839.png)

  - Tes akses deployment `nginx` yang telah dibuat menggunakan perintah curl
  
   ```console
   minikube service nginx-server --url
   curl http://192.168.39.192:32152
   ```
   
   ![image](https://user-images.githubusercontent.com/89076954/184534357-6e13a4a0-52a2-4c61-a4d2-848a7e3e6118.png)

  - Tes nfs dengan mengubah file `index.html` pada direktori `/data` dan mengakses ulang `nginx`
  
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
  
  ###Dynamic NFS provisioning
  
  - Membuat dan Mengekspor direktori dengan menambah baris baru pada file `/etc/exports`
  
  ```console
  mkdir -p /data2
  sudo nano /etc/exports
  /data2  *(rw,sync,no_subtree_check,no_root_squash,insecure)
  sudo exportfs -rv
  showmount -e
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184540869-631a4f1b-ef7e-4444-ad50-f8338221e70f.png)
  
  - Me *mounting* nfs melalui ssh
  
  ```console
  minikube ip
  ssh docker@192.168.39.192 (pass: tcuser)
  mount -t nfs 192.168.39.1:/data2 /mnt
  df -Th
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184540846-8d737ef4-e7dd-4676-8b4d-2496f063a653.png)
  
  - Membuat file bernama rbac.yaml untuk membuat `serviceaccount`, `clusterrole`, `clusterrolebinding`, `role`, dan `rolebinding`
  
  ```
  apiVersion: v1
  kind: ServiceAccount
  metadata:
    name: nfs-client-provisioner
    namespace: default
  ---
  kind: ClusterRole
  apiVersion: rbac.authorization.k8s.io/v1
  metadata:
    name: nfs-client-provisioner-runner
  rules:
    - apiGroups: [""]
      resources: ["persistentvolumes"]
      verbs: ["get", "list", "watch", "create", "delete"]
    - apiGroups: [""]
      resources: ["persistentvolumeclaims"]
      verbs: ["get", "list", "watch", "update"]
    - apiGroups: ["storage.k8s.io"]
      resources: ["storageclasses"]
      verbs: ["get", "list", "watch"]
    - apiGroups: [""]
      resources: ["events"]
      verbs: ["create", "update", "patch"]
  ---
  kind: ClusterRoleBinding
  apiVersion: rbac.authorization.k8s.io/v1
  metadata:
    name: run-nfs-client-provisioner
  subjects:
    - kind: ServiceAccount
      name: nfs-client-provisioner
      namespace: default
  roleRef:
    kind: ClusterRole
    name: nfs-client-provisioner-runner
    apiGroup: rbac.authorization.k8s.io
  ---
  kind: Role
  apiVersion: rbac.authorization.k8s.io/v1
  metadata:
    name: leader-locking-nfs-client-provisioner
    namespace: default
  rules:
    - apiGroups: [""]
      resources: ["endpoints"]
      verbs: ["get", "list", "watch", "create", "update", "patch"]
  ---
  kind: RoleBinding
  apiVersion: rbac.authorization.k8s.io/v1
  metadata:
    name: leader-locking-nfs-client-provisioner
    namespace: default
  subjects:
    - kind: ServiceAccount
      name: nfs-client-provisioner
      namespace: default
  roleRef:
    kind: Role
    name: leader-locking-nfs-client-provisioner
    apiGroup: rbac.authorization.k8s.io
  ```
  
  - Mendeploy dan memverifikasi rbac.yaml 
  
  ```console
  kubectl apply -f rbac.yaml
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184541484-0bdef4cb-8c61-4c86-a9bc-ac0e8b9f10bc.png)

  - Membuat file bernama deployment.yaml
  
  ```
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: nfs-client-provisioner
    labels:
      app: nfs-client-provisioner
    namespace: default
  spec:
    replicas: 1
    strategy:
      type: Recreate
    selector:
      matchLabels:
        app: nfs-client-provisioner
    template:
      metadata:
        labels:
          app: nfs-client-provisioner
      spec:
        serviceAccountName: nfs-client-provisioner
        containers:
          - name: nfs-client-provisioner
            image: quay.io/external_storage/nfs-client-provisioner:latest
            volumeMounts:
              - name: nfs-client-root
                mountPath: /persistentvolumes
            env:
              - name: PROVISIONER_NAME
                value: fuseim.pri/ifs
              - name: NFS_SERVER
                value: 192.168.39.1
              - name: NFS_PATH
                value: /data2
        volumes:
          - name: nfs-client-root
            nfs:
              server: 192.168.39.1
              path: /data2
  ```

  - Mendeploy dan memverifikasi deployment.yaml
  
  ```console
  kubectl apply -f deployment.yaml
  kubectl get deploy
  ```

  - Membuat file bernama class.yaml untuk membuat `storageclass` 
  
  ```
  apiVersion: storage.k8s.io/v1
  kind: StorageClass
  metadata:
    name: managed-nfs-storage
  provisioner: fuseim.pri/ifs 
  parameters:
    archiveOnDelete: "false"

  ```

  - Mendeploy dan memverifikasi class.yaml
  
  ```console
  kubectl apply -f class.yaml
  kubectl get sc
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184541825-cd069e40-24a0-4d39-ad16-b29fcb1678d6.png)

  - Mengganti default `storageclass`
  
  ```console
  kubectl patch storageclass standard -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'
  kubectl patch storageclass managed-nfs-storage -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184541842-6454f290-d906-4e93-bdb7-b4ccf00b5aae.png)

  - Membuat file bernama pv.yaml untuk membuat `persistentvolume` 
  
  ```
  apiVersion: v1
  kind: PersistentVolume
  metadata:
    name: nfs-pv
    labels:
      name: mynfs
  spec:
    storageClassName: managed-nfs-storage
    capacity:
      storage: 200Mi
    accessModes:
      - ReadWriteMany
    nfs:
      server: 192.168.39.1
      path: "/data2"
  ```

  - Mendeploy dan memverifikasi pv.yaml
  
  ```console
  kubectl apply -f pv.yaml
  kubectl get pv
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184542159-e4679565-456c-4898-9f55-6ea79c2e394e.png)

  - Membuat file bernama test-claim.yaml untuk membuat `persistentvolumeclaim` 
  
  ```
  kind: PersistentVolumeClaim
  apiVersion: v1
  metadata:
    name: test-claim
    annotations:
      volume.beta.kubernetes.io/storage-class: "managed-nfs-storage"
  spec:
    storageClassName: managed-nfs-storage
    accessModes:
      - ReadWriteMany
    resources:
      requests:
        storage: 1Mi
  ```

  - Mendeploy dan memverifikasi `persistentvolumeclaim`
  
  ```console
  kubectl apply -f test-claim.yaml
  kubectl get pvc
  ```

  ![image](https://user-images.githubusercontent.com/89076954/184542227-61dab3c8-761c-4f8b-8276-1bbd80213c98.png)

  - Membuat file bernama test-pod.yaml untuk membuat `busybox` yang bertugas membuat file bernama SUCCESS ke dalam direktori `/mnt` yang terhubung juga dengan direktori `/data2`
  
  ```
  kind: Pod
  apiVersion: v1
  metadata:
    name: test-pod
  spec:
    containers:
    - name: test-pod
      image: gcr.io/google_containers/busybox:1.24
      command:
        - "/bin/sh"
      args:
        - "-c"
        - "touch /mnt/SUCCESS && exit 0 || exit 1"
      volumeMounts:
        - name: nfs-pvc
          mountPath: "/mnt"
    restartPolicy: "Never"
    volumes:
      - name: nfs-pvc
        persistentVolumeClaim:
          claimName: test-claim
  ```
  
  - Mendeploy dan memverifikasi `pod`
  
  ```console
  kubectl apply -f test-pod.yaml
  kubectl get pod
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184542509-5ebc31da-4103-4694-a778-e493b81befbc.png)

  - Melihat isi direktori '/data2'
  
  ```console
  ls /data2
  ```
  
  ![image](https://user-images.githubusercontent.com/89076954/184542580-8f0989b0-1f01-4430-ad21-333d98896e3f.png)

Referensi : https://medium.com/@myte/kubernetes-nfs-and-dynamic-nfs-provisioning-97e2afb8b4a9
            https://github.com/kubernetes-retired/external-storage/tree/master/nfs-client
            https://en.cdmana.com/2022/185/202207040708159405.html#2waiting_for_a_volume_to_be_created_either_by_external_provisioner_fuseimpriifs_or_manually_created_by_system_administrator_337


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
  
  









