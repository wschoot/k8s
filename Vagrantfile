# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "centos/7"
  config.vm.provider :libvirt do |vb|
    vb.memory = 4096
    vb.cpus = "2"
  end

  # kubectl run --image=nginx nginx-app --port=80 --env="DOMAIN=cluster"
  # kubectl expose deployment nginx-app --type=NodePort --name=nginx--service
  # OF
  # kubectl expose deployment nginx --port 80 --target-port 80 --type ClusterIP --selector=run=nginx
  # Je nginx leeft nu op het externe IP uit kubectl get pods --output=wide

  config.vm.define "master" do |node|
    node.vm.hostname = "master.local"
		node.vm.network :private_network, ip: "10.0.15.30"
		node.vm.provision :hostmanager
	end

	N = 3
	(1..N).each do |i|
		config.vm.define "node#{i}" do |node|
			node.vm.hostname = "node#{i}.local"
			node.vm.network :private_network, ip: "10.0.15.3#{i}"
			node.vm.provision :hostmanager

			if i == N
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
		end
	end

	if Vagrant.has_plugin?("vagrant-hostmanager")
		config.hostmanager.enabled = false
		config.hostmanager.manage_host = true
		config.hostmanager.manage_guest = true
		config.hostmanager.include_offline = true
	end
end
