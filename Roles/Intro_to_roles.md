## Ansible Roles and why

Ansible roles are an independent component which allows resue of common configuration steps.

> > 1. They are used within playbooks
> > 2. They are reusable
> > 3. Ansible roles is a set of tasks to configure a host to serve a certain purpose, like SSH for example
> > 4. Roles are defined using YAML files with a predefined directory structure

---

For example, lets say you want to create a ansible baseline role for a baseline for all your nodes to be set up on
The first step is creating the directory as the ansible user, and setting permissions for that folder using chown
The second step would be to create a few directorys contained within /etc/ansible/roles for use of setting up a role
and lastly, we need to create the main.yml that the role will be based off

```console
[ansible@control1 ~]$ sudo mkdir baseline && sudo chown ansible.ansible baseline
[ansible@control1 roles]$ mkdir /etc/ansible/roles/baseline/{templates,tasks,files}
[ansible@control1 roles]$ echo "---" > baseline/tasks/main.yml
```

One normal step at the begining would be to copy over a message of the day template for the ansible role. You can do this by
simply copying, like below, into the templates directory

```console
[ansible@control1 roles]$ cp /home/ansible/resources/motd.j2 baseline/templates/
```

Next we need to develop the task to deploy the role

We can do this with a seperate .yml file, we will create a deploy_motd.yml file within baseline/tasks/ that will then be used in the main.yml. This will split up tasks more effectively

#### Motd.yml

```yml
---
- template:
    src: motd.j2
    dest: /etc/motd
```

#### Main.yml

```yml
---
- name: configure motd
  import_tasks: deploy_motd.yml
```

Next Step is to install the latest Nagios Client using the role

```console
[ansible@control1 roles]$ vim baseline/tasks/deploy_nagios.yml
```

#### Nagios.yml

```yml
---
- yum: name=nrpe state=latest
```

####Main.yml

```yml
---
- name: configure motd
  import_tasks: deploy_motd.yml
- name: deploy nagios client
  import_tasks: deploy_nagios.yml
```

Our next step is to configure the role to add an entry to /etc/hosts for the Nagios
Server

#### Edit_hosts.yml

```yml
---
- lineinfile:
    line: "10.0.1.90 nagios.example.com"
    path: /etc/hosts
```

Then once again we have to edit the main.yml to now include our new tasks

Once we have added in the new tasks to the main.yml we have now been tasked with
configuring the role to create the noc User and deploy the provided public
key for the noc user on target systems

First we copy over the file from the home/ansible/resources folder, to our baseline/files folder we created earlier

```console
[ansible@control1 roles]$ cp /home/ansible/resources/authorized_keys /etc/ansible/roles/baseline/files/
```

Next we are to create the yml file that will create the user, and place the authorized keys within the specified destination on the target csystem

```yml
---
- user: name=noc
- file:
    state: directory
    path: /home/noc/.ssh
    mode: 0600
    owner: noc
    group: noc
- copy:
    src: authorized_keys
    dest: /home/noc/.ssh/authorized_keys
    mode: 0600
    owner: noc
    group: noc
```

Once we have added in this new task to our main.yml we have now been asked to edit web.yml and deploy the baseline role

```console
[ansible@control1 roles]$ vim /home/ansible/resources/web.yml
```

Then edit the file to this

```yml
---
- hosts: webservers
  become: yes
  roles:
    - baseline
  tasks:
    - name: install httpd
      yum: name=httpd state=latest
    - name: start and enable httpd
      service: name=httpd state=started enabled=yes
enabled=yes
```

Last step is to deploy the playbook

```console
[ansible@control1 ~]$ ansible-playbook resources/web.yml

PLAY [webservers] *******************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************
ok: [node2]
ok: [node1]

TASK [baseline : template] **********************************************************************************************************************************************************************************************************************
changed: [node1]
changed: [node2]

TASK [baseline : yum] ***************************************************************************************************************************************************************************************************************************
changed: [node1]
changed: [node2]

TASK [baseline : lineinfile] ********************************************************************************************************************************************************************************************************************
changed: [node1]
changed: [node2]

TASK [baseline : user] **************************************************************************************************************************************************************************************************************************
changed: [node1]
changed: [node2]

TASK [baseline : file] **************************************************************************************************************************************************************************************************************************
changed: [node2]
changed: [node1]

TASK [baseline : copy] **************************************************************************************************************************************************************************************************************************
changed: [node1]
changed: [node2]

TASK [install httpd] ****************************************************************************************************************************************************************************************************************************
changed: [node1]
changed: [node2]

TASK [start and enable httpd] *******************************************************************************************************************************************************************************************************************
changed: [node1]
changed: [node2]

PLAY RECAP **************************************************************************************************************************************************************************************************************************************
node1                      : ok=9    changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node2                      : ok=9    changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
