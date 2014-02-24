Ubuntu Mesos Slave with Serf
============================

Vagrant build for Ubuntu with Serf

Requires Vagrant v2 - http://www.vagrantup.com/

Also requires Vagrant plugins:
  - vagrant-berkshelf
  - vagrant-omnibus
  - vagrant-hosts
  - vagrant-cachier

Installs:
  - Apache Mesos
  - Serf

The following environment variables are required for every vagrant command:

  - VM_HOSTNAME - the hostname to configure the VM with
  - VM_MEM - VM ram (in mb)
  - ZK_HOSTS - a list of Apache ZooKeeper urls
  - MESOS_MASTER - hostname or IP of a Mesos master, used as Serf first neighbour

Example:

    VM_HOSTNAME="slave1x" VM_MEM="1024" ZK_HOSTS="zk://zk1:2181, zk://zk2:2181" MESOS_MASTER="master1" vagrant up