### Ansible Playbook Loops

Loops are a relatively simple concept, here is an example below

```yml
--- #loop example
- hosts: localhost
  become: yes
  tasks:
    - name: install software
      yum:
        name: "{{ item }}"
        state: absent
      loop:
        - elinks
        - nmap-ncat
        - bind-utils
        # The idea behind this is to run the yum install module, and replace item with the list of the items in the loop        # Generally you want to iterate through lists with loops
```

This will output the following

```console
[ansible@bb40367f0b1c ~]$ [ansible@bb40367f0b1c ~]$ ansible-playbook loop.yml
[WARNING]: Unable to load the facts cache plugin (json).

PLAY [localhost] *******************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************
ok: [localhost]

TASK [install software] ************************************************************************************************
ok: [localhost] => (item=elinks)
ok: [localhost] => (item=nmap-ncat)
changed: [localhost] => (item=bind-utils)

PLAY RECAP *************************************************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
