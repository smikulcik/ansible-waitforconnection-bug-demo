# -*- mode: ruby -*-
# vi: set ft=ruby :

$firewallscript = <<SCRIPT
sudo ufw default deny incoming
sudo ufw allow from 192.168.33.20
SCRIPT

Vagrant.configure(2) do |config|

  config.vm.define "bastion" do |bastion|
    bastion.vm.box = "ubuntu/trusty64"
    bastion.vm.network "private_network", ip: "192.168.33.20"
  end

  config.vm.define "hidden" do |hidden|
    hidden.vm.box = "ubuntu/trusty64"
    hidden.vm.network "private_network", ip: "192.168.33.21"
    hidden.vm.provision "shell",
      inline: <<-EOF
        sudo ufw default deny incoming
        sudo ufw allow from 192.168.33.20
        sudo ufw deny 22
        sudo ufw --force enable
EOF
  end
 
end
