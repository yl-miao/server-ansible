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
ansible-playbook server.yaml -e "custom_block_ports=[12345, 23456]"
```


### 2.检查:

```bash
#检查fail2ban日志:
tail -f /var/log/fail2ban.log
#检查 nftables 规则是否正确应用：
nft list ruleset
```

手动解封所有fail2ban jail中的IP地址：

```bash
sudo fail2ban-client unban --all
```

只针对解封单独的地址：
```bash
sudo fail2ban-client unban <ip-address>
```

fail2ban-client 命令还有许多其他选项，提供了很多可能性和灵活性。详细了解它们，[look here](https://manpages.debian.org/testing/fail2ban/fail2ban-client.1.en.html)

### 3.References:

https://linuxiac.com/how-to-protect-ssh-with-fail2ban/