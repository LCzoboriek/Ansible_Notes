### Ansible Inventory

An inventory is a list of hosts that Ansible manages.

The default inventory files is /etc/ansible/hosts

**NOTE** This has changed now with newer versions of RHEL and CentOS, when installing with python using pip you must create this file, and then change the default configuration to point to that inventory file
The inventory file looks like this by default

```yml
# This is the default ansible 'hosts' file.
#
# It should live in /etc/ansible/hosts
#
#   - Comments begin with the '#' character
#   - Blank lines are ignored
#   - Groups of hosts are delimited by [header] elements
#   - You can enter hostnames or ip addresses
#   - A hostname/ip can be a member of multiple groups

# Ex 1: Ungrouped hosts, specify before any group headers.

## green.example.com
## blue.example.com
## 192.168.100.1
## 192.168.100.10

# Ex 2: A collection of hosts belonging to the 'webservers' group

## [webservers]
## alpha.example.org
## beta.example.org
## 192.168.1.100
## 192.168.1.110

# If you have multiple hosts following a pattern you can specify
# them like this:

## www[001:006].example.com

# Ex 3: A collection of database servers in the 'dbservers' group

## [dbservers]
##
## db01.intranet.mydomain.net
## db02.intranet.mydomain.net
## 10.25.1.56
## 10.25.1.57

# Here's another example of host ranges, this time there are no
# leading 0s:

## db-[99:101]-node.example.com
```

As you can see you can group hosts into groups by using the [] bracket notation

So if you wanted to create a webservers group you

```yml
[webservers]
192.168.0.3
192.168.0.4
```

You can also specify what inventory files to use in adhoc commands by using the -i tag, and it will use that inventory file instead of the default one.

A good way to see if your inventory is working correctly, you can use the ping module to ping groups

```console
ansible centos -m ping
```

This above command will ping every server in the centos group
The returned state sequence is not the sequence its defined in the hosts file, its just whatever server returns the ping first
This is due to Ansibles fork nature
