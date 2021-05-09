# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    config.vm.define "swarm1" do |swarm1|
        swarm1.vm.hostname = "swarm1.example.com"
        swarm1.vm.box = "generic/centos7"
        swarm1.vm.network "private_network", ip: "10.10.10.21"
        swarm1.vm.synced_folder ".", "/vagrant", type: "rsync"
        swarm1.vm.provision "shell", inline: <<-SHELL
            #Install docker
            sudo yum install -y yum-utils
            sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
            sudo yum install docker-ce docker-ce-cli containerd.io -y
            sudo systemctl --now enable docker
            sudo docker run hello-world
            # Setup DNS client with vagranting-dns
            ETH0=$(sudo nmcli connection show | grep eth0 | cut -d ' ' -f 4)
            ETH1=$(sudo nmcli connection show | grep eth1 | cut -d ' ' -f 4)
            sudo nmcli connection modify $ETH1 ipv4.dns 10.10.10.2
            sudo nmcli connection modify $ETH1 ipv4.dns-search example.com
            sudo nmcli connection modify $ETH1 ipv4.dns-priority 1
            sudo nmcli connection modify $ETH0 ipv4.dns-priority 10
            sudo nmcli connection up $ETH1
            sudo nmcli connection up $ETH0
            # Swarm init/join script
            managerIP="10.10.10.21"
            hostIP=$(hostname -I | grep 10.10.10.2. --only-matching)
                sudo firewall-cmd --permanent --add-port=2376/tcp
                sudo firewall-cmd --permanent --add-port=2377/tcp
                sudo firewall-cmd --permanent --add-port=7946/tcp --add-port=7946/udp
                sudo firewall-cmd --permanent --add-port=4789/udp
                sudo firewall-cmd --reload

            
             if [ $managerIP == $hostIP ]; then
                sudo docker swarm init --advertise-addr 10.10.10.21   
                sudo docker swarm join-token manager -q > /tmp/index.html
                sudo firewall-cmd --permanent --add-port=80/tcp
                sudo firewall-cmd --reload 
                sudo docker run -d -p 80:80 --name  keyhttpd httpd
                sudo docker cp /tmp/index.html keyhttpd:/usr/local/apache2/htdocs

            else 
                managerKey=$(curl -s http://10.10.10.21:80/index.html)
                sudo docker swarm join --token $managerKey 10.10.10.21:2377
            fi

        SHELL
        swarm1.vm.provider "virtualbox" do |vb|
            vb.cpus = "1"
            vb.memory = "1024"
            vb.customize ["modifyvm", :id, "--groups", "/vagranting"] #If you have problems with folder exist error -> Comment out this line
            vb.name = "swarm1"
        end
    end
    config.vm.define "swarm2" do |swarm2|
        swarm2.vm.hostname = "swarm2.example.com"
        swarm2.vm.box = "generic/centos7"
        swarm2.vm.network "private_network", ip: "10.10.10.22"
        swarm2.vm.synced_folder ".", "/vagrnat", type: "rsync"
        swarm2.vm.provision "shell", inline: <<-SHELL
            sudo yum install -y yum-utils
            sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
            sudo yum install docker-ce docker-ce-cli containerd.io -y
            sudo systemctl --now enable docker
            sudo docker run hello-world
             # Setup DNS client with vagranting-dns
             ETH0=$(sudo nmcli connection show | grep eth0 | cut -d ' ' -f 4)
             ETH1=$(sudo nmcli connection show | grep eth1 | cut -d ' ' -f 4)
             sudo nmcli connection modify $ETH1 ipv4.dns 10.10.10.2
             sudo nmcli connection modify $ETH1 ipv4.dns-search example.com
             sudo nmcli connection modify $ETH1 ipv4.dns-priority 1
             sudo nmcli connection modify $ETH0 ipv4.dns-priority 10
             sudo nmcli connection up $ETH1
             sudo nmcli connection up $ETH0
            # Swarm init/join script
            managerIP="10.10.10.21"
            hostIP=$(hostname -I | grep 10.10.10.2. --only-matching)
                sudo firewall-cmd --permanent --add-port=2376/tcp
                sudo firewall-cmd --permanent --add-port=2377/tcp
                sudo firewall-cmd --permanent --add-port=7946/tcp --add-port=7946/udp
                sudo firewall-cmd --permanent --add-port=4789/udp
                sudo firewall-cmd --reload

            
             if [ $managerIP == $hostIP ]; then
                sudo docker swarm init --advertise-addr 10.10.10.21   
                sudo docker swarm join-token manager -q > /tmp/index.html
                sudo firewall-cmd --permanent --add-port=80/tcp
                sudo firewall-cmd --reload 
                sudo docker run -d -p 80:80 --name  keyhttpd httpd
                sudo docker cp /tmp/index.html keyhttpd:/usr/local/apache2/htdocs

            else 
                managerKey=$(curl -s http://10.10.10.21:80/index.html)
                sudo docker swarm join --token $managerKey 10.10.10.21:2377
            fi
        SHELL
        swarm2.vm.provider "virutalbox" do |vb|
                vb.cups = "1"
                vb.memory = "1024"
                vb.custome ["modifyvm", :id, "--groups", "/vagranting"] #If you have problems with folder exist error -> Comment out this line
                vb.name = "swarm2"
        end
    end
end

