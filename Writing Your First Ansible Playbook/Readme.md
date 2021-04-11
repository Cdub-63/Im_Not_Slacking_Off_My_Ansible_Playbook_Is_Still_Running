![image](https://user-images.githubusercontent.com/44756128/114306369-6d5aa000-9aa1-11eb-904a-fdebcbd6e6ac.png)

Introduction
Playbooks are the heart of Ansible. They provide a means of executing numerous tasks across any number of systems.

Your supervisor has asked you to find a way to automate and audit basic system configuration for new servers in your environment. Given that Ansible is already configured at a basic level in your environment, the simplest solution would be to just write a playbook for bootstrapping your new hosts. Create a playbook called /home/ansible/bootstrap.yml to fulfill the following boot strap requirements:

All servers:
  - Edit /etc/hosts to include the following entry:
```sh
ansible.xyzcorp.com  169.168.0.1
```
  - Install elinks
  - Create the user xyzcorp_audit
  - Copy the files /home/ansible/motd and /home/ansible/issue to /etc/

Network servers:
  - Install nmap-ncat
  - Create the user xyzcorp_network

SysAdmin servers:
  - Copy /home/ansible/scripts.tgz from the control node to /mnt/storage

Begin by logging in to the Ansible control node:
```sh
ssh username@PUBLIC_IP_ADDRESS
```

Become the ansible user:
```sh
sudo -i -u ansible
```

# Create a basic playbook for all servers in the Ansible inventory
Create a basic playbook in /home/ansible/bootstrap.yml that completes the noted task required for all servers in the Ansible inventory.
 - Create the file /home/ansible/bootstrap.yml and add the following content:
```sh
 vim /home/ansible/bootstrap.yml
 ```
 
 ![image](https://user-images.githubusercontent.com/44756128/114306774-8b74d000-9aa2-11eb-83a6-94147e76f063.png)

 ```sh
 ---
 - hosts: all
   become: yes
   tasks:
     - name: edit host file
       lineinfile:
         path: /etc/hosts
         line: "ansible.xyzcorp.com 169.168.0.1"
     - name: install elinks
       package:
         name: elinks
         state: latest
     - name: create audit user
       user:
         name: xyzcorp_audit
         state: present
     - name: update motd
       copy:
         src: /home/ansible/motd
         dest: /etc/motd
     - name: update issue
       copy:
         src: /home/ansible/issue
         dest: /etc/issue
```

![image](https://user-images.githubusercontent.com/44756128/114306800-a0e9fa00-9aa2-11eb-9c10-c13d1d4013f0.png)

# Add a section to the playbook for the Network servers in the Ansible inventory
Add a section to the playbook in /home/ansible/bootstrap.yml that completes the noted task required for the network servers in the Ansible inventory.
  - Create a play in the file /home/ansible/bootstrap.yml similar to the following:
```sh
  - hosts: network
    become: yes
    tasks:
      - name: install netcat
        yum:
          name: nmap-ncat
          state: latest
      - name: create network user
        user:
          name: xyzcorp_network
          state: present
```

# Add a section to the playbook for the SysAdmin servers in the Ansible inventory
Add a section to the playbook in /home/ansible/bootstrap.yml that completes the noted task required for the sysadmin servers in the Ansible inventory.
  - Create a play in the file /home/ansible/bootstrap.yml similar to the following:
```sh
  - hosts: sysadmin
    become: yes
    tasks:
      - name: copy tarball
        copy:
          src: /home/ansible/scripts.tgz
          dest: /mnt/storage/
```

Total of bootstrap.yml:
```sh
---
- hosts: all
  become: yes
  tasks:
    - name: edit host file
      lineinfile:
        path: /etc/hosts
        line: "169.168.0.1 ansible.xyzcorp.com"
    - name: install elinks
      package:
        name: elinks
        state: latest
    - name: create audit user
      user:
        name: xyzcorp_audit
        state: present
    - name: update motd
      copy:
        src: /home/ansible/motd
        dest: /etc/motd
    - name: update issue
      copy:
        src: /home/ansible/issue
        dest: /etc/issue
- hosts: network
  become: yes
  tasks:
    - name: install netcat
      yum:
        name: nmap-ncat
        state: latest
    - name: create network user
      user:
        name: xyzcorp_network
        state: present
- hosts: sysadmin
  become: yes
  tasks:
    - name: copy tarball
      copy:
        src: /home/ansible/scripts.tgz
        dest: /mnt/storage/
```

# Execute playbook to verify your playbook works correctly
Execute playbook /home/ansible/bootstrap.yml to verify your playbook works correctly.
  - Run ansible-playbook /home/ansible/bootstrap.yml from the control node.

![image](https://user-images.githubusercontent.com/44756128/114307208-42be1680-9aa4-11eb-82ea-b7c560a17276.png)
