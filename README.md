# k8s
Kubernetes with a master node and three worker nodes based on official _centos/7_ vagrant boxes :
* **Vagrant**
  * sshfs
  * hostmanager
  * ...
* **Ansible**
  * Version?
* **libvirt**
* **Multiple networkpods**
  * Flannel
  * Celium
  * Calico
  * Weave
* MetalLB (for simple loadbalancing, aka giving out IP's)
* Kubernetes Dashboard
* (_Optional_) Istio (1.2.2) installation
* (_Optional_) Guestbook sample application

## To be added
 * HPA maken
 * idempotent
 * [Meerdere etcd](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/setup-ha-etcd-with-kubeadm/)
 * ceph als onderliggende storage middels PVC's
 * k8s from scratch
 * Moderner OS als onderlaag ivm kerneleisen (BPF) cilium
 * [Traefik](https://traefik.io/) als ingress controller http://dashboard.traefik/metrics
 * Rewrite to use https://docs.ansible.com/ansible/latest/modules/k8s_module.html
 * Alles in eigen namespace
