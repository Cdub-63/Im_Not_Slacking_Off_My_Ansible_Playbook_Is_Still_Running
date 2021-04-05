![image](https://user-images.githubusercontent.com/44756128/113487527-391e2880-947e-11eb-9f8f-fa286b42ff5b.png)

Ok, we are starting our Ansible journey! For this deployment we will need two VMs. One to be the workstation host and the other the control host.

Use Securecrt, putty or you local computer's command line utility to SSH into the servers.

# Install Ansible on the Control Host
Log in to the Control Host server using ssh:
```sh
ssh username@Server_IP
```

Install epel-release and enter y when prompted:
```sh
sudo yum install epel-release
```

![image](https://user-images.githubusercontent.com/44756128/113487614-c3668c80-947e-11eb-8885-32b163774b74.png)

Install Ansible and enter y when prompted:
```sh
sudo yum install ansible
```

![image](https://user-images.githubusercontent.com/44756128/113487634-d8432000-947e-11eb-80b5-41ff311bad8a.png)

# Create an ansible User
Now, we need to create the ansible user on both the control host and workstation host.

On the control node:
```sh
sudo useradd ansible
```

![image](https://user-images.githubusercontent.com/44756128/113487666-07599180-947f-11eb-8544-6bd0c0d1f7c4.png)

Connect to the workstation node using ssh:
```sh
e.x. ssh [<username>]@[<Server_IP>]
```

Add the ansible user to the workstation and set a password for the ansible user. We need to make sure it is something we will remember:
```sh
sudo useradd ansible
sudo passwd ansible
logout
```

![image](https://user-images.githubusercontent.com/44756128/113487767-85b63380-947f-11eb-9947-eec9f338d998.png)

# Configure a Pre-Shared Key for Ansible
With our user created, we need to create a pre-shared key that allows the user to log in from control to workstation without a password.

Change to the ansible user:
```sh
sudo su - ansible
```

Generate a new SSH key, accepting the default settings when prompted:
```sh
ssh-keygen
```

Copy the SSH key to workstation, providing the password we created earlier:
```sh
ssh-copy-id workstation
```

Test that we no longer need a password to log in to the workstation (may also be a public IP if dns/host files are not setup):
```sh
ssh workstation
```

Once we succeed at logging in, log out of workstation:
```sh
logout
```

![image](https://user-images.githubusercontent.com/44756128/113487870-1ee54a00-9480-11eb-9cc9-0b28aee34c56.png)

# Configure the Ansible User on the Workstation Host
Our next job is to configure the ansible user on the workstation host so that that they may sudo without a password.

Log in to the workstation using ssh:
```sh
e.x. ssh [<username>]@[<Server_IP>]
```

Edit the sudoers file:
```sh
sudo visudo
```

![image](https://user-images.githubusercontent.com/44756128/113488022-10e3f900-9481-11eb-82cf-eafea278e7ec.png)

Add this line at the end of the file:
```sh
ansible       ALL=(ALL)       NOPASSWD: ALL
```

Save the file:
```sh
:wq
```

![image](https://user-images.githubusercontent.com/44756128/113488010-fe69bf80-9480-11eb-8d3a-04e20891b59e.png)

Log out of workstation:
```sh
logout
```

# Create a Simple Inventory
Next, we need to create a simple inventory, /home/ansible/inventory, consisting of only the workstation host.

On the control host, as the ansible user, run the following commands:
```sh
vim /home/ansible/inventory
```

![image](https://user-images.githubusercontent.com/44756128/113488052-50aae080-9481-11eb-806b-e992145e8e46.png)

Add the text "workstation" to the file and save using :wq in vim.

![image](https://user-images.githubusercontent.com/44756128/113488097-94054f00-9481-11eb-9708-515994ead66b.png)

# Write an Ansible Playbook
We need to write an Ansible playbook into /home/ansible/git-setup.yml on the control node that installs git on workstation, then executes the playbook.

On the control host, as the ansible user, create an Ansible playbook:
```sh
vim /home/ansible/git-setup.yml
```

![image](https://user-images.githubusercontent.com/44756128/113488139-c747de00-9481-11eb-8b3f-f11893de5939.png)

Add the following text to the file:
```yml
--- # install git on target host
- hosts: workstation
  become: yes
  tasks:
  - name: install git
    yum:   
      name: git     
      state: latest
```

Save and exit the file (:wq in vim).

![image](https://user-images.githubusercontent.com/44756128/113488168-f4948c00-9481-11eb-9d47-6d123431e739.png)

Run the playbook:
```sh
ansible-playbook -i /home/ansible/inventory /home/ansible/git-setup.yml
```

![image](https://user-images.githubusercontent.com/44756128/113488208-358ca080-9482-11eb-9399-6fa6b62a6385.png)

Verify that the playbook ran successfully:
```sh
ssh workstation
which git
```

![image](https://user-images.githubusercontent.com/44756128/113488235-5654f600-9482-11eb-9fd1-b7194bee041c.png)

We now have a better understanding of how to deploy Ansible, create a connection between two nodes, create a simple inventory, and how to write an Ansible playbook.
