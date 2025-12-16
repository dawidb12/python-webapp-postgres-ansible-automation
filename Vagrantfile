# Before you execute, check, if ssh keypair exists in Vagrantfile directory. If not, follow the procedure at: https://devops.stackexchange.com/a/1247

$script = <<-SCRIPT
#!/bin/bash
if [[ "$HOSTNAME" == "ansible-control" ]]; then
  sudo apt update
  sudo apt remove -y docker docker.io
  sudo apt install -y apt-transport-https ca-certificates curl gpg software-properties-common gnupg
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
  sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
  sudo apt update
  sudo apt install -y docker-ce
  sudo usermod -aG docker vagrant
  sudo chmod 666 /var/run/docker.sock
  sudo apt install -y python3-pip
  sudo -u vagrant pip3 install --user ansible molecule molecule-plugins[docker] docker --break-system-packages
fi
SCRIPT

Vagrant.configure("2") do |config|
    servers=[
        {
          :hostname => "ansible-control",
          :box => "bento/ubuntu-24.04",
          :ip => "192.168.60.110",
          :ram => 2048,
          :cpu => 2,
          :ssh_port => '2200'
        },
        {
          :hostname => "web",
          :box => "bento/ubuntu-24.04",
          :ip => "192.168.60.111",
          :ram => 1024,
          :cpu => 1,
          :ssh_port => '2201'
        },
        {
          :hostname => "app",
          :box => "bento/ubuntu-24.04",
          :ip => "192.168.60.112",
          :ram => 1024,
          :cpu => 1,
          :ssh_port => '2202'
        },
        {
          :hostname => "database",
          :box => "bento/ubuntu-24.04",
          :ip => "192.168.60.113",
          :ram => 2048,
          :cpu => 1,
          :ssh_port => '2203'
        },
        {
          :hostname => "monitoring",
          :box => "bento/ubuntu-24.04",
          :ip => "192.168.60.114",
          :ram => 1024,
          :cpu => 1,
          :ssh_port => '2204'
        },
      ]

    servers.each do |machine|
        config.vm.define machine[:hostname] do |node|
            node.vm.box = machine[:box]
            node.vm.hostname = machine[:hostname]
            node.vm.network :private_network, ip: machine[:ip]
            node.vm.network "forwarded_port", guest: 22, host: machine[:ssh_port], id: "ssh"
            node.vm.provider :virtualbox do |vb|
              vb.memory = machine[:ram]
              vb.cpus = machine[:cpu]
            end
            node.vm.provision "file", source: "id_rsa", destination: "/home/vagrant/.ssh/id_rsa"
            public_key = File.read("id_rsa.pub")
            node.vm.provision :shell, :inline =>"
              echo 'Copying ansible-vm public SSH Keys to the VM'
              mkdir -p /home/vagrant/.ssh
              chmod 700 /home/vagrant/.ssh
              echo '#{public_key}' >> /home/vagrant/.ssh/authorized_keys
              chmod -R 600 /home/vagrant/.ssh/authorized_keys
              echo 'Host 192.168.*.*' >> /home/vagrant/.ssh/config
              echo 'StrictHostKeyChecking no' >> /home/vagrant/.ssh/config
              echo 'UserKnownHostsFile /dev/null' >> /home/vagrant/.ssh/config
              chmod -R 600 /home/vagrant/.ssh/config
              chmod 600 /home/vagrant/.ssh/id_rsa
              ", privileged: false
            node.vm.provision "shell", inline: $script, privileged: false
        end
    end
end