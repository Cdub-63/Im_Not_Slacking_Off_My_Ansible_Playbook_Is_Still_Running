![image](https://user-images.githubusercontent.com/44756128/113579083-69cea100-95e9-11eb-8f2a-e4974522f975.png)

# The Scenario
We have to set up automation to pull down a data file, from a notoriously unreliable third-party system, for integration purposes. Create a playbook that attempts to pull down http://apps.l33t.com/transaction_list to localhost. The playbook should gracefully handle the site being down by outputting the message "l33t.com appears to be down. Try again later." to stdout. If the task succeeds, the playbook should write "File downloaded." to stdout. No matter if the playbook errors or not, it should always output "Attempt completed." to stdout.

If the report is collected, the playbook should write and edit the file to replace all occurrences of #BLANKLINE with a line break \n.

Tasks list summary:
  - Create a playbook, /home/ansible/report.yml.
  - Configure the playbook to download http://apps.l33t.com/transaction_list to /home/ansible/transaction_list on localhost and output "File downloaded." to stdout.
  - Configure the playbook to handle connection failure by outputting "l33t.com appears to be down. Try again later." to stdout.
  - Configure the playbook to output "Attempt Completed" to stdout, whether it was successful or not.
  - Configure the playbook to replace all instances of #BLANKLINE with the line break character \n.
  - Run the playbook using the default inventory to verify whether things work or not.

Important notes:
  - For convenience, Ansible has been installed on the control node.
  - The user ansible already exists on all servers, with appropriate shared keys for access to the necessary servers from the control node.
  - All necessary Ansible inventories have already been created.
  - apps.l337.com is unavailable by default.
  - We may force a state change by running /home/ansible/scripts/change_l33t.sh.
 
# Logging In
Log into the control node (control1) as the ansible user.

![image](https://user-images.githubusercontent.com/44756128/113581546-e3b45980-95ec-11eb-8ce5-0e11b9a354e2.png)

# Create a playbook: /home/ansible/report.yml
Create the file with an echo command:
```sh
[ansible@control1]$ echo "---" >> /home/ansible/report.yml
```

Using a text editor, such as vim, edit /home/ansible/report.yml
```sh
[ansible@control1]$ vim /home/ansible/report.yml
```

# Configure the Playbook to Download a File and Output a Message
First, we'll specify our host and tasks (name, and debug message):

```yml
---
- hosts: localhost
  tasks:
    - name: download transaction_list
      get_url:
        url: http://apps.l33t.com/transaction_list
        dest: /home/ansible/transaction_list
    - debug: msg="File downloaded"
```
![image](https://user-images.githubusercontent.com/44756128/113581776-33932080-95ed-11eb-8f73-82c7ad5af276.png)

# Reconfigure the Playbook to Handle Connection Failure by Outputting a Message
We need to reconfigure a bit here, adding a block keyword and a rescue, in case the URL we're reaching out to is down:

```yml
---
- hosts: localhost
  tasks:
    - name: download transaction_list
      block:
        - get_url:
            url: http://apps.l33t.com/transaction_list
            dest: /home/ansible/transaction_list
        - debug: msg="File downloaded"
      rescue:
        - debug: msg="l33t.com appears to be down.  Try again later."
```
![image](https://user-images.githubusercontent.com/44756128/113581925-66d5af80-95ed-11eb-8e5d-d8b84545e48c.png)

# Configure the Playbook to Output a Message Whether It Was Successful or Not
An always block here will let us know that the playbook at least made an attempt to download the file:

```yml
---
- hosts: localhost
  tasks:
    - name: download transaction_list
      block:
        - get_url:
            url: http://apps.l33t.com/transaction_list
            dest: /home/ansible/transaction_list
        - debug: msg="File downloaded"
      rescue:
        - debug: msg="l33t.com appears to be down.  Try again later."
      always:
        - debug: msg="Attempt completed."
```

![image](https://user-images.githubusercontent.com/44756128/113582107-95ec2100-95ed-11eb-9e21-b22dd0d0511b.png)

# Configure the Playbook to Replace All Instances of #BLANKLINE with the Line Break Character \n
We can use the replace module for this task, and we'll sneak it in between the get_url and first debug tasks.

```yml
---
- hosts: localhost
  tasks:
    - name: download transaction_list
      block:
        - get_url:
            url: http://apps.l33t.com/transaction_list
            dest: /home/ansible/transaction_list
        - replace:
            path: /home/ansible/transaction_list
            regexp: "#BLANKLINE"
            replace: '\n'
        - debug: msg="File downloaded"
      rescue:
        - debug: msg="l33t.com appears to be down.  Try again later."
      always:
        - debug: msg="Attempt completed."
```

![image](https://user-images.githubusercontent.com/44756128/113582327-d9df2600-95ed-11eb-9a3f-be6d4b4d9f66.png)

# Verify Configuration by Running the Playbook
We can run the playbook with this:
```sh
[ansible@control1]$ ansible-playbook /home/ansible/report.yml
```

![image](https://user-images.githubusercontent.com/44756128/113582457-0430e380-95ee-11eb-8feb-6a2111072c59.png)

If all went well, we can read the downloaded text file:
```sh
[ansible@control1]$ cat /home/ansible/transaction_list
```

![image](https://user-images.githubusercontent.com/44756128/113582569-23c80c00-95ee-11eb-8cc9-edf7b3833431.png)

The file looks ok. Let's read the original, up where it sits on l33t.com:
```sh
[ansible@control1]$ curl apps.l33t.com/transaction_list
```

![image](https://user-images.githubusercontent.com/44756128/113582652-40644400-95ee-11eb-875c-287f210f1f47.png)

We'll see instances of #BLANKLINE there that our playbook actually turned into new lines.

Now we'll test to see how gracefully we deal with errors. We'll shut l33t.com down:
```sh
[ansible@control1]$ ./scripts/change_l33t.sh
```

![image](https://user-images.githubusercontent.com/44756128/113582709-53771400-95ee-11eb-945d-3f69d709954b.png)

Then we can run our playbook again:
```sh
[ansible@control1]$ ansible-playbook /home/ansible/report.yml
```

![image](https://user-images.githubusercontent.com/44756128/113582796-6db0f200-95ee-11eb-95f2-b84b9497b1ab.png)

In the output, we see that the get_url task failed, but that the playbook did not stop executing. It outputted all of the appropriate messages.

# Conclusion
We successfully created a playbook that downloads a file from a URL, and errors out gracefully when something (like the URL is down) goes awry.
