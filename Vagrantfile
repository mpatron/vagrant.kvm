# -*- mode: ruby -*-
# vi: set ft=ruby :

# set http_proxy=http://login:pass@192.168.20.150:8080
# set https_proxy=%http_proxy%
# vagrant box add ubuntu/focal64
# vagrant up --provision --provider virtualbox
# cd /vagrant && PYTHONUNBUFFERED=1 ANSIBLE_NOCOLOR=False ANSIBLE_CONFIG=ansible.cfg ansible-playbook --limit="all" --inventory-file=inventory --extra-vars=\{\"PROXY_ON\":false,\"PROXY_SERVER\":\"\"\} -v provision.yml

# On hoster, load :
# sudo apt install sshpass -y
# PYTHONUNBUFFERED=1 ANSIBLE_NOCOLOR=False ANSIBLE_CONFIG=ansible.cfg ansible-playbook --limit="all" --inventory-file=inventory --extra-vars=\{\"PROXY_ON\":false,\"PROXY_SERVER\":\"\"\} -v provision.yml

# ENV["LC_ALL"] = "fr_FR.UTF-8"
VM_COUNT = 6
VM_RAM = "4096" # 1024 2048 3072 4096 6144 8192
VM_CPU = 2
# VM
# IMAGE = "ubuntu/focal64" #20.04 LTS
# IMAGE = "generic/ubuntu2204"
# IMAGE = "generic/debian12"
IMAGE = "almalinux/9"
# LXC
# IMAGE = "hibox/focal64"
# IMAGE = "ubuntu/focal/cloud"

Vagrant.configure("2") do |config|
  
  # config.proxy.http     = "http://login:pass@192.168.20.150:8080"
  # config.proxy.https    = "http://login:pass@192.168.20.150:8080"
  # config.proxy.no_proxy = "localhost,127.0.0.1"
  puts "proxyconf..."
  if Vagrant.has_plugin?("vagrant-proxyconf")
    puts "find proxyconf plugin !"
    if ENV["http_proxy"]
      puts "http_proxy: " + ENV["http_proxy"]
      config.proxy.http = ENV["http_proxy"]
    end
    if ENV["https_proxy"]
      puts "https_proxy: " + ENV["https_proxy"]
      config.proxy.https = ENV["https_proxy"]
    end
    if ENV["no_proxy"]
      config.proxy.no_proxy = ENV["no_proxy"]
    end
  end

  config.vm.box = IMAGE
  config.vm.box_check_update = false
  config.vm.boot_timeout = 600 # default=300s
  config.vm.synced_folder ".", "/vagrant"
  # config.ssh.insert_key = false
  # config.vm.synced_folder ".", "/vagrant" #, type: "virtualbox"
  # config.vm.synced_folder ".", "/vagrant", type: "nfs", mount_options: ["vers=3,tcp"]
#  config.vm.synced_folder ".", "/vagrant",
#    type: "nfs",
#    nfs_version: 4,
#    nfs_udp: false
  config.vm.provider :virtualbox do |vb|
    vb.cpus = VM_CPU
    vb.nested = true
    vb.memory = VM_RAM
    vb.customize ['modifyvm', :id, '--cableconnected1', 'on']
    vb.customize ["modifyvm", :id, "--audio", "none"]
    vb.customize ['modifyvm', :id, '--graphicscontroller', 'vmsvga']
    vb.linked_clone = true
  end
  config.vm.provider :libvirt do |lbv|
    lbv.cpus = VM_CPU
    lbv.nested = true
    lbv.memory = VM_RAM
    lbv.storage :file, :type => 'qcow2', :size => "20G"
  end

  (0..(VM_COUNT-1)).each do |i|
    config.vm.define "node#{i}" do |node|
      node.vm.hostname = "node#{i}.jobjects.net"
      node.vm.network "private_network", ip: "192.168.56.14#{i}"
      node.vm.provision "shell", run: "always", inline: <<-SHELL1
