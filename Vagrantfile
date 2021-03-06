# -*- mode: ruby -*-
# vi: set ft=ruby :

## vagrant plugins required:
# vagrant-berkshelf, vagrant-omnibus, vagrant-hosts, vagrant-cachier

# https://github.com/everpeace/cookbook-mesos/blob/master/example/mesosphere/Vagrantfile

# Set VM_HOSTNAME, VM_MEM, ZK_HOSTS and SERF_JOIN
# Required even to run ssh/destroy
# E.g.
# VM_HOSTNAME="slave1" VM_MEM="1024" ZK_HOSTS="zk://zk1:2181/mesos, zk://zk2:2181/mesos" SERF_JOIN="zk1" vagrant up

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
  ip=`ip addr list eth1 | grep "inet " | cut -d ' ' -f6 | cut -d/ -f1`
  echo $ip | tee /etc/mesos-slave/ip
  exec start mesos-slave
SCRIPT
  config.vm.provision "shell", inline: $script

$script = <<SCRIPT
  wget https://dl.bintray.com/mitchellh/serf/0.4.1_linux_amd64.zip
  unzip 0.4.1_linux_amd64.zip
  mv serf /usr/local/bin
  rm 0.4.1_linux_amd64.zip
  wget https://raw.github.com/hashicorp/serf/c15b5f15dec3d735dae1a6d1f3455d4d7b5685e7/ops-misc/upstart.conf
  mv upstart.conf /etc/init/serf-agent.conf
  mkdir /etc/serf
  ip=`ip addr list eth1 | grep "inet " | cut -d ' ' -f6 | cut -d/ -f1`
  echo { \\"start_join\\": [\\"$1\\"], \\"protocol\\": 3, \\"tags\\": { \\"role\\": \\"slave\\" }, \\"advertise\\": \\"$ip\\" } | tee /etc/serf/config.json
  exec start serf-agent
SCRIPT
  config.vm.provision "shell", inline: $script, args: "#{ENV['SERF_JOIN']}"
end
