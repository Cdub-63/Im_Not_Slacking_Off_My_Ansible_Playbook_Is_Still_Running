![image](https://user-images.githubusercontent.com/44756128/113514214-f702ee80-9532-11eb-95ff-774e1af05c31.png)

# The Scenario
Our company has been increasing the deployment of small brochure-style websites for clients. The head of IT has decided that each client should have their own web server, for better client isolation, and has tasked us with creating concept automation to quickly deploy web-nodes with simple static website content.

We have been provided an Ansible control node (control1) and 2 test lab servers (node1 and node2) that have been preconfigured with the ansible user and key.

We must create an Ansible inventory in /home/ansible/inventory containing a host group named web. The web group should contain node1 and node2.

Then we've got to design an Ansible playbook that will execute the following tasks on your configured inventory:
  - Install httpd
  - Start and enable the httpd service
  - Install a simple website provided on a repository server.

The playbook will be in /home/ansible/web.yml. The simple website may be accessed from http://repo.example.com/website.tgz.

# Get Logged In
Log into the control1 server, become the ansible user (su - ansible) and we can get going.

![image](https://user-images.githubusercontent.com/44756128/113514382-c5d6ee00-9533-11eb-8ce2-091189466e2c.png)

# Create an inventory in /home/ansible/inventory That Contains a Host Group Named web. The web Group Should Contain node1 and node2
Use Vim or echo to create the inventory file:

[ansible@control1]$ echo "[web]" >> /home/ansible/inventory

![image](https://user-images.githubusercontent.com/44756128/113514406-f585f600-9533-11eb-8751-b07d9a07c6a8.png)

![image](https://user-images.githubusercontent.com/44756128/113514443-4a297100-9534-11eb-9fd2-d64bbc1cc003.png)

It should contain this when we're done:

[web]

node1

node2

![image](https://user-images.githubusercontent.com/44756128/113514458-5f060480-9534-11eb-800b-3c32e9c18c77.png)

# Create a Playbook in /home/ansible/web.yml
Using Vim, we'll create our web.yml file with these contents:

---
- hosts: web
  become: yes
  tasks:
    - name: install httpd
      yum: name=httpd state=latest
    - name: start and enable httpd
      service: name=httpd state=started enabled=yes
    - name: retrieve website from repo
      get_url: url=http://repo.example.com/website.tgz dest=/tmp/website.tgz
    - name: install website
      unarchive: remote_src=yes src=/tmp/website.tgz dest=/var/www/html/
 
![image](https://user-images.githubusercontent.com/44756128/113514472-79d87900-9534-11eb-8e9b-93bc6e94219a.png)

![image](https://user-images.githubusercontent.com/44756128/113514501-a7252700-9534-11eb-9160-b2c42cb55cbd.png)

![image](https://user-images.githubusercontent.com/44756128/113514529-c459f580-9534-11eb-9da6-88d3175bdccd.png)
      
# Verify the Work by Executing the Playbook Using the Inventory
[ansible@control1]$ansible-playbook -i /home/ansible/inventory /home/ansible/web.yml

![image](https://user-images.githubusercontent.com/44756128/113514572-16028000-9535-11eb-8757-8ff996e0bc51.png)

# Conclusion
This new setup is everything we needed. The Ansible playbook installs httpd, starts and enables it, and creates a simple website, all on it's own web node. That's what we needed.
