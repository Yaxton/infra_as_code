Vagrant.configure("2") do |config|
  config.vm.box = "generic/ubuntu2204"

 # Configuration réseau
  config.vm.network "private_network", type: "dhcp"
  
  # Configuration mémoire et CPU
  config.vm.provider "virtualbox" do |vb|
    vb.name = "gitlab.local"
    vb.memory = "4096"
    vb.cpus = 2
  end

  # Provisionnement avec un script bash
  config.vm.provision "shell", inline: <<-SHELL
    # Mise à jour des paquets
    sudo apt-get update
    sudo apt-get upgrade -y

    # Installation des dépendances nécessaires
    sudo apt-get install -y curl openssh-server ca-certificates tzdata perl git
   


    # Ajout du dépôt GitLab
    curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh | sudo bash

    # Configuration de l'adresse IP pour GitLab (remplacez par l'IP de la VM si nécessaire)
    IP=$(hostname -I | awk '{print $2}')
    EXTERNAL_URL="http://$IP"

    # Installation de Docker pour le support des conteneurs
    sudo apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo add-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    sudo apt update
    sudo apt install -y docker

    # Installation de GitLab avec Container Registry activé
    sudo EXTERNAL_URL="$EXTERNAL_URL" apt install -y gitlab-ee

    git clone https://github.com/IkariTyux/atelier /etc/gitlab
    mv /etc/gitlab/atelier/files/* /etc/gitlab

    # Démarrer et reconfigurer GitLab
    sudo gitlab-ctl reconfigure

    # Vérification de l'installation
    sudo gitlab-ctl status
    sudo docker --version

    echo "GitLab accessible à: http://$IP"
    echo "GitLab Container Registry accessible à: http://$IP:5050"
  SHELL
end