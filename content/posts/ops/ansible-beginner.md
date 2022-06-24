---
title: "ansible beginner"
author: "rogers"
date: 2022-06-20T00:06:57+08:00
slug: "use-ansible-manager-large-number-of-computer"
summary: "ansible help make it easier to manage large number of computer"
tags: ["ops", "ansible"]
---

## Install

```bash
sudo apt install ansible
#or use pip3
pip3 install --user ansible
ansible --version
# ansible [core 2.12.4]
#   config file = None
#   configured module search path = ['/home/rogers/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
#   ansible python module location = /usr/lib/python3/dist-packages/ansible
#   ansible collection location = /home/rogers/.ansible/collections:/usr/share/ansible/collections
#   executable location = /bin/ansible
#   python version = 3.10.4 (main, Mar 24 2022, 13:07:27) [GCC 11.2.0]
#   jinja version = 3.0.3
#   libyaml = True
ssh-copy-id root@127.0.0.1
#input your password
```

define your inventory file

hosts file

```conf
[localhost]
127.0.0.1
```

```bash
ansible -i hosts -m ping
# 127.0.0.1 | SUCCESS => {
#     "ansible_facts": {
#         "discovered_interpreter_python": "/usr/bin/python3.10"
#     },
#     "changed": false,
#     "ping": "pong"
# }
ansible-doc ping
#show module help
```
