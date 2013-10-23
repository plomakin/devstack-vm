# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.define :controller do |cont|
    cont.vm.box = "raring64"
    cont.vm.box_url = "http://cloud-images.ubuntu.com/raring/current/raring-server-cloudimg-vagrant-amd64-disk1.box"
    # eth1, this will be the endpoint
    cont.vm.network :private_network, ip: "192.168.28.100"
    # eth2, this will be the OpenStack "public" network, use DevStack default
    cont.vm.network :private_network, ip: "172.28.4.225", :netmask => "255.255.255.224", :auto_config => false
    cont.vm.network "forwarded_port", guest: 22, host: 2301, host_ip: '0.0.0.0'
    cont.vm.network "forwarded_port", guest: 5000, host: 5000, host_ip: '0.0.0.0'
    cont.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: '0.0.0.0'
    cont.vm.network "forwarded_port", guest: 35357, host: 35357, host_ip: '0.0.0.0'
    cont.vm.provider :virtualbox do |vb|
        vb.customize ["modifyvm", :id, "--memory", 4096]
       	# eth2 must be in promiscuous mode for floating IPs to be accessible
       	vb.customize ["modifyvm", :id, "--nicpromisc3", "allow-all"]
        vb.customize ["modifyvm", :id, "--cpus", "1"]
    end
    cont.vm.provision :ansible do |ansible|
        ansible.host_key_checking = false
        ansible.playbook = "devstack_cont.yaml"
        ansible.verbose = "vv"
    end
    cont.vm.provision :shell, :inline => "cd devstack; sudo -u vagrant env HOME=/home/vagrant ./stack.sh"
    cont.vm.provision :shell, :inline => "ovs-vsctl add-port br-ex eth2"
  end

  config.vm.define :compute1 do |comp1|
    comp1.vm.box = "raring64"
    comp1.vm.box_url = "http://cloud-images.ubuntu.com/raring/current/raring-server-cloudimg-vagrant-amd64-disk1.box"
    comp1.vm.network :private_network, ip: "192.168.28.101"
    comp1.vm.network :private_network, ip: "172.28.4.225", :netmask => "255.255.255.224", :auto_config => false
    comp1.vm.network "forwarded_port", guest: 22, host: 2302, host_ip: '0.0.0.0'
    comp1.vm.provider :virtualbox do |vb|
        vb.customize ["modifyvm", :id, "--memory", 2048]
       	vb.customize ["modifyvm", :id, "--nicpromisc3", "allow-all"]
    end
    comp1.vm.provision :ansible do |ansible|
        ansible.host_key_checking = false
        ansible.playbook = "devstack_comp1.yaml"
        ansible.verbose = "vv"
    end
    comp1.vm.provision :shell, :inline => "cd devstack; sudo -u vagrant env HOME=/home/vagrant ./stack.sh"
    comp1.vm.provision :shell, :inline => "ovs-vsctl add-port br-ex eth2"
  end

  config.vm.define :compute2 do |comp2|
    comp2.vm.box = "raring64"
    comp2.vm.box_url = "http://cloud-images.ubuntu.com/raring/current/raring-server-cloudimg-vagrant-amd64-disk1.box"
    comp2.vm.network :private_network, ip: "192.168.28.102"
    comp2.vm.network :private_network, ip: "172.28.4.225", :netmask => "255.255.255.224", :auto_config => false
    comp2.vm.network "forwarded_port", guest: 22, host: 2303, host_ip: '0.0.0.0'
    comp2.vm.provider :virtualbox do |vb|
        vb.customize ["modifyvm", :id, "--memory", 2048]
       	vb.customize ["modifyvm", :id, "--nicpromisc3", "allow-all"]
    end
    comp2.vm.provision :ansible do |ansible|
        ansible.host_key_checking = false
        ansible.playbook = "devstack_comp2.yaml"
        ansible.verbose = "vv"
    end
    config.vm.provision :shell, :inline => "cd devstack; sudo -u vagrant env HOME=/home/vagrant ./stack.sh"
    config.vm.provision :shell, :inline => "ovs-vsctl add-port br-ex eth2"
end
