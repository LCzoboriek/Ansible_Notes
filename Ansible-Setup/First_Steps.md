### Creating a virtual machine

**Document**
https://lucid.app/lucidchart/1d889755-b1bf-4c48-a960-69afc7188191/view?page=xvHRm.qhwOQq#

**First steps**
Normally the first step of working with ansible is to create your control node machine and then your host machines to be managed by this control node.
You divide these up into different groups within the inventory file.

Then using the inventory commands, you use the modules, and then pass arguments to these modules to perform tasks.
There are hundreds and hundreds of different modules.

So to start with the first step run the following command, under the assumption of using a red hat distribution

```console
sudo yum install ansible
```
