### YAML and Ansible

Document:

https://lucid.app/lucidchart/a84dc946-b1e3-4da2-9e8b-c44eb0a87cff/view?page=xvHRm.qhwOQq#

Lists - Lists are essential useable lists within ansible playbooks that can be accessed outside the playbook itself. You can use them as variable files, and engage for example, a list of users in a playbook

Dictionary - Dictionaries use key-value pairs, that are designated like python, with colons and spaces.

Include_newlines - | you use the pipe character

```yml
include_newlines: | #This will be interpreted as multiple lines
  type some text
  even more text
  the last of the text

fold_newlines: > #This will be interpreted as a single line
  type some text
  more text
  last of the text
```
