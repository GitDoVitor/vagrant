# -*- mode: ruby -*-
# vi: set ft=ruby :

BOX_NAME = "ubuntu/focal64"
MEMORY = "512"
CPUS = 1
MANAGERS = 1 
MANAGER_IP = "172.20.20.1"
WORKERS = 2
WORKER_IP = "172.20.20.10"
VAGRANTFILE_API_VERSION = "2"


$install_docker = <<-SCRIPT
echo "Installing dependencies ..."
sudo apt-get update
echo "Adding Docker’s official GPG key ..."
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "Setting up the stable repository ..."
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
echo "Installing dependencies and installing docker ..."
sudo apt-get update && sudo apt-get install docker-ce docker-ce-cli containerd.io -y
SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    #Common setup
    config.vm.box = BOX_NAME
    config.vm.synced_folder ".", "/vagrant"
    config.vm.provision "shell", inline: $install_docker, privileged: true
    config.vm.provider "virtualbox" do |vb|
      vb.memory = MEMORY
      vb.cpus = CPUS
    end
    #Setup Manager Nodes
    (1..MANAGERS).each do |i|
        config.vm.define "manager0#{i}" do |manager|
          manager.vm.network :private_network, ip: "#{MANAGER_IP}#{i}"
          manager.vm.hostname = "manager0#{i}"
          if i == 1
            #Only configure port to host for Manager01
            manager.vm.network :forwarded_port, guest: 8080, host: 8080
            manager.vm.network :forwarded_port, guest: 5000, host: 5000
            manager.vm.network :forwarded_port, guest: 9000, host: 9000
          end
        end
    end
    #Setup Woker Nodes
    (1..WORKERS).each do |i|
        config.vm.define "worker0#{i}" do |worker|
            worker.vm.network :private_network, ip: "#{WORKER_IP}#{i}"
            worker.vm.hostname = "worker0#{i}"
        end
    end
end
