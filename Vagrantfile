Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"

  config.vm.provision "shell", inline: <<-SHELL
    echo "haciendo update y upgrade"
    
    apt-get update -y
    apt-get upgrade -y
    
    echo "------------descargando los certificados del git-----------------"
    
    apt-get install -y ca-certificates curl gnupg lsb-release git
    
    install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    chmod a+r /etc/apt/keyrings/docker.gpg

    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo $VERSION_CODENAME) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null

    echo "----------descargando docker--------------"
    
    apt-get update -y
    apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

    systemctl enable docker
    systemctl start docker

    git clone https://github.com/antonio676767/projectocarlosdocker.git /projectocarlosdocker
    
    cd /projectocarlosdocker
    tar -xf ./twentytwentyfive.tar
    sudo chmod 777 /projectocarlosdocker/twentytwentyfive
    docker compose up -d
    docker cp /projectocarlosdocker/twentytwentyfive wordpress_app:/var/www/html/wp-content/themes/
    
  SHELL

  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--graphicscontroller", "vmsvga"]
    vb.customize ["modifyvm", :id, "--vram", "128"]
    vb.customize ["modifyvm", :id, "--accelerate3d", "off"]
  end
end
