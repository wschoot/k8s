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


  config.vm.define "master" do |node|
    config.vm.hostname = "master.local"
    node.vm.provision :shell, inline:  <<-SHELL
    echo 'export KUBECONFIG=/etc/kubernetes/admin.conf' >> ~/.bashrc
    echo "source <(kubectl completion bash)" >> ~/.bashrc
    echo "alias k=kubectl" >> ~/.bashrc
    echo "complete -F __start_kubectl k" >> ~/.bashrc
    export KUBECONFIG=/etc/kubernetes/admin.conf
    kubeadm config images pull
    kubeadm init --pod-network-cidr=10.244.0.0/16
    kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/a70459be0084506e4ec919aa1c114638878db11b/Documentation/kube-flannel.yml
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
    kubectl create clusterrolebinding --user system:serviceaccount:kube-system:default kube-system-cluster-admin --clusterrole cluster-admin
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
    kubectl apply -f ~vagrant/dashboard-adminuser.yaml
    kubectl apply -f ~vagrant/dashboard-adminrole.yaml
    kubectl proxy &
    # kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
    # kubectl run --image=nginx nginx-app --port=80 --env="DOMAIN=cluster"
    # kubectl expose deployment nginx-app --type=NodePort --name=nginx--service
    # OF
    # kubectl expose deployment nginx --port 80 --target-port 80 --type ClusterIP --selector=run=nginx
    # Je nginx leeft nu op het externe IP uit kubectl get pods --output=wide
    SHELL
  end

  (1..2).each do |i|
    config.vm.define "node#{i}" do |node|
      node.vm.hostname = "node#{i}"
    end
  end

  config.vm.provision "file", source: "files/sysctl.d-k8s.conf", destination: "sysctl.d-k8s.conf"
  config.vm.provision "file", source: "files/dashboard-adminuser.yaml", destination: "dashboard-adminuser.yaml"
  config.vm.provision "file", source: "files/dashboard-adminrole.yaml", destination: "dashboard-adminrole.yaml"
  config.vm.provision :ansible do |ansible|
    ansible.playbook = "ansible.yml"
    ansible.groups = {
      "masters" => ["master"],
      "nodes"   => ["node1", "node2"],
    }

  end

  config.vm.provision "shell", inline: <<-SHELL
  mv ~vagrant/sysctl.d-k8s.conf /etc/sysctl.d/k8s.conf && sysctl --system
  swapoff -a; sed '/swap/d' -i /etc/fstab

SHELL
end
# http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/

# vagrant ssh master -- -L 8001:localhost:8001
