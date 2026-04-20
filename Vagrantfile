Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/jammy64"

  #puerto de acceso
  config.vm.network "forwarded_port", guest: 8080, host: 8080

  # carpeta compartida
  config.vm.synced_folder ".", "/home/vagrant/proyecto"

  #primera vez que se ejecuta
  config.vm.provision "shell", run: "once", inline: <<-SHELL
    echo "---- actualizr sistema ----"
    apt-get update -y
    apt-get upgrade -y

    echo "---- descargando docker ----"
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

    echo "---- carpeta central ----"
    mkdir -p /home/vagrant/proyecto/app

    echo "---- arrancar docker ----"
    cd /home/vagrant/proyecto
    docker compose up -d || true
  SHELL

  #vagrant provision
  config.vm.provision "shell", inline: <<-SHELL

    cd /home/vagrant/proyecto
    docker compose run --rm git-updater || true

    docker compose up -d
  SHELL

 #esto hace falta para que arranque la maquina virtual sin que parpade la pantalla (bug)
  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--graphicscontroller", "vmsvga"]
    vb.customize ["modifyvm", :id, "--vram", "128"]
    vb.customize ["modifyvm", :id, "--accelerate3d", "off"]
  end

end