for N in {0..#{VM_COUNT}}; do
  [[ ! $(grep "192.168.56.14${N} node${N}.jobjects.net node${N}" /etc/hosts) ]] && echo $(echo "192.168.56.14${N} node${N}.jobjects.net node${N}" | sudo tee -a /etc/hosts)
done
sudo sed -i '/127.0.1.1/d' /etc/hosts
sudo hostnamectl set-hostname node#{i}.jobjects.net
sudo sed -i -e "\\#PasswordAuthentication no# s#PasswordAuthentication no#PasswordAuthentication yes#g" /etc/ssh/sshd_config
sudo systemctl restart sshd
# sudo apt-get update -y && sudo apt-get install sshpass -y
sudo dnf update -y && dnf install sshpass bash-completion -y 
# =============================================================================
# Ajout certificat ssh pour vagrant
bash -c 'cat << EOF > /home/vagrant/.ssh/id_ed25519
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW
QyNTUxOQAAACDi8uu07CqVhPz1mO7Brddi/zofsEpn6bsf0Jh3S9ffMAAAAJCou9OoqLvT
qAAAAAtzc2gtZWQyNTUxOQAAACDi8uu07CqVhPz1mO7Brddi/zofsEpn6bsf0Jh3S9ffMA
AAAEBytj//ZeYFeYIBVUUhsT76YZdSm/2vC3uW/v6n2rp65+Ly67TsKpWE/PWY7sGt12L/
Oh+wSmfpux/QmHdL198wAAAADXZhZ3JhbnRAbm9kZTA=
-----END OPENSSH PRIVATE KEY-----
EOF'
bash -c 'cat << EOF > /home/vagrant/.ssh/id_ed25519.pub
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOLy67TsKpWE/PWY7sGt12L/Oh+wSmfpux/QmHdL198w vagrant@node0
EOF'
grep -q --no-messages "AAaAAC3NzaC1lZDI1NTE5AAAAIOLy67TsKpWE" /home/vagrant/.ssh/authorized_keys && echo "Deja present dans /home/vagrant/.ssh/authorized_keys" || bash -c 'echo "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOLy67TsKpWE/PWY7sGt12L/Oh+wSmfpux/QmHdL198w vagrant@node0" >> /home/vagrant/.ssh/authorized_keys'
sudo chmod 600 /home/vagrant/.ssh/id_ed25519
sudo chmod 600 /home/vagrant/.ssh/id_ed25519.pub
sudo chmod 600 /home/vagrant/.ssh/authorized_keys
for N in {0..#{VM_COUNT}}; do
  if [[ ! -f /home/vagrant/.ssh/config ]]; then
    sudo touch /home/vagrant/.ssh/config
    sudo chmod 600 /home/vagrant/.ssh/config
    sudo chown vagrant:vagrant /home/vagrant/.ssh/config
  fi
  if [[ ! $(grep "Host node${N}" /home/vagrant/.ssh/config) ]]; then
    echo "Host node${N}" | sudo tee -a /home/vagrant/.ssh/config
    echo "    Hostname 192.168.56.14${N}" | sudo tee -a /home/vagrant/.ssh/config
    echo "    StrictHostKeyChecking no" | sudo tee -a /home/vagrant/.ssh/config
  fi
done
sudo chmod 600 /home/vagrant/.ssh/config
sudo chown vagrant:vagrant -R /home/vagrant/.ssh
bash -c 'cat << EOF > /home/vagrant/maj.sh
#!/bin/bash
echo "=== Maj OS ==="
sudo apt autoclean -yqq && sudo apt update -yqq && sudo apt upgrade -yqq && sudo apt autoremove --purge -yqq
echo "=== OS need to restart ? ==="
if [ -f /var/run/reboot-required ]; then
  echo "Reboot required"
else
  echo "No reboot need"
fi
EOF'
sudo chmod +x /home/vagrant/maj.sh
# /home/vagrant/maj.sh
SHELL1
    end
  end

end

