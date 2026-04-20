Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/jammy64"

  # 🔥 acceso correcto a WordPress
  config.vm.network "forwarded_port", guest: 8080, host: 8080

  # carpeta compartida
  config.vm.synced_folder ".", "/home/vagrant/proyecto"

  # --------------------------
  # SETUP INICIAL
  # --------------------------
  config.vm.provision "shell", run: "once", inline: <<-SHELL
    echo "---- UPDATE SYSTEM ----"
    apt-get update -y
    apt-get upgrade -y

    echo "---- INSTALL DOCKER ----"
    apt-get install -y ca-certificates curl gnupg

    install -m 0755 -d /etc/apt/keyrings

    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg

    echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
    $(. /etc/os-release && echo $VERSION_CODENAME) stable" \
    > /etc/apt/sources.list.d/docker.list

    apt-get update -y
    apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

    systemctl enable docker
    systemctl start docker

    echo "---- PROJECT STRUCTURE ----"
    mkdir -p /home/vagrant/proyecto/themes

    echo "---- START DOCKER ----"
    cd /home/vagrant/proyecto
    docker compose up -d || true
  SHELL

  # --------------------------
  # UPDATE PROVISION
  # --------------------------
  config.vm.provision "shell", inline: <<-SHELL
    echo "---- UPDATE THEMES ----"

    cd /home/vagrant/proyecto

    docker compose run --rm git-updater || true

    docker compose up -d
  SHELL

  # --------------------------
  # VIRTUALBOX FIX
  # --------------------------
  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--graphicscontroller", "vmsvga"]
    vb.customize ["modifyvm", :id, "--vram", "128"]
    vb.customize ["modifyvm", :id, "--accelerate3d", "off"]
  end

end
