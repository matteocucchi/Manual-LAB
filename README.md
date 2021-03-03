# Set up di un cluster Kubernetes con HA + Glusterfs + Heketi
## Vagrant Environment
|Role|FQDN|OS|CPU|RAM|IP|
|----|----|----|----|----|----|
|Gluster|gluster1.example.com|CentOS 7|1CPU|2GB RAM|172.16.16.11|
|Gluster|gluster2.example.com|CentOS 7|1CPU|2GB RAM|172.16.16.12|
|Client|client1.example.com|CentOS 7|1CPU|2GB RAM|172.16.16.20|
|Load Balancer|loadbalancer.example.com|CentOS 7|1CPU|1GB RAM|172.16.16.100| 
|Master|kmaster1.example.com|CentOS 7|2CPU|2GB RAM|172.16.16.101|
|Master|kmaster2.example.com|CentOS 7|2CPU|2GB RAM|172.16.16.102|
|Worker|kworker1.example.com|CentOS 7|1CPU|1GB RAM|172.16.16.201|

## Set up GlusterFS

## Set up del load balancer
1)Scaricare haproxy
    
    sudo yum install -y haproxy

2)Modificare il file /etc/haproxy/haproxy.cfg:

    frontend kubernetes-frontend
        bind 172.16.16.100:6443
        mode tcp
        option tcplog
        default_backend kubernetes-backend

    backend kubernetes-backend
        mode tcp
        option tcp-check
        balance roundrobin
        server kmaster1 172.16.16.101:6443 check fall 3 rise 2
        server kmaster2 172.16.16.102:6443 check fall 3 rise 2

3)Disabilitare il controllo di SELinux:
    
    sudo setsebool -P haproxy_connect_any=1

4)Disabilitari firewalld:

    sudo systemctl disable --now firewalld


## Set up cluster (tutti i nodi)
eseguire tutti i comandi come root!!

1)Modificare i file /etc/hosts in modo che si vedano le macchine.

2)Disabilitare SELinux:
    
    sudo setenforce 0
    modificare il file /etc/sysconfig/selinux

3)Disabilitare il Firewall:
    
    sudo systemctl disable --now firewalld

4)Disabilitare lo swap:
    
    eliminare/commentare da /etc/fstab lo swap
    sudo swapoff -a

5)Creazione delle impostazioni di sysctl per il networking di Kubernetes in :

    /etc/sysctl.d/kubernetes.conf
        net.bridge.bridge-nf-call-ip6tables=1
        net.bridge.bridge-nf-call-iptables=1
    sudo sysctl --system

6)Installare docker:

    yum install -y yum-utils device-mapper-persistent-data lvm2
    yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    yum install -y docker
    systemctl enable --now docker

7)Aggiungere la repository di Kubernetes:
    
    creare il file /etc/yum.repos.d/kubernetes.repo:
        [kubernetes]
        name=kubernetes
        baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
        enabled=1
        gpgcheck=1
        repo gpgcheck=1
        gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
    
8)Installare Kubernetes:

    sudo yum install -y kubectl kubeadm kubelet

9)Attivare il servizio kubelet:

    sudo systemctl enable --now kubelet

>ATTENZIONE: il servizio potrebbe dare un errore finchè non si effettua la kubeadm init.

## OPERAZIONI DA ESEGUIRE SOLO SU UNO DEI MASTER(es: kmaster1) 
1)Inizializzare il cluster:

    sudo kubeadm init --control-plane-endpoint="172.16.16.100:6443" --upload-certs --apiserver-advertise-address=172.16.16.101 --pod-network-cidr=10.244.0.0/16

2)per poter utilizzare i comandi dell'interfaccia del cluster gli utenti hanno bisogno delle impostazioni di kubernetes all'interno della propria cartella /home:

    mkdir /home/user/.kube
    sudo cp /etc/kubernetes/admin.conf /home/user/.kube/conf
    sudo chown -R user:user /home/user/.kube/conf

### NON COME ROOT!!!!!!
3)Scaricare il file kube-flannel.yml:

    wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

>    ATTENZIONE: se si utilizza vagrant modificare il file kube-flannel.yml, cercare con vim /flanneld, sotto "args:" aggiungere indentato correttamente "- --iface=eth1". Questo si fa perchè vagrant utilizza la iface eth0 per il collegamento ssh con le macchine che di default viene utilizata da flanneld.

4)se si è dimenticato il token per l'aggiunta di nuovi nodi al cluster:

    sudo kubeadm token create --print-join-command


## SUGLI ALTRI NODI:
1)Aggiungere un nodo:

    eseguire il comando restituito come output nel passaggio 4 come root.
>ATTENZIONE: distinguere il tipo di nodo da aggiungere per utilizzare la join corretta.

