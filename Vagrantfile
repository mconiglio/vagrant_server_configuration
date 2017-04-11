# -*- mode: ruby -*-
# vi: set ft=ruby :

# Mauricio Coniglio

required_plugins = %w(vagrant-cachier)
required_plugins.each do |plugin|
  system "vagrant plugin install #{plugin}" unless Vagrant.has_plugin? plugin
end

VAGRANTFILE_API_VERSION = "2"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end

  # Provider
  (1..2).each do |n|
    config.vm.define "vm0#{n}" do |srv|
        srv.vm.box ="ubuntu/trusty64"
        srv.vm.hostname = "vm0#{n}"
        srv.vm.network "private_network", ip: "10.0.0.1#{n}"
    end
  end

  # Provision
  config.vm.provision "shell", inline: <<-SHELL
    echo "Vagrant provision at $(date +%F-%H.%M.%S)" > /tmp/created
    adduser --disabled-password -gecos "" ucu
    echo ucu:pass | chpasswd
    sudo su
    mkdir /home/ucu/.ssh
    cp /vagrant/files/ssh_config /home/ucu/.ssh/config
    if [ ! -f /vagrant/files/id_rsa ]; then ssh-keygen -t rsa -f /vagrant/files/id_rsa -N ''; fi
    cp /vagrant/files/id_rsa /home/ucu/.ssh/id_rsa
    cp /vagrant/files/id_rsa.pub /home/ucu/.ssh/id_rsa.pub
    cat /vagrant/files/id_rsa.pub >> /home/ucu/.ssh/authorized_keys
    chown -R ucu /home/ucu/.ssh
    chmod -R 700 /home/ucu/.ssh
    cp /vagrant/files/sshd_config /etc/ssh/sshd_config
    service ssh restart
  SHELL
end
