# -*- mode: ruby -*-
# vi: set ft=ruby :


Vagrant.configure("2") do |config|

  routes = `ip route`.lines.grep(/default via/)
  if routes.first.start_with?("default")
    iface = routes.first.split(" ")[4]  # first line always has default: default via 192.168.1.1 dev eth0  metric 1024
    puts "Detected " + iface + " as default interface"
  end

  config.vm.box = "centos/7"
  config.vm.provider :libvirt do |vb|
    vb.memory = 4096
    vb.cpus = "2"
  end

  # https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/
  # https://kubernetes.io/docs/setup/independent/install-kubeadm/

  # kubectl patch svc my-service -n default -p '{"spec": {"type": "LoadBalancer", "externalIPs":["192.168.88.60", "192.168.121.60"]}}'
  # Token: kubeadm token list
  #   vagrant ssh master -c 'sudo kubeadm token list |grep default-node-token | awk "{ print $1 }"'
  # Hash openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'

  config.vm.network "public_network", bridge: iface, dev: iface
  #config.vm.network "public_network", bridge: 'eno1', dev: 'eno1'
  #config.vm.network "public_network", bridge: ['enp59s0', 'eno1',], dev: ['enp59s0', 'eno1',]
  #config.vm.network :public_network, :bridge => 'eno1', :dev => 'eno1'


  # kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
  # kubectl run --image=nginx nginx-app --port=80 --env="DOMAIN=cluster"
  # kubectl expose deployment nginx-app --type=NodePort --name=nginx--service
  # OF
  # kubectl expose deployment nginx --port 80 --target-port 80 --type ClusterIP --selector=run=nginx
  # Je nginx leeft nu op het externe IP uit kubectl get pods --output=wide
  config.vm.define "master" do |node|
    config.vm.hostname = "master.local"
  end

  (1..3).each do |i|
    config.vm.define "node#{i}" do |node|
      node.vm.hostname = "node#{i}.local"
    end
  end

  config.vm.provision :ansible do |ansible|
    ansible.playbook = "playbook.yml"
		ansible.compatibility_mode = "2.0"
    ansible.groups = {
      "masters" => ["master"],
      "nodes"   => ["node[1:3]"],
    }
  end

end
# vagrant ssh master -- -L 8001:localhost:8001
# http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
# Vraag je token op met :
# [root@master vagrant]# kubectl describe secret $(kubectl describe sa admin-user -n kube-system | awk '/^Tokens/ { print $2 }') -n kube-system | awk ' /^token/ { print $2 }'
