## File Manipulation using Ansible Modules

> Expectations
>
> 1. Understand key arguments
> 2. Use ansible ad hoc commands to manipulate files

### Task

![Diagram](Screen%20Shot%202023-04-04%20at%2012.04.58.png)

#### Step 1

Download the tarball and checksum from the url and store it in a variable

```console
CHECKSUM=$(curl http://software.xyzcorp.com/enigma-checksum.txt | cut -f1 -d' ')
```

Next we pull down the tarball to the hosts that we are trying to configure

```console
ansible qa-servers -m get_url -a "url=http://software.xyzcorp.com/enigma.tgz dest=/tmp/enigma.tgz checksum=sha256:$CHECKSUM"
```

> > ansible (user)
> > qa-servers (hosts group)
> > -m (module)  
> > get_url (get_url module)
> > -a (arguments for module)
> > "url=http://software.xyzcorp.com/enigma.tgz (location of file)
> > dest=/tmp/enigma.tgz (destination)
> > checksum=sha256:$CHECKSUM" (checksum check)

### Output

```console
localhost | CHANGED => {
    "changed": true,
    "checksum_dest": null,
    "checksum_src": "e79da8d37fb0edc3d51968cd6010e54c0446a73a",
    "dest": "/tmp/enigma.tgz",
    "gid": 1003,
    "group": "ansible",
    "md5sum": "325849d3079fb429e0e2bcf1b034a906",
    "mode": "0664",
    "msg": "OK (583 bytes)",
    "owner": "ansible",
    "secontext": "unconfined_u:object_r:user_home_t:s0",
    "size": 583,
    "src": "/home/ansible/.ansible/tmp/ansible-tmp-1680690480.82-7859-86562761547129/tmpAnbePZ",
    "state": "file",
    "status_code": 200,
    "uid": 1003,
    "url": "http://software.xyzcorp.com/enigma.tgz"
}
node2 | CHANGED => {
    "changed": true,
    "checksum_dest": null,
    "checksum_src": "e79da8d37fb0edc3d51968cd6010e54c0446a73a",
    "dest": "/tmp/enigma.tgz",
    "gid": 1003,
    "group": "ansible",
    "md5sum": "325849d3079fb429e0e2bcf1b034a906",
    "mode": "0664",
    "msg": "OK (583 bytes)",
    "owner": "ansible",
    "secontext": "unconfined_u:object_r:user_home_t:s0",
    "size": 583,
    "src": "/home/ansible/.ansible/tmp/ansible-tmp-1680690480.98-7862-130499215643818/tmp2vliGV",
    "state": "file",
    "status_code": 200,
    "uid": 1003,
    "url": "http://software.xyzcorp.com/enigma.tgz"
}
node1 | CHANGED => {
    "changed": true,
    "checksum_dest": null,
    "checksum_src": "e79da8d37fb0edc3d51968cd6010e54c0446a73a",
    "dest": "/tmp/enigma.tgz",
    "gid": 1003,
    "group": "ansible",
    "md5sum": "325849d3079fb429e0e2bcf1b034a906",
    "mode": "0664",
    "msg": "OK (583 bytes)",
    "owner": "ansible",
    "secontext": "unconfined_u:object_r:user_home_t:s0",
    "size": 583,
    "src": "/home/ansible/.ansible/tmp/ansible-tmp-1680690481.07-7861-130150818837889/tmpI6tY10",
    "state": "file",
    "status_code": 200,
    "uid": 1003,
    "url": "http://software.xyzcorp.com/enigma.tgz"
}
```

#### Extract to opt directory

```console
[ansible@control ~]$ ansible qa-servers -b -m unarchive -a "src=/tmp/enigma.tgz dest=/opt/ remote_src=yes"
```

We use remote_src=yes because we are using the tarball downloaded to each of the servers and not downloading it again

#### Output

