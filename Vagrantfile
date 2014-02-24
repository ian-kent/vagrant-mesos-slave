# -*- mode: ruby -*-
# vi: set ft=ruby :

## vagrant plugins required:
# vagrant-berkshelf, vagrant-omnibus, vagrant-hosts, vagrant-cachier

Vagrant.configure("2") do |config|

  # enable plugins
  config.berkshelf.enabled = true
  config.omnibus.chef_version = :latest
  config.cache.auto_detect = true

  config.vm.provider :virtualbox do |vb, override|
    #override.vm.box = "Official Ubuntu 13.04 daily Cloud Image amd64 (No Guest Additions)"
    #override.vm.box_url = "http://cloud-images.ubuntu.com/vagrant/raring/current/raring-server-cloudimg-amd64-vagrant-disk1.box"

    override.vm.box = "Official Ubuntu Precise64 Cloud Image current"
    override.vm.box_url = "http://cloud-images.ubuntu.com/vagrant/precise/current/precise-server-cloudimg-amd64-vagrant-disk1.box"

    override.vm.hostname = ENV['VM_HOSTNAME']
    override.vm.network :public_network

    vb.name = 'vagrant-mesos-' + ENV['VM_HOSTNAME']
    vb.customize ["modifyvm", :id, "--memory", ENV['VM_MEM'] ]
  end

  config.vm.provision :chef_solo do |chef|
#   chef.log_level = :debug
    chef.add_recipe "apt"
    chef.add_recipe "mesos"

    chef.add_recipe "mesos::slave"
    chef.json = {
      :mesos => {
        :type         => "mesosphere",
        :version      => "0.16.0",
        :slave        => {
          :master       => "#{ENV['ZK_HOSTS']}",
        },
        :slave_ips    => ["localhost"]
      }
    }
  end

$script = <<SCRIPT
  wget https://dl.bintray.com/mitchellh/serf/0.4.1_linux_amd64.zip
  unzip 0.4.1_linux_amd64.zip
  mv serf /bin
  rm 0.4.1_linux_amd64.zip
SCRIPT
  config.vm.provision "shell", inline: $script
end
