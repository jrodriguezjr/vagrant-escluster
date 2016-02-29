# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Create Mesos Master VM
  config.vm.define "master" do |master|

    master.vm.box = "geerlingguy/centos7"
    master.vm.hostname = "master"
    master.vm.network :private_network, ip: "192.168.101.2"

    # Create a forwarded port mapping which allows access to a specific port
    # within the machine from a port on the host machine. In the example below,
    # accessing "localhost:8080" will access port 80 on the guest machine.
    # master.vm.network "forwarded_port", guest: 8080, host: 8080
    # master.vm.network "forwarded_port", guest: 5050, host: 5050

    master.vm.provider :virtualbox do |vb|
      # Use VBoxManage to customize the VM. For example to change memory:
      vb.customize ["modifyvm", :id, "--memory", "1024", "--cpus", "2"]
      vb.gui = false
    end

    # Enable provisioning with a shell script. Additional provisioners such as
    # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
    # documentation for more information about their specific syntax and use.
  master.vm.provision "shell", inline: <<-SHELL

     # install epel
     sudo yum -y install epel-release

     # install tools
     sudo yum -y install net-tools moreutils wget jq

     sudo yum install -y python-pip
     sudo pip install httpie

     #  install java
     wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u73-b02/jdk-8u73-linux-x64.rpm"
     sudo yum -y localinstall jdk-8u73-linux-x64.rpm
     rm -rf /home/vagrant/jdk-8u73-linux-x64.rpm

    #  install elasticsearch
    sudo rpm --import http://packages.elastic.co/GPG-KEY-elasticsearch

sudo echo '[elasticsearch-2.x]
name=Elasticsearch repository for 2.x packages
baseurl=http://packages.elastic.co/elasticsearch/2.x/centos
gpgcheck=1
gpgkey=http://packages.elastic.co/GPG-KEY-elasticsearch
enabled=1
' | sudo tee /etc/yum.repos.d/elasticsearch.repo

    sudo yum -y install elasticsearch

    sudo echo -e "127.0.0.1\tlocalhost\n::1\tlocalhost\n" > /etc/hosts
    sudo echo -e "192.168.101.2\tmaster\n192.168.101.3\tdata1\n192.168.101.4\tdata2" >> /etc/hosts

    # config master server
    sudo sed -i 's/# cluster.name: my-application/cluster.name: production/' /etc/elasticsearch/elasticsearch.yml
    sudo sed -i 's/# node.name: node-1/node.name: \$\{HOSTNAME\}/' /etc/elasticsearch/elasticsearch.yml
    sudo sed -i 's/# network.host: 192.168.0.1/network.host: \[_enp0s8_, _local_\]/' /etc/elasticsearch/elasticsearch.yml
    sudo sed -i "s/# discovery.zen.ping.unicast.hosts: \[\"host1\", \"host2\"\]/discovery.zen.ping.unicast.hosts: \[\"master\", \"data1\", \"data2\"\]/" /etc/elasticsearch/elasticsearch.yml

    sudo systemctl start elasticsearch
    sudo systemctl enable elasticsearch

   SHELL
  end

  # Create Mesos Node1 VM
  config.vm.define "data1" do |data1|

    data1.vm.box = "geerlingguy/centos7"
    data1.vm.hostname = "data1"
    data1.vm.network :private_network, ip: "192.168.101.3"

    data1.vm.provider :virtualbox do |vb|
      # Use VBoxManage to customize the VM. For example to change memory:
      vb.customize ["modifyvm", :id, "--memory", "1024", "--cpus", "2"]
      vb.gui = false
    end

    # Enable provisioning with a shell script. Additional provisioners such as
    # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
    # documentation for more information about their specific syntax and use.
  data1.vm.provision "shell", inline: <<-SHELL

       # install epel
       sudo yum -y install epel-release

       # install tools
       sudo yum -y install net-tools moreutils wget

       #  install java
       wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u73-b02/jdk-8u73-linux-x64.rpm"
       sudo yum -y localinstall jdk-8u73-linux-x64.rpm
       rm -rf /home/vagrant/jdk-8u73-linux-x64.rpm

      #  install elasticsearch
      sudo rpm --import http://packages.elastic.co/GPG-KEY-elasticsearch
