### Writing your first ansible playbook

Task:

Your supervisor has asked you to find a way to automate and audit basic system configuration for new servers in your environment. Given that Ansible is already configured at a basic level in your environment, the simplest solution would be to just write a playbook for bootstrapping your new hosts. Create a playbook called /home/ansible/bootstrap.yml to fulfill the following boot strap requirements:

All servers:

    Edit /etc/hosts to include the following entry: ansible.xyzcorp.com 169.168.0.1
    Install elinks
    Create the user xyzcorp_audit
    Copy the files /home/ansible/motd and /home/ansible/issue to /etc/

Network servers:

    Install nmap-ncat
    Create the user xyzcorp_network

SysAdmin servers:

    Copy /home/ansible/scripts.tgz from the control node to /mnt/storage

The Ansible control node has been configured for you and each testing server has already been configured for use with Ansible. The default inventory has been configured to include a the groups network and sysadmin. Each group includes a sample host.

### Solution

```yml
---
- hosts: all
  become: yes
  tasks:
    - name: edit host file
      lineinfile:
        path: /etc/hosts
        line: "169.168.0.1 ansible.xyzcorp.com"
    - name: install elinks
      yum:
        name: elinks
        state: latest
    - name: create the user xyzcorp_audit
      ansible.builtin.user:
        name: xyzcorp_audit
        state: present
    - name: Update motd
      copy:
        src: /home/ansible/motd
        dest: /etc/motd
    - name: update issue
      copy:
        src: /home/ansible/issue
        dest: /etc/issue
- hosts: network
  become: yes
  tasks:
    - name: install nmap-ncat
      yum:
        name: nmap-ncat
        state: latest
    - name: create user
      ansible.builtin.user:
        name: xyzcorp_network
        state: present
- hosts: sysadmin
  become: yes
  tasks:
    - name: copy scripts over from tarball to storage
      copy:
        src: /home/ansible/scripts.tgz
        dest: /mnt/storage
```

### Output

```console
PLAY [all] *************************************************************************************************************
TASK [Gathering Facts] *************************************************************************************************ok: [localhost]
ok: [node2]
ok: [node1]

TASK [edit host file] **************************************************************************************************ok: [localhost]
changed: [node2]
changed: [node1]

TASK [install elinks] **************************************************************************************************changed: [localhost]
changed: [node2]
changed: [node1]

TASK [create the user xyzcorp_audit] ***********************************************************************************changed: [node1]
changed: [localhost]
changed: [node2]

TASK [Update motd] *****************************************************************************************************changed: [localhost]
changed: [node2]
changed: [node1]

TASK [update issue] ****************************************************************************************************
changed: [localhost]
changed: [node2]
changed: [node1]

PLAY [network] *********************************************************************************************************
TASK [Gathering Facts] *************************************************************************************************ok: [node1]

TASK [install nmap-ncat] ***********************************************************************************************changed: [node1]

TASK [create user] *****************************************************************************************************changed: [node1]

PLAY [sysadmin] ********************************************************************************************************
TASK [Gathering Facts] *************************************************************************************************ok: [node2]

TASK [copy scripts over from tarball to storage] ***********************************************************************changed: [node2]

PLAY RECAP *************************************************************************************************************localhost                  : ok=6    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node1                      : ok=9    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node2                      : ok=8    changed=6    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
