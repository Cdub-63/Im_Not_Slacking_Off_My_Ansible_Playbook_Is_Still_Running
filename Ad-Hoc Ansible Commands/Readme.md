![image](https://user-images.githubusercontent.com/44756128/113512660-434a3080-952b-11eb-830d-dc711a880895.png)

# The Scenario
Some consultants will be performing audits on a number of systems in our company's environment. We've got to create the user accounts listed in /home/ansible/userlist.txt and set up the provided public keys for their accounts. The security team has built a jump host for the consultants to access production systems and provided us with the full key-pair so we can set up and test the connection. All hosts in dbsystems will need that public key installed so the consultants may use key-pair authentication to access the systems. We must also ensure the auditd service is enabled and running on all systems.

Important notes:
  - Ansible is already on the control node. If we connect to the server by clicking on the Public IP address in a web browser, we need to make sure we change to the ansible user, with the sudo su - ansible command.
  - The user ansible is present on all servers with appropriate shared keys for access to managed servers from the control node. We need to make sure we use this user to complete the commands.
  - The default Ansible inventory has already been configured with the appropriate hosts and groups.
  - /etc/hosts entries are present on the control1 host for the managed servers.

# Get Logged In
Log into the control1 server, become the ansible user (su - ansible) and we can get going.

![image](https://user-images.githubusercontent.com/44756128/113512907-907ad200-952c-11eb-9711-4db74763c25c.png)

# Create the User Accounts Noted in /home/ansible/userlist.txt
If we read the userlist.txt file in our home directory, we'll see consultant and supervisor. Those are the two new user accounts we have to create:

![image](https://user-images.githubusercontent.com/44756128/113512896-822cb600-952c-11eb-864d-61289da5f1e5.png)

[ansible@control1]$ ansible dbsystems -b -m user -a "name=consultant"

[ansible@control1]$ ansible dbsystems -b -m user -a "name=supervisor"\

![image](https://user-images.githubusercontent.com/44756128/113512944-bdc78000-952c-11eb-83c5-5c5e2654ea7b.png)

# Place Key Files in the Correct Location, /home/$USER/.ssh/authorized_keys, on Hosts in dbsystems
[ansible@control1]$ ansible dbsystems -b -m file -a "path=/home/consultant/.ssh state=directory owner=consultant group=consultant mode=0755"

![image](https://user-images.githubusercontent.com/44756128/113512975-e2bbf300-952c-11eb-84f0-9d1fe7bedfb7.png)

[ansible@control1]$ ansible dbsystems -b -m copy -a "src=/home/ansible/keys/consultant/authorized_keys dest=/home/consultant/.ssh/authorized_keys mode=0600 owner=consultant group=consultant"

![image](https://user-images.githubusercontent.com/44756128/113513010-1434be80-952d-11eb-9cbf-453412abdb4c.png)

[ansible@control1]$ ansible dbsystems -b -m file -a "path=/home/supervisor/.ssh state=directory owner=supervisor group=supervisor mode=0755"

![image](https://user-images.githubusercontent.com/44756128/113513030-29a9e880-952d-11eb-8415-27f2ef68311b.png)

[ansible@control1]$ ansible dbsystems -b -m copy -a "src=/home/ansible/keys/supervisor/authorized_keys dest=/home/supervisor/.ssh/authorized_keys mode=0600 owner=supervisor group=supervisor"

![image](https://user-images.githubusercontent.com/44756128/113513040-3c242200-952d-11eb-9cbe-9d8599a4a2b6.png)

# Ensure auditd Is Enabled and Running on All Hosts
[ansible@control1]$ ansible all -b -m service -a "name=auditd state=started enabled=yes"

![image](https://user-images.githubusercontent.com/44756128/113513086-7ab9dc80-952d-11eb-8603-7cfdd7b0f51c.png)

![image](https://user-images.githubusercontent.com/44756128/113513091-87d6cb80-952d-11eb-954a-615d3f6442f9.png)

![image](https://user-images.githubusercontent.com/44756128/113513095-91f8ca00-952d-11eb-9277-9c3d3e1b85d7.png)


