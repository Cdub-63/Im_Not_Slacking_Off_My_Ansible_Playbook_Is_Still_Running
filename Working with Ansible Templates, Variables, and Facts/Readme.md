![image](https://user-images.githubusercontent.com/44756128/113584854-07799e80-95f1-11eb-91b6-612ae183eb47.png)

# The Scenario
A colleague was the unfortunate victim of a scam email, and their network account was compromised. Shortly after we finished helping them pack up their desk, our boss gave us the assignment to promote system security by deploying a hardened sudoers file. We need to create an Ansible template of the sudoers file.

We also need to create an accompanying playbook in /home/ansible/security.yml that will deploy this template to all servers in the default inventory.

Important notes:
  - Ansible has been installed on the control node.
  - The user ansible has been already created on all servers with the appropriate shared keys for access to the necessary servers from the control node.
  - All necessary Ansible inventories have already been created.

# Logging In
Log into the control node (control1) as the ansible user.

![image](https://user-images.githubusercontent.com/44756128/113585399-cafa7280-95f1-11eb-9877-b95d7e04603c.png)

# Create a Template sudoers File
```sh
[ansible@control1]$ vim /home/ansible/hardened.j2
```

Now that we're in Vim, we'll put these contents in the file:
```
%sysops {{ ansible_default_ipv4.address }} = (ALL) ALL
Host_Alias WEBSERVERS = {{ groups['web']|join(' ') }}
Host_Alias DBSERVERS = {{ groups['database']|join(' ') }}
%httpd WEBSERVERS = /bin/su - webuser
%dba DBSERVERS = /bin/su - dbuser
```

![image](https://user-images.githubusercontent.com/44756128/113585563-0301b580-95f2-11eb-9111-16b0cc55c05c.png)

# Create a Playbook
```sh
[ansible@control1]$ vim /home/ansible/security.yml
```

The security.yml file should look like this:

```yml
 ---
 - hosts: all
   become: yes
   tasks:
   - name: deploy sudo template
     template:
       src: /home/ansible/hardened.j2
       dest: /etc/sudoers.d/hardened
       validate: /sbin/visudo -cf %s
```

![image](https://user-images.githubusercontent.com/44756128/113586450-25480300-95f3-11eb-81e7-6d6efed9ceb2.png)

# Run the Playbook
```sh
[ansible@control1]$ ansible-playbook /home/ansible/security.yml
```

![image](https://user-images.githubusercontent.com/44756128/113586206-d69a6900-95f2-11eb-9778-89404e011e3b.png)

The output will show that everything deployed fine, but we can check locally to make sure. Let's become root (with sudo su -) and then read our file:
```sh
[root@control1]$ cat /etc/sudoers.d/hardened
```

The custom IP and host aliases are in there.

![image](https://user-images.githubusercontent.com/44756128/113586654-62ac9080-95f3-11eb-92f0-e0162be1d2e2.png)