sudo echo '[elasticsearch-2.x]
name=Elasticsearch repository for 2.x packages
baseurl=http://packages.elastic.co/elasticsearch/2.x/centos
gpgcheck=1
gpgkey=http://packages.elastic.co/GPG-KEY-elasticsearch
enabled=1
' | sudo tee /etc/yum.repos.d/elasticsearch.repo

      sudo yum -y install elasticsearch

      sudo echo -e "127.0.0.1\tlocalhost\n::1\tlocalhost\n" > /etc/hosts
      sudo echo -e "192.168.101.2\tmaster\n192.168.101.3\tdata1\n192.168.101.4\tdata2" >> /etc/hosts

      # config master server
      sudo sed -i 's/# cluster.name: my-application/cluster.name: production/' /etc/elasticsearch/elasticsearch.yml
      sudo sed -i 's/# node.name: node-1/node.name: \$\{HOSTNAME\}/' /etc/elasticsearch/elasticsearch.yml
      sudo sed -i 's/# network.host: 192.168.0.1/network.host: \[_enp0s8_, _local_\]/' /etc/elasticsearch/elasticsearch.yml
      sudo sed -i "s/# discovery.zen.ping.unicast.hosts: \[\"host1\", \"host2\"\]/discovery.zen.ping.unicast.hosts: \[\"master\", \"data1\", \"data2\"\]/" /etc/elasticsearch/elasticsearch.yml

      sudo systemctl start elasticsearch
      sudo systemctl enable elasticsearch
   SHELL
  end

  # Create Mesos Node2 VM
  config.vm.define "data2" do |data2|

    data2.vm.box = "geerlingguy/centos7"
    data2.vm.hostname = "data2"
    data2.vm.network :private_network, ip: "192.168.101.4"

    data2.vm.provider :virtualbox do |vb|
      # Use VBoxManage to customize the VM. For example to change memory:
      vb.customize ["modifyvm", :id, "--memory", "1024", "--cpus", "1"]
      vb.gui = false
    end

    # Enable provisioning with a shell script. Additional provisioners such as
    # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
    # documentation for more information about their specific syntax and use.
   data2.vm.provision "shell", inline: <<-SHELL
       # install epel
       sudo yum -y install epel-release

       # install tools
       sudo yum -y install net-tools moreutils wget

       #  install java
       wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u73-b02/jdk-8u73-linux-x64.rpm"
       sudo yum -y localinstall jdk-8u73-linux-x64.rpm
       rm -rf ~/jdk-8u73-linux-x64.rpm

      #  install elasticsearch
      sudo rpm --import http://packages.elastic.co/GPG-KEY-elasticsearch
sudo echo '[elasticsearch-2.x]
name=Elasticsearch repository for 2.x packages
baseurl=http://packages.elastic.co/elasticsearch/2.x/centos
gpgcheck=1
gpgkey=http://packages.elastic.co/GPG-KEY-elasticsearch
enabled=1
' | sudo tee /etc/yum.repos.d/elasticsearch.repo

      sudo yum -y install elasticsearch

      sudo echo -e "127.0.0.1\tlocalhost\n::1\tlocalhost\n" > /etc/hosts
      sudo echo -e "192.168.101.2\tmaster\n192.168.101.3\tdata1\n192.168.101.4\tdata2" >> /etc/hosts

      # config master server
      sudo sed -i 's/# cluster.name: my-application/cluster.name: production/' /etc/elasticsearch/elasticsearch.yml
      sudo sed -i 's/# node.name: node-1/node.name: \$\{HOSTNAME\}/' /etc/elasticsearch/elasticsearch.yml
      sudo sed -i 's/# network.host: 192.168.0.1/network.host: \[_enp0s8_, _local_\]/' /etc/elasticsearch/elasticsearch.yml
      sudo sed -i "s/# discovery.zen.ping.unicast.hosts: \[\"host1\", \"host2\"\]/discovery.zen.ping.unicast.hosts: \[\"master\", \"data1\", \"data2\"\]/" /etc/elasticsearch/elasticsearch.yml
      sudo systemctl start elasticsearch
      sudo systemctl enable elasticsearch
    SHELL
  end
#
end
