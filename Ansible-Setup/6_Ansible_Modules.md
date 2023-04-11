### Ansible Modules

#### Documentation

https://lucid.app/lucidchart/1d889755-b1bf-4c48-a960-69afc7188191/view?page=OvHRTmQ.84j2#

### Shell and Command Modules

These are effective modules to run "raw" commands on a target host

In practice you want to mostly avoid using these modules, and you only use it if there isnt a module to perform the task you need to do

```console
ansible localhost -a "<FreeformArgument>touch /tmp/file"
```

This will give you a warning if there is already a module that can perform this task

This can also take in state arguments to ensure the ansible command is only run when needed. You can find the
