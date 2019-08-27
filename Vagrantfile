# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "centos/7"
  config.vm.provider :libvirt do |vb|
    vb.memory = 4096
    vb.cpus = "2"
  end

	config.vm.define "master" do |node|
		node.vm.synced_folder ".", "/vagrant", type: "sshfs"
		node.vm.synced_folder "/home/wouter/Downloads/LFS258/SOLUTIONS/", "/LFS258", type: "sshfs"
		node.vm.hostname = "master.local"
		node.vm.network :private_network, ip: "10.0.15.30"
		node.vm.provision :hostmanager
		node.hostmanager.aliases = %w(dashboard.traefik dashboard.local guestbook.local whoareyou.local prom.local)
		node.vm.provision :ansible do |ansible|
			ansible.playbook = "playbook.yml"
			ansible.compatibility_mode = "2.0"
			ansible.limit = "all"
			ansible.groups = {
				"masters" => ["master"],
				"nodes"   => ["node[1:3]"],
			}
		end
	end

	(1..3).each do |i|
		config.vm.define "node#{i}" do |node|
			node.vm.hostname = "node#{i}.local"
			node.vm.network :private_network, ip: "10.0.15.3#{i}"
			node.vm.provision :hostmanager
		end
	end

	if Vagrant.has_plugin?("vagrant-hostmanager")
		config.hostmanager.enabled = false
		config.hostmanager.manage_host = true
		config.hostmanager.manage_guest = true
		config.hostmanager.include_offline = true
	end
end
