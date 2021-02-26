# Manual-LAB
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
    


