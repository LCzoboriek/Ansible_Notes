### Ansible Facts

Ansible facts are simply a variety of properties a given remote system has, that can be retrieved using the setup module

The filter parameter takes regex to allow you to prune fact output.

This can be used in various ways like pulling an ip address of a host, a type of OS the host uses, among other things.

```console
[ansible@bb40367f0b1c ~]$ ansible centos -m setup -a filter=*ipv4
localhost | SUCCESS => {
    "ansible_facts": {
        "ansible_default_ipv4": {
            "address": "172.31.21.38",
            "alias": "ens5",
            "broadcast": "172.31.31.255",
            "gateway": "172.31.16.1",
            "interface": "ens5",
            "macaddress": "06:e5:54:14:95:44",
            "mtu": 9001,
            "netmask": "255.255.240.0",
            "network": "172.31.16.0",
            "type": "ether"
        },
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false
}
bb40367f0b2c.mylabserver.com | SUCCESS => {
    "ansible_facts": {
        "ansible_default_ipv4": {
            "address": "172.31.20.178",
            "alias": "ens5",
            "broadcast": "172.31.31.255",
            "gateway": "172.31.16.1",
            "interface": "ens5",
            "macaddress": "06:1b:64:4c:fa:30",
            "mtu": 9001,
            "netmask": "255.255.240.0",
            "network": "172.31.16.0",
            "type": "ether"
        },
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false
}
bb40367f0b3c.mylabserver.com | SUCCESS => {
    "ansible_facts": {
        "ansible_default_ipv4": {
            "address": "172.31.21.165",
            "alias": "ens5",
            "broadcast": "172.31.31.255",
            "gateway": "172.31.16.1",
            "interface": "ens5",
            "macaddress": "06:9c:bf:b0:37:6c",
            "mtu": 9001,
            "netmask": "255.255.240.0",
            "network": "172.31.16.0",
            "type": "ether"
        },
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false
}
```

So for example we could do the command

```console
[ansible@bb40367f0b1c ~]$ ansible centos -m setup -a filter=*ipv4
```

This will give the ipv4 addresses of all the centos grouped hosts that the control node is managing.

```yml
---
- hosts: centos
  tasks:
    - name: create a file
      lineinfile:
        path: /home/ansible/hostname
        create: yes
        line: "{{ ansible_hostname }}"
    - name: access magic variables
      lineinfile:
        path: /home/ansible/hostname
        line: "{{ hostvars['localhost']['ansible_default_ipv4']['address'] }}"
```

Above is an example of a yml for gathering facts, it uses magic variables, of which there are many, hostvars is a dictionary of facts already gathered on the hosts.

In this example we are calling on localhost, ansible_default_ipv4, and address out of the hostvars dictionary, and getting the values for those key pairs.

Ansible however be default only gathers facts of the host that it touches in that playbook. If we were to run the above playbook in default config it will fail as the localhost is not existing.

In ansible.cfg you can add fact_caching to equal a jsonfile, this will collect alot of data on each server however. So this isnt the right approach, really you should use redis, and should have the redis cache location set to support the amount of data you might get. You might also want to disable fact_gathering by default, and instead run the ansible_setup module on a scheduele.
