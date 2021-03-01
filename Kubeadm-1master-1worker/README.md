# Creazione di un cluster Kubernetes con 1 master e 1 worker tramite kubeadm
Creazione di un Cluster con Kubernetes.

1)Creare un Vagrantfile che avvii le seguenti macchine:
    -Master: 1CPU, 2GB RAM, IP 172.16.16.100
    -Worker: 1CPU, 1GB RAM, IP 172.16.16.101

2)Modificare i file /etc/hosts in modo che si vedano le macchine.

3)Installare docker: 
    sudo yum install docker
    sudo systemctl enable --now docker

4)Disabilitare SELinux:
    sudo setenforce 0
    modificare il file /etc/sysconfig/selinux

5)Disabilitare il Firewall:
    sudo systemctl disable firewalld
    sudo systemctl stop firewalld

6)Disabilitare lo swap:
    eliminare/commentare da /etc/fstab lo swap
    sudo swapoff -a

7)Update delle impostazioni di sysconf per il networking di Kubernetes:
    creazione file /etc/sysctl.d/kubernetes.conf:
        net.bridge.bridge-nf-call-ip6tables=1
        net.bridge.bridge-nf-call-iptables=1
    sudo sysctl --system

8)Aggiungere la repository di Kubernetes:
    creare il file /etc/yum.repos.d/kubernetes.repo:
        [kubernetes]
        name=kubernetes
        baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
        enabled=1
        gpgcheck=1
        repo gpgcheck=1
        gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
    
9)Installare Kubernetes:
    sudo yum install -y kubectl kubeadm kubelet

10)Attivare il servizio kubelet:
    sudo systemctl enable --now kubelet
    (Potrebbe dare un errore il servizio finchè non si effettua la kubeadm init)

OPERAZIONI DA ESEGUIRE SOLO SUL MASTER:
11)Inizializzare il cluster:
    sudo kubeadm init --apiserver-advertise-address=172.16.16.100 --pod-network-cidr=10.244.0.0/16
    (segnarsi il token per aggiungere altri nodi. é possibile ritrovarlo successivamente.)

12)per poter utilizzare i comandi dell'interfaccia del cluster gli utenti hanno bisogno delle impostazioni di kubernetes all'interno della propria cartella /home:
    mkdir /home/user/.kube
    sudo cp /etc/kubernetes/admin.conf /home/user/.kube/conf
    sudo chown -R user:user /home/user/.kube/conf

NON ROOT!!!!!!:
13)Scaricare il file kube-flannel.yml:
    wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
    ATTENZIONE: se si utilizza vagrant modifacer il file come segue
        - cercare /flannld
        - sotto "args:" aggiungere indentato correttamente "- --iface=eth1"
    questo si fa perchè vagrant utilizza la iface eth0 per il collegamento sshcon le macchine.

14)se si è dimenticato il token per l'aggiunta di nuovi nodi al cluster:
    sudo kubeadm token create --print-join-command


SUGLI ALTRI NODI:
15)Aggiungere un nodo:
    eseguire il comando restituito come output nel passaggio 14 come root.

