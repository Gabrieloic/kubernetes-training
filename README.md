## kubernetes-training

```bash
Dans ce repo je mettrai toutes les informations ( installation, exercices, commandes ...etc) du cluster de lab sur kubernetes
```

### ********** installation du cluster ************ ###
```bash

# Les commandes

# on creera ca cluste avec un user centos qui aura les droits admin
    sudo -i
    useradd centos
    passwd centos
# on le rajoute dans le groupe admin wheel
    usermod -aG wheel centos 

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
    sudo wget https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
# sudo curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
    sudo chmod +x minikube-linux-amd64
    sudo mv minikube-linux-amd64 /usr/bin/minikube
# sudo curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.22.0/bin/linux/amd64/kubectl
# sudo curl -LO https://dl.k8s.io/release/v1.32.0/bin/linux/amd64/kubectl
    sudo curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
    sudo chmod +x kubectl
    sudo mv kubectl /usr/bin/
    sudo echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
    sudo systemctl enable docker.service
# on check la version installee afin de lancer la bonne avec la commande
    minikube version
# Puis on installe avec la commande
    minikube start --driver=none --kubernetes-version v1.32.0

# A cette etapes on risque avoir une erreur car depuis Kubernetes 1.32.0 il nécessite crictl pour fonctionner correctement.
# Pour résoudre ce problème, il faut installer crictl

# Telecharger la derniere version de crictl
    curl -LO https://github.com/kubernetes-sigs/cri-tools/releases/download/v1.22.0/crictl-v1.22.0-linux-amd64.tar.gz
# Extraire l'archive
    tar -zxvf crictl-v1.22.0-linux-amd64.tar.gz
# Déplacer les fichiers extraits dans un répertoire du PATH
    sudo mv crictl /usr/local/bin/
# Verifier si l'install est bien faite
    crictl --version

# Egalement on a besoin de cri-dockerd car depuis Kubernetes v1.24 et au-delà, le driver none de Minikube nécessite cri-dockerd pour fonctionner avec Docker comme runtime de conteneur.
# cri-dockerd permet à Kubernetes de communiquer avec Docker pour gérer les conteneurs.
    git clone https://github.com/Mirantis/cri-dockerd.git
    sudo yum install -y golang make gcc
    cd cri-dockerd
    make
    sudo make install

# Pour demarrer cridockerd on ouvre le fichier
    sudo vim /etc/systemd/system/cri-docker.service
# et on insere le contenu suivant

#############

    [Unit]
    Description=CRI interface for Docker Application Container Engine
    Documentation=https://github.com/Mirantis/cri-dockerd
    After=network.target
    
    [Service]
    ExecStart=/usr/local/bin/cri-dockerd
    Restart=always
    ExecStartPost=/usr/bin/sleep 0.5
    RestartSec=3
    LimitNOFILE=1048576
    
    [Install]
    WantedBy=multi-user.target

#########

# Ensuite on l'active et on lui permet de demarrer automatiquement au demarrage de la machine
    sudo systemctl enable cri-docker
    sudo systemctl start cri-docker
    sudo systemctl status cri-docker

# A ce moment on peut relancer la commande d'install
    minikube start --driver=none --kubernetes-version v1.32.0

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
