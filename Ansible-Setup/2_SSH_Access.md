### SSH Access in Ansible

Now with ansible automation, it is possible to execute commands without the use of ssh keys using the -k authentication process, however this will prompt for a ssh password every single time. When managing a large stack this can get tricky.
So this is where SSH Keys comes into play

First step

```console
ssh-keygen
```

Second step

```console
ssh-copy-id node1hostname
ssh-copy-id node2hostname
```

This will copy across the ssh keys to both the nodes, and anymore you need to copy across to which are held in groups, and allow unrestricted access for ansible commands to run on the hosts that need to be configured from the control node.
