Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  config.vm.box = "hashicorp/precise64"

  # Sync protected database dump
  # config.vm.provision "file", source:'./data.pgdump', destination:'/vagrant/data.pgdump'

  # Use local ssh data
  config.ssh.forward_agent = true  

  # Set locale to en_US.UTF-8
  config.vm.provision "shell", inline: $script_locale

  # Install build environment
  config.vm.provision "shell", inline: $script_install, privileged: false

  config.vm.provider :parallels do |p, o|
    o.vm.box = "parallels/ubuntu-12.04"
  end 
end

$script_locale = <<SCRIPT
  echo "Setting locale to en_US.UTF-8..."
  locale-gen en_US.UTF-8
  update-locale LANG=en_US.UTF-8 LC_ALL=en_US.UTF-8
SCRIPT

$script_install = <<SCRIPT
set -o nounset -o errexit -o pipefail -o errtrace

# Send vagrant ssh to the correct folder
echo "cd /vagrant" >> /home/vagrant/.bashrc

# Config SSH for faster login
if ! grep "UseDNS no" /etc/ssh/sshd_config > /dev/null; then
  echo "UseDNS no" | sudo tee -a /etc/ssh/sshd_config > /dev/null
  sudo service ssh restart
fi

export DEBIAN_FRONTEND=noninteractive

echo "Adding apt repoisitories and updating..."
sudo apt-get update
sudo apt-get install -y python-software-properties software-properties-common apt-transport-https
sudo apt-get update
sudo apt-get install -y postgresql-9.1 postgresql-client-9.1 postgresql-contrib-9.1
sudo apt-get update
sudo apt-get install python-dev python-pip -q -y
pip install --upgrade python
sudo -u postgres createuser vagrant --superuser
sudo -u postgres createdb vagrant
cd /vagrant

SCRIPT
