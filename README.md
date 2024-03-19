# server-ansible

The Ansible Playbook to configure new servers

**For now, this ONLY supports Debian/Ubuntu (amd64/arm64)!**

tested on Debian 12 (amd64) and Ubuntu 22.04 (amd64 and arm64)

### 0. Overview

It does the following:

- enable BBR
- update and install common packages (use fnm instead of nvm, use nftables instead of iptables, use iproute2 instead of net-tools)
- install Caddy V2 (as Web Server), [rustup](https://www.rust-lang.org/tools/install), [gvm](https://github.com/moovweb/gvm) (Go version manager), [fnm](https://github.com/Schniz/fnm) (Node.js version manager), Minconda3, Docker CE
- install and configure nftables and fail2ban (ufw and iptables will be removed if they exist)
- configure vim
- block the ports 3306, 5432, 27017, and custom ports

### 0. Configure the servers:


First, clone my repo:

```bash
git clone https://github.com/yl-miao/server-ansible
cd server-ansible
```

then, install Ansible: https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html

then, copy your SSH keys to the servers that you want to set up:

```bash
ssh-copy-id -p 2222 root@1.1.1.1
ssh-copy-id -p 2222 root@2.2.2.2
sudo nano /etc/ansible/hosts
```

Then, edit ansible hosts file `sudo vim /etc/ansible/hosts`:

```bash
[servers]
serv1 ansible_host=1.1.1.1 ansible_ssh_port=2222 ansible_user=root
serv2 ansible_host=2.2.2.2 ansible_ssh_port=2222 ansible_user=root

[web]
#host1 ansible_host=192.168.1.20 ansible_user=webadmin ansible_ssh_private_key_file=/path/to/webkey

[all:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_ssh_private_key_file=~/.ssh/id_rsa
```

and then, to check availability: `ansible all -m ping`


### 1. Run the playbook:

To run the playbook:

```bash
# ansible-galaxy role install andrewrothstein.miniconda
ansible-playbook server.yaml
```

or

```bash
ansible-playbook server.yaml -e "custom_block_ports=[12345, 23456]"
```

to start at task:

```bash
ansible-playbook server.yaml --start-at-task="<Your starting task name>"
```

to force reinstall docker and other softwares even if they exist on the target machine:

```bash
ansible-playbook server.yaml -e force_reinstalls=true
```

### 2.Check:

```bash
#check the log of fail2ban:
tail -f /var/log/fail2ban.log
#check if the nftables rules are used:
nft list ruleset
```

Manually unban all IP addresses in the fail2ban jail：

```bash
sudo fail2ban-client unban --all
```

Only unban certain address:

```bash
sudo fail2ban-client unban <ip-address>
```

fail2ban-client has many other options，provides a lot of possibilities and flexibilities. To learn more，[look here](https://manpages.debian.org/testing/fail2ban/fail2ban-client.1.en.html)

### 3.References:

https://linuxiac.com/how-to-protect-ssh-with-fail2ban/

https://docs.docker.com/engine/install/debian/

https://medium.com/@GarisSpace/how-to-install-docker-using-ansible-01a674086f8c
