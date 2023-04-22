# adventures in managing docker+libvirt on alpine linux using terraform

Out of the box, docker can be installed and run on alpine linux without
issue. However, libvirt requires a few extra steps before it can be
used with docker and terraform.

## Creating a bridge for libvirt

```sh
# install bridge utils
apk add bridge

# create a bridge (edit as needed)
cat <<<EOF >> /etc/network/interfaces
auto libvirtbr0
iface libvirtbr0 inet static
  address 192.168.1.2
  netmask 255.255.255.0
  gateway 192.168.1.1
  bridge-ports eth0
  bridge-stp 0
EOF

# restart networking
rc-service networking restart
```

## Making docker play nice with libvirt

**/etc/sysctl.conf**:

```conf
# @see https://serverfault.com/questions/963759/docker-breaks-libvirt-bridge-network
net.bridge.bridge-nf-call-iptables=0
```

## Allowing libvirt provider to communicate with libvirt over ssh

```sh
echo AllowTcpForwarding yes >> /etc/ssh/sshd_config
```