```console
 node2 | CHANGED => {
    "changed": true,
    "dest": "/opt/",
    "extract_results": {
        "cmd": [
            "/bin/gtar",
            "--extract",
            "-C",
            "/opt/",
            "-z",
            "-f",
            "/tmp/enigma.tgz"
        ],
        "err": "",
        "out": "",
        "rc": 0
    },
    "gid": 0,
    "group": "root",
    "handler": "TgzArchive",
    "mode": "0755",
    "owner": "root",
    "secontext": "system_u:object_r:usr_t:s0",
    "size": 20,
    "src": "/tmp/enigma.tgz",
    "state": "directory",
    "uid": 0
}
localhost | CHANGED => {
    "changed": true,
    "dest": "/opt/",
    "extract_results": {
        "cmd": [
            "/bin/gtar",
            "--extract",
            "-C",
            "/opt/",
            "-z",
            "-f",
            "/tmp/enigma.tgz"
        ],
        "err": "",
        "out": "",
        "rc": 0
    },
    "gid": 0,
    "group": "root",
    "handler": "TgzArchive",
    "mode": "0755",
    "owner": "root",
    "secontext": "system_u:object_r:usr_t:s0",
    "size": 20,
    "src": "/tmp/enigma.tgz",
    "state": "directory",
    "uid": 0
}
node1 | CHANGED => {
    "changed": true,
    "dest": "/opt/",
    "extract_results": {
        "cmd": [
            "/bin/gtar",
            "--extract",
            "-C",
            "/opt/",
            "-z",
            "-f",
            "/tmp/enigma.tgz"
        ],
        "err": "",
        "out": "",
        "rc": 0
    },
    "gid": 0,
    "group": "root",
    "handler": "TgzArchive",
    "mode": "0755",
    "owner": "root",
    "secontext": "system_u:object_r:usr_t:s0",
    "size": 20,
    "src": "/tmp/enigma.tgz",
    "state": "directory",
    "uid": 0
}
```

We have now been asked to change a line of text in a file from DEPLOY_CODE to CODE_RED, we do this with the lineinfile module like displayed below

```console
[ansible@control ~]$ ansible qa-servers -b -m lineinfile -a "regexp=DEPLOY_CODE line=CODE_RED path=/opt/enigma/details.txt"
```

The regexp part is searching the text in details.txt, and changing that line of code to CODE_RED

Next we have to change the group ownership of directorys located on the servers so that each file contained within the directory is protected for each host in qa-servers

```console
ansible qa-servers -b -m file -a "recurse=yes state=directory path=/opt/enigma/secret group=protected"
```

> ansible (ansible command)
> qa-servers (host group)
> -b (become root)
> -m (module)
> file (module)
> -a (arguments)
> recurse=yes (we want to change all files contained within this directory)
> state=directory (targeting a directory)
> path (the location path)
> group (protected)

Last step is to delete the passwords.txt file from all servers

```console
ansible all -b -m file -a "state=absent path=/opt/enigma/tmp/deployment-passwords.txt"
```

We use the file module for this, and state=absent is to get rid of the files if present, and then path is the file we are looking to remove

### Output

```console
localhost | CHANGED => {
    "changed": true,
    "gid": 1004,
    "group": "protected",
    "mode": "0755",
    "owner": "root",
    "path": "/opt/enigma/secret",
    "secontext": "unconfined_u:object_r:usr_t:s0",
    "size": 70,
    "state": "directory",
    "uid": 0
}
node2 | CHANGED => {
    "changed": true,
    "gid": 1004,
    "group": "protected",
    "mode": "0755",
    "owner": "root",
    "path": "/opt/enigma/secret",
    "secontext": "unconfined_u:object_r:usr_t:s0",
    "size": 70,
    "state": "directory",
    "uid": 0
}
node1 | CHANGED => {
    "changed": true,
    "gid": 1004,
    "group": "protected",
    "mode": "0755",
    "owner": "root",
    "path": "/opt/enigma/secret",
    "secontext": "unconfined_u:object_r:usr_t:s0",
    "size": 70,
    "state": "directory",
    "uid": 0
}
```

Learning outcomes:

- Use the unarchive module to deploy tarballs to all workstations on a particular host group
- Check the tarballs authenticity with a checksum file and then running that tarball against the checksum to ensure no corruption has occured
- We use remote_src when using localised files that are already downloaded
- Line in file module will replace parts of a text file when provided with the correct arguments
-
