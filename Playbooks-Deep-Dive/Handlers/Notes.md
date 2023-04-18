### Ansible Handlers

Ansible handlers execute when a certain task has been completed, and a listener listens for the notify call to then execute another task.

An example of this is bellow with the notify httpd service and the listen httpd service
You must place the listen under a handler group

```yml
--- #Handler
- hosts: remote
  become: yes
  vars:
    httpd_log_level: error
  tasks:
    - name: install httpd
      yum:
        name: httpd
        state: latest
    - name: update config
      template:
        src: /home/ansible/httpd.conf.j2
        dest: /etc/httpd/conf/httpd.conf
      notify: httpd service # This will call the handler listen parameter and flag for it to be run
  handlers: # They look alot like tasks, but only execute when the listen parameter is filled
    - name: httpd service
      service:
        name: httpd
        state: restarted
      listen: httpd service
```

You can have as many handlers as you want in a single playbook
