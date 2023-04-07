### Ansible Facts

Ansible Facts are various information about hosts that are managed using Ansible. IP addresses, disk configuration, distro of linux, and lots of other information

```console
ansible remote -m setup | less
```

Returns

```yml
bb40367f0b3c.mylabserver.com | SUCCESS => {
    "ansible_facts": {
        "ansible_all_ipv4_addresses": [
            "172.31.21.165"
        ],
        "ansible_all_ipv6_addresses": [
            "2a05:d01c:2c7:d803:c68b:2ef6:4d0a:d2ec",
            "fe80::49c:bfff:feb0:376c"
        ],
        "ansible_apparmor": {
            "status": "disabled"
        },
        "ansible_architecture": "x86_64",
        "ansible_bios_date": "10/16/2017",
        "ansible_bios_version": "1.0",
        "ansible_cmdline": {
            "BOOT_IMAGE": "/boot/vmlinuz-3.10.0-1160.81.1.el7.x86_64",
            "LANG": "en_US.UTF-8",
            "console": "ttyS0,115200n8",
            "crashkernel": "auto",
            "ro": true,
            "root": "UUID=0f790447-ebef-4ca0-b229-d0aa1985d57f",
            "vconsole.font": "latarcyrheb-sun16",
            "vconsole.keymap": "us"
        },
        "ansible_date_time": {
```

And this particular file goes on for a long time, you can however add filters like below

```console
ansible remote -m setup -a "filer=*dist*"
```

Returns

```yml
[ansible@bb40367f0b1c ~]$ ansible remote -m setup -a "filter=*dist*"
bb40367f0b3c.mylabserver.com | SUCCESS => {
    "ansible_facts": {
        "ansible_distribution": "CentOS",
        "ansible_distribution_file_parsed": true,
        "ansible_distribution_file_path": "/etc/redhat-release",
        "ansible_distribution_file_variety": "RedHat",
        "ansible_distribution_major_version": "7",
        "ansible_distribution_release": "Core",
        "ansible_distribution_version": "7.9",
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false
}
bb40367f0b2c.mylabserver.com | SUCCESS => {
    "ansible_facts": {
        "ansible_distribution": "CentOS",
        "ansible_distribution_file_parsed": true,
        "ansible_distribution_file_path": "/etc/redhat-release",
        "ansible_distribution_file_variety": "RedHat",
        "ansible_distribution_major_version": "7",
        "ansible_distribution_release": "Core",
        "ansible_distribution_version": "7.9",
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false
}
```

Its also possible to output this stuff to files using the outputfile flag
