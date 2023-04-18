### Conditionals in Ansible

Ansible playbooks are capable of making actions conditional when youuse the keyword
"when"
Its used to test a condtion within a playbook

Lets take this playbook as an example

```yml
--- #Ansible Conditional Example
- hosts: remote
  vars:
    target_file: /home/ansible/hostname
  tasks:
    - name: Gather file information
      stat: # Returns information about a target
        path: "{{ target_file }}" # Target for information collection
      register: hostname # Registers a variable with the value of hostname, this is only accessible within the play
    - name: Rename hostname when found
      command: mv "{{ target_file }}" /home/ansible/net-info # This will then move the target file
      when: hostname.state.exists # If hostnames state is exist
```
