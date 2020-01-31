
$addhosts = <<-SCRIPT
sudo echo "192.168.200.100 control.example.com control" >> /etc/hosts
sudo echo "192.168.200.101 ansible1.example.com ansible1" >> /etc/hosts
sudo echo "192.168.200.102 ansible2.example.com ansible2" >> /etc/hosts
SCRIPT

$edit_sshd = <<-SCRIPT
sudo echo "ansible ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/ansible
sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config;
sudo systemctl restart sshd;
SCRIPT

Vagrant.configure("2") do |config|
  #config.ssh.insert_key = false
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.define "control" do |control|
    control.vm.box = "generic/centos8"
	control.vm.hostname = "control.example.com"
	control.vm.network "forwarded_port", guest: 22, host: 2223, id: "ssh"
	control.vm.network "private_network", ip: "192.168.200.100", :device => "eth2", :adapter => 2, :netmask => "255.255.255.0"
	
	# Provisioning with shell
	control.vm.provision "shell", inline: $addhosts
	control.vm.provision "shell", inline: $edit_sshd
	control.vm.provider "virtualbox" do |vb|
	  vb.cpus = 1
      vb.memory = "512"
    end
  end
  
  # configure two machines keepalive server
  (1..2).each do |i|
	  
	  config.vm.define "keepalive#{i}" do |node|
		node.vm.box = "generic/centos8"
		node.vm.hostname = "keepalive-host#{i}.example.com"
		node.vm.network "forwarded_port", guest: 22, host: "222#{i}", id: "ssh"
		node.vm.network "forwarded_port", guest: 80, host: "808#{i}"
		node.vm.network "forwarded_port", guest: 443, host: "844#{i}"
		node.vm.network "private_network", ip: "192.168.200.10#{i}", :device => "eth2", :adapter => 2, :netmask => "255.255.255.0"
		
		node.vm.provider "virtualbox" do |vb|
		  vb.gui = false
		  vb.cpus = 1
		  vb.memory = "256"
		end
	  end  
   end

   # configure two machines nginx server
  (1..2).each do |i|
	  
	  config.vm.define "nginx#{i}" do |node|
		node.vm.box = "generic/centos8"
		node.vm.hostname = "nginx#{i}.example.com"
		node.vm.network "forwarded_port", guest: 22, host: "222#{i}", id: "ssh"
		node.vm.network "forwarded_port", guest: 80, host: "808#{i}"
		node.vm.network "forwarded_port", guest: 443, host: "844#{i}"
		node.vm.network "private_network", ip: "192.168.200.10#{i}", :device => "eth2", :adapter => 2, :netmask => "255.255.255.0"
		
		node.vm.provider "virtualbox" do |vb|
		  vb.gui = false
		  vb.cpus = 1
		  vb.memory = "256"
		end  
	  end
   end
   
end