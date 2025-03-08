## kubernetes-training

```bash
Dans ce repo je mettrai toutes les informations ( installation, exercices, commandes ...etc) du cluster de lab sur kubernetes
```

### ********** installation du cluster ************ ###
```bash

# Les commandes

  sudo yum -y update
  sudo yum -y install epel-release
  sudo yum -y install git libvirt qemu-kvm virt-install virt-top libguestfs-tools bridge-utils
  sudo yum install socat -y
  sudo yum install -y conntrack
  sudo curl -fsSL https://get.docker.com -o get-docker.sh
  sudo sh get-docker.sh
  sudo usermod -aG docker centos
  sudo systemctl start docker
  sudo yum -y install wget
# sudo wget https://storage.googleapis.com/minikube/releases/v1.11.0/minikube-linux-amd64
  sudo curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
  sudo chmod +x minikube-linux-amd64
  sudo mv minikube-linux-amd64 /usr/bin/minikube
# sudo curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.22.0/bin/linux/amd64/kubectl
  sudo curl -LO https://dl.k8s.io/release/v1.32.0/bin/linux/amd64/kubectl
  
  sudo chmod +x kubectl
  sudo mv kubectl /usr/bin/
  sudo echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
  sudo systemctl enable docker.service
  minikube start --driver=none --kubernetes-version v1.22.0

# Pour l'autocomplétion kubectl :
  echo 'source <(kubectl completion bash)' >> ${HOME}/.bashrc && source ${HOME}/.bashrc


```

### ********** Suppression du cluster ************ ###

```bash

# Si vous souhaitez supprimer votre cluster en faisant un roll  back; voici les commandes dans le bon ordre

# Annuler la mise à jour des paquets système
    sudo yum history undo last
# Désinstaller epel-release
    sudo yum remove epel-release
# Désinstaller tous les paquets installés avec yum
    sudo yum remove git libvirt qemu-kvm virt-install virt-top libguestfs-tools bridge-utils
# Désinstaller socat et conntrack
    sudo yum remove socat conntrack
# Désinstaller Docker
    sudo yum remove docker-ce docker-ce-cli containerd.io
# Désactiver et arrêter le service Docker
    sudo systemctl stop docker
    sudo systemctl disable docker
# Supprimer l'utilisateur Docker du groupe docker
    sudo gpasswd -d centos docker
# Désinstaller Minikube
    sudo rm /usr/bin/minikube
# Désinstaller kubectl
    sudo rm /usr/bin/kubectl
# Réinitialiser le paramètre bridge-nf-call-iptables
    echo '0' | sudo tee /proc/sys/net/bridge/bridge-nf-call-iptables
  # ou alors le faire directement dans le fichier /etc/sysctl.conf ou en cli avec la commande
    sudo sysctl -w net.bridge.bridge-nf-call-iptables=0
# Désactiver Docker au démarrage
    sudo systemctl disable docker.service
# Arrêter le cluster Minikube
    sudo minikube stop
    sudo minikube delete
# Supprimer les fichiers restants
    rm 't.localdomain dockerd[2' get-docker.sh anaconda-ks.cfg 
# Supprimer le dossier .minikube
    rm -rf .minikube/


```
