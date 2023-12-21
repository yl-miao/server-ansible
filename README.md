# server-ansible
The Ansible Playbook to configure new servers

### 0. Overview

It does the following:

* use BBR
* update packages
* install Caddy V2
* install and configure nftables and fail2ban
* block the ports 3306, 5432, 27017, and custom ports

### 1. Run the playbook:

```bash
ansible-playbook server.yaml
```

or

```bash
ansible-playbook server.yml -e "custom_block_ports=[12345, 23456]"
```


###检查:

```bash
#检查fail2ban日志:
tail -f /var/log/fail2ban.log
#检查 nftables 规则是否正确应用：
nft list ruleset
```


###References:

https://linuxiac.com/how-to-protect-ssh-with-fail2ban/