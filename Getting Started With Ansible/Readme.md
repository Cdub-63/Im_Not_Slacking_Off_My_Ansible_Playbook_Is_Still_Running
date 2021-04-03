![image](https://user-images.githubusercontent.com/44756128/113488442-c87a0a80-9483-11eb-8e61-3a5541b995f9.png)

# Getting Started with Ansible
We'll install Ansible on a control node and configure two managed servers for use with Ansible. We will also create a simple inventory and run an Ansible command to verify our configuration is correct.

# Install Ansible on the Control Node
Log in to the control node using ssh, cloud_user, and the provided public IP address and password:

ssh username@PUBLIC IP

To install Ansible on the control node:

sudo yum install ansible

![image](https://user-images.githubusercontent.com/44756128/113488650-fdd32800-9484-11eb-9401-000e08dd3b80.png)


# Configure the ansible User on the Control Node
Next, we'll configure the ansible user on the control node for ssh shared key access to managed nodes.
Note: Do not use a passphrase for the key pair.

Create a key pair for the ansible user on the control host, accepting the defaults when prompted:

sudo su - ansible

ssh-keygen

Copy the public key to both node1 and node2:

ssh-copy-id node1

ssh-copy-id node2

![image](https://user-images.githubusercontent.com/44756128/113488701-4ee31c00-9485-11eb-9c8d-ed9886664f69.png)

# Create a Simple Ansible Inventory
Next, we'll create a simple Ansible inventory on the control node in /home/ansible/inventory containing node1 and node2.

On the control host:

sudo su - ansible

touch /home/ansible/inventory

echo "node1" >> /home/ansible/inventory

echo "node2" >> /home/ansible/inventory

![image](https://user-images.githubusercontent.com/44756128/113488765-a5505a80-9485-11eb-8557-168f5821a7db.png)

# Configure sudo Access for Ansible
Now, we'll configure sudo access for Ansible on node1 and node2 such that Ansible may use sudo for any command with no password prompt.

Log into node1 and edit the sudoers file to contain appropriate access for the ansible user:

ssh username@PUBLIC IP

sudo visudo

![image](https://user-images.githubusercontent.com/44756128/113488821-0e37d280-9486-11eb-9e8a-057e99ad3192.png)

Add the following line to the file and save:

ansible    ALL=(ALL)       NOPASSWD: ALL

![image](https://user-images.githubusercontent.com/44756128/113488812-011ae380-9486-11eb-9310-c825d7a69cb9.png)

Enter:

logout

Repeat these steps for node2, and then back out to the control node.

# Verify Each Managed Node Is Accessible
Finally, we'll verify each managed node is able to be accessed by Ansible from the control node using the ping module.

Redirect the output of a successful command to /home/ansible/output.

To verify each node, run the following as the ansible user from the control host:

ansible -i /home/ansible/inventory node1 -m ping

ansible -i /home/ansible/inventory node2 -m ping

![image](https://user-images.githubusercontent.com/44756128/113488873-6ec70f80-9486-11eb-8f49-24a7088cbd67.png)

To redirect output of a successful command to /home/ansible/output:

ansible -i /home/ansible/inventory node1 -m ping > /home/ansible/output

![image](https://user-images.githubusercontent.com/44756128/113488912-a930ac80-9486-11eb-84b7-e7b970020395.png)

