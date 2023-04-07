### Ansible Ad-Hoc Command

#### Document

https://lucid.app/lucidchart/1d889755-b1bf-4c48-a960-69afc7188191/view?page=OvHRTmQ.84j2#

**Syntax**

```console
ansible <host> -b -m <MODULE> -a "<ARG1 ARG2 ARGn>" -f <NUM_FORKS>
```

Ansible ad-hoc commands are very similar to bash commands, and a playbook can then be compared to a bash script.

The syntax follows the same similar set up as our example command above. You start with the ansible keyword.

> Then follow that with whatever hosts you want to effect
>
> **-b** is become high priviledge
>
> **-m** requires an argument and stands for module
>
> **-a** is arguments for that module

Forks are something that we will discuss later on
