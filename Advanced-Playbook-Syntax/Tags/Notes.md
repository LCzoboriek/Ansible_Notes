### Ansible Tags

Tags allow for both plays or tasks to run in such a way as to only run plays or tasks with a particular tag, you specify which tags to run or not via arguments to the ansible-playbook command

Take this playbook for example

```yml
--- # Tags example
- hosts: localhost
  tasks:
    - name: Install elinks
      become: yes
      yum:
        name: elinks
        state: latest
      tags:
        - software
    - name: add line to text file
      lineinfile:
        path: /home/ansible/tag-test.txt
        create: yes
        line: "Tag Called!"
      tags:
        - files
        - config
    - name: copy tag file
      copy:
        src: /home/ansible/tag-test.txt
        dest: /tmp/copied.txt
      tags:
        - config
```

And we run this command

```console
[ansible@bb40367f0b1c ~]$ [ansible@bb40367f0b1c ~]$ ansible-playbook tags.yml --tags software
```

Will produce this

```console
[ansible@bb40367f0b1c ~]$ [ansible@bb40367f0b1c ~]$ ansible-playbook tags.yml --tags software
[WARNING]: Unable to load the facts cache plugin (json).

PLAY [localhost] *******************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************
ok: [localhost]

TASK [Install elinks] **************************************************************************************************
changed: [localhost]

PLAY RECAP *************************************************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

As you can see, the tasks that are run are only elinks as thats the only tasks tagged with software

You can call more then one tag by using a comma

```console
[ansible@bb40367f0b1c ~]$ ansible-playbook tags.yml --tags software,files
[WARNING]: Unable to load the facts cache plugin (json).

PLAY [localhost] *******************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************
ok: [localhost]

TASK [Install elinks] **************************************************************************************************
ok: [localhost]

TASK [add line to text file] *******************************************************************************************
changed: [localhost]

PLAY RECAP *************************************************************************************************************
localhost                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

You can also skip tags by using the skip-tags flag

```console
[ansible@bb40367f0b1c ~]$ ansible-playbook tags.yml --skip-tags software
[WARNING]: Unable to load the facts cache plugin (json).

PLAY [localhost] *******************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************
ok: [localhost]

TASK [add line to text file] *******************************************************************************************
ok: [localhost]

TASK [copy tag file] ***************************************************************************************************
changed: [localhost]

PLAY RECAP *************************************************************************************************************
localhost                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
