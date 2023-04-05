Be able to develop and encrypt and work with sensitive files within ansible

#### Tasks

Currently our secret file is unencrypted and we can see that by using cat

```console
[ansible@control1 ~]$ cat secret
secure_user: bond
secure_password: james
```

We ecnrypt the file using ansible vault command

```console
[ansible@control1 ~]$ ansible-vault encrypt secret
New Vault password:
Confirm New Vault password:
Encryption successful
```

For our playbooks to be able to use that encrypted file we must echo the password onto the vault file

```console
[ansible@control1 ~]$ echo "PASSWORD" > vault
```

When you now cat the file you will see this instead

```console
[ansible@control1 ~]$ cat secret
$ANSIBLE_VAULT;1.1;AES256
65663237363132353966333531303366393135663062303135376637636632643332613735316134
3934393663366532643238353838626264323836613062370a366461393830323664346332396536
32373861343265373461643833383134656234663461313263613537376339663238663530643437
3435343735333539330a346266663432656434653961306262303938336338343936633061613034
31376662343561363730373063613434623237303662343031613739323364383332333563353131
3363656164313631346236636539336639333136646136353565
```

You can also adhoc view the encrypted file

```console
[ansible@control1 ~]$ ansible-vault view secret
Vault password:
secure_user: bond
secure_password: james
```

Now to run the playbook itself

```console
[ansible@control1 ~]$ ansible-playbook --vault-password vault secPage.yml
```

This will use the password previously stored in vault, and allow secPage.yml to access the confidential information

This is what that playbook looked like

```yml
---
- hosts: webservers
  become: yes
  vars_files:
    - /home/ansible/secret
  tasks:
    - name: install apache
      yum: name=httpd state=latest
    - name: configure httpd as necessary
      template:
        src: /home/ansible/assets/httpd.conf.j2
        dest: /etc/httpd/conf/httpd.conf
    - name: create secure directory
      file: state=directory path=/var/www/html/secure mode=0755
    - name: deploy htaccess file
      template:
        src: /home/ansible/assets/htaccess.j2
        dest: /var/www/html/secure/.htaccess
    - name: make sure passlib is installed for htpasswd module
      yum: name=python-passlib state=latest
    - name: create users for basic auth
      htpasswd:
        path: /var/www/html/secure/.passwdfile
        name: "{{ secure_user }}"
        password: "{{ secure_password }}"
        crypt_scheme: md5_crypt
    - name: start and enable apache
      service: name=httpd state=started enabled=yes
    - name: install secure files
      copy:
        src: /home/ansible/assets/classified.html
        dest: /var/www/html/secure/classified.html
```

To test this secure page has been deployed correctly we can attemp to access http://node1/secure/classified.html as the user _bond_ with the password _james_

```console
[ansible@control1 ~]$ curl -u bond http://node1/secure/classified.html
Enter host password for user 'bond':
It's always sunny in Moscow this time of year....
```
