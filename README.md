Apache Mesos Slave with Serf
============================

Vagrant build for Ubuntu with Apache Mesos and Serf

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
  - SERF_NODE - a Serf node to connect to

Example:

    VM_HOSTNAME="slave1" VM_MEM="1024" ZK_HOSTS="zk://zk1:2181/mesos, zk://zk2:2181/mesos" SERF_NODE="zk1" vagrant up