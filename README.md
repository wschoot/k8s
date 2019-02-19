
-   [skip to content](https://wiki.schoot.org/libvirt#dokuwiki__content)

# [![](https://wiki.schoot.org/lib/tpl/dokuwiki/images/logo.png)ascent](https://wiki.schoot.org/start "[H]")

### User Tools

-   Logged in as:  Wouter Schoot  (ascent)
-   [Update Profile](https://wiki.schoot.org/libvirt?do=profile "Update Profile")
-   [Admin](https://wiki.schoot.org/libvirt?do=admin "Admin")
-   [Log Out](https://wiki.schoot.org/libvirt?do=logout&sectok=349b5e0a6a2b27343f0d73cbf4ed6a6e "Log Out")

### Site Tools

Search

-   [Recent Changes](https://wiki.schoot.org/libvirt?do=recent "Recent Changes [r]")
-   [Media Manager](https://wiki.schoot.org/libvirt?do=media&ns= "Media Manager")
-   [Sitemap](https://wiki.schoot.org/libvirt?do=index "Sitemap [x]")

----------

libvirt

CentOS 7.6

yum -y update
yum -y install gcc libvirt vim tmux qemu-kvm qemu-img libvirt libvirt-python libvirt-client  bridge-utils libvirt-devel rsync git bash-completion
 
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
 
 
# iscsi

for vm in $(sudo virsh list --all --name); do sudo virsh destroy $vm; sudo virsh undefine $vm; done
for vol in $(sudo virsh vol-list default | grep \.img | grep -v box | awk '{ print $1 }'); do sudo virsh vol-delete --pool default $vol; done

libvirt.txt  · Last modified: 2019/02/19 18:58 by  ascent

----------

### Page Tools

[![Donate](https://wiki.schoot.org/lib/tpl/dokuwiki/images/button-donate.gif)](https://www.dokuwiki.org/donate "Donate")  [![Powered by PHP](https://wiki.schoot.org/lib/tpl/dokuwiki/images/button-php.gif)](https://php.net/ "Powered by PHP")  [![Valid HTML5](https://wiki.schoot.org/lib/tpl/dokuwiki/images/button-html5.png)](https://validator.w3.org/check/referer "Valid HTML5")  [![Valid CSS](https://wiki.schoot.org/lib/tpl/dokuwiki/images/button-css.png)](https://jigsaw.w3.org/css-validator/check/referer?profile=css3 "Valid CSS")  [![Driven by DokuWiki](https://wiki.schoot.org/lib/tpl/dokuwiki/images/button-dw.png)](https://dokuwiki.org/ "Driven by DokuWiki")

![](https://wiki.schoot.org/lib/exe/indexer.php?id=libvirt&1550600296)
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTU4ODg0NzI1MCwtMTg5MjU0NzgxM119
-->