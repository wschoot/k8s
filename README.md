# libvirt

CentOS 7.6
```bash
yum -y update
yum -y install \
	gcc \
	libvirt \
	vim \
	tmux \
	qemu-kvm \
	qemu-img \
	libvirt \
	libvirt-python \
	libvirt-client \
	bridge-utils \
	libvirt-devel \
	rsync \
	git \
	bash-completion
 
scp wouter@schoot.org:.vimrc ~
scp wouter@schoot.org:.tmux.conf ~
scp wouter@schoot.org:.gitconfig ~
 
 
# sudo visudo #NOPASSWD
 
systemctl enable --now libvirtd
 
yum -y install https://releases.hashicorp.com/vagrant/2.2.3/vagrant_2.2.3_x86_64.rpm
 
vagrant plugin install vagrant-libvirt
 
sudo usermod -a -G libvirt wouter
#sudo usermod -a -G kvm wouter
 
sudo reboot # voor de juiste modules, anders 
#rmmod kvm_intel
#rmmod kvm
#modprobe kvm
#modprobe kvm_intel
 ```
 
# iscsi

```bash
for vm in $(sudo virsh list --all --name); do sudo virsh destroy $vm; sudo virsh undefine $vm; done
for vol in $(sudo virsh vol-list default | grep \.img | grep -v box | awk '{ print $1 }'); do sudo virsh vol-delete --pool default $vol; done

```	
	
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1NDI3NTM4MjMsNTU0NzU0NDk1LC0xOD
kyNTQ3ODEzXX0=
-->