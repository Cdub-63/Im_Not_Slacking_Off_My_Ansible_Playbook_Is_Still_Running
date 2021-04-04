![image](https://user-images.githubusercontent.com/44756128/113513332-dc2e7b00-952e-11eb-806e-7864bc8ca7a4.png)

# The Scenario
Our company has decided the backup software license is frivolous and unnecessary, and didn't renew the license. Our supervisor has created a simple script and an Ansible playbook to create an archive of select files, depending on pre-defined Ansible host groups, as a stop-gap measure. We will create the inventory file to complete the backup strategy.

We have to accomplish the following:
- Create the inventory file in /home/ansible/inventory.
- Configure the host group media to contain media1 and media2.
- Define the following variables for media with their accompanying values:
  - media_content should be set to /var/media/content/.
  - media_index should be set to /opt/media/mediaIndex.
- Configure the host group webservers to contain the hosts web1 and web2.
- Define the following variables for webservers with their accompanying values:
  - httpd_webroot should be set to /var/www/.
  - httpd_config should be set to /etc/httpd/.
- Define the variable script_files specifically for web1. The value of script_files should be set to /usr/local/scripts.

# Get Logged In
Log into the control1 server, become the ansible user (su - ansible) and we can get going.

Note that there a couple ways to the tasks. One way is by appending things to files with echo, the other is to edit the file with an actual editor (like Vim or Nano).

# Create the inventory File in /home/ansible/
[ansible@control1]$ touch /home/ansible/inventory

![image](https://user-images.githubusercontent.com/44756128/113513368-054f0b80-952f-11eb-801e-3e66c7d594ba.png)

# Configure the Host Group media to Contain media1 and media2
[ansible@control1]$ echo "[media]" >> /home/ansible/inventory

[ansible@control1]$ echo "media1" >> /home/ansible/inventory

[ansible@control1]$ echo "media2" >> /home/ansible/inventory

If were going the editor route, we just need to make sure the file looks like this when we're done:

[media]

media1

media2

![image](https://user-images.githubusercontent.com/44756128/113513417-42b39900-952f-11eb-9c9d-46a358495e08.png)

# Define Variables for media with Their Accompanying Values
[ansible@control1]$ mkdir /home/ansible/group_vars

[ansible@control1]$ touch /home/ansible/group_vars/media

These will populate that new media file:

[ansible@control1]$ echo "media_content: /var/media/content/" >> /home/ansible/group_vars/media

[ansible@control1]$ echo "media_index: /opt/media/mediaIndex" >> /home/ansible/group_vars/media

The media file should look like this when we're done:

media_content: /var/media/content/

media_index: /opt/media/mediaIndex

![image](https://user-images.githubusercontent.com/44756128/113513500-ac33a780-952f-11eb-988c-0b29fdf938a9.png)

# Configure the Host Group webservers to Contain the Hosts web1 and web2
[ansible@control1]$ echo "[webservers]" >> /home/ansible/inventory

[ansible@control1]$ echo "web1" >> /home/ansible/inventory

[ansible@control1]$ echo "web2" >> /home/ansible/inventory

Once we've made changes, it will look like this:

[media]

media1

media2


[webservers]

web1

web2

![image](https://user-images.githubusercontent.com/44756128/113513547-e604ae00-952f-11eb-9235-83802fbbd3a5.png)

# Define Variables for webservers with Their Accompanying Values
[ansible@control1]$ touch /home/ansible/group_vars/webservers

[ansible@control1]$ echo "httpd_webroot: /var/www/" >> /home/ansible/group_vars/webservers

[ansible@control1]$ echo "httpd_config: /etc/httpd/" >> /home/ansible/group_vars/webservers

Finished, it will look like this:

httpd_webroot: /var/www/

httpd_config: /etc/httpd/

![image](https://user-images.githubusercontent.com/44756128/113513630-4267cd80-9530-11eb-82f5-08ba7ea92a51.png)

# Define the Variable script_files Specifically for web1, Setting Its Value to /usr/local/scripts
[ansible@control1]$ mkdir /home/ansible/host_vars

[ansible@control1]$ touch /home/ansible/host_vars/web1

[ansible@control1]$ echo "script_files: /usr/local/scripts" >> /home/ansible/host_vars/web1

This file should just have the script_files: /usr/local/scripts line in it when we're done.

![image](https://user-images.githubusercontent.com/44756128/113513675-8529a580-9530-11eb-981a-d5acc2118501.png)

# Testing
Let's run the backup script to see if what we've done works:

[ansible@control1]$ cd ~

[ansible@control1]$ ./scripts/backup.sh

![image](https://user-images.githubusercontent.com/44756128/113513714-a9858200-9530-11eb-81a9-b79b5a0368b8.png)
