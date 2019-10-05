# Installation

[Ansible] - Ansible Docs
Installing Ansible on server

```sh
$ sudo yum install ansible
$ sudo yum install git
```

### Setup Ansible Inventories

Add localhost to inventotry

```sh
$ sudo vim /etc/ansible/hosts
```

### Create Ansible User

```sh
$ su - ansible
$ sudo passwd <enter password>
```

### Configure SSH and Sudo for Ansible

> Worker Node (CentOS)

```sh
$ sudo su -
$ useradd ansible
$ passwd ansible
```

> Worker Node (Debian)

```sh
$ sudo su -
$ adduser ansible
```

> Control Node

```sh
$ su - ansible
$ ssh-keygen
$ ssh-copy-id <targetserver>
$ ssh-copy-id localhost
```

ssh <targetserver> to access other target servers.
Make ansible have privileges for sudo commands

```sh
$ logout
$ sudo visudo
```

ansible ALL=(ALL) NOPASSWD: ALL

---

# Connecting to EC2 Instance via Ansible

&nbsp;

```sh
$ sudo vim /etc/ansible/hosts
```

**_INSERT_**

```sh
localhost
ec2 ansible_host=<ip of ec2 instance>
ansible ec2 -m ping -u <user-name>
```

### Working with 'ssh-agent' to connect to <user-name>

key-pair is the .pem file associated with the ec2 instance

```sh
$ ssh-agent bash
$ chmod 600 <key-pair>.pem
$ ssh-add <key-pair>.pem
$ ansible ec2 -m ping -u <user-name>
```

### AWS Console Access

```sh
$ sudo pip install awscli
$ aws configure
```

### Store credentials in keys.yml file and encrypt with ansible vault

```sh
$ cat keys.yml
AWS_ACCESS_KEY_ID: test
AWS_SECRET_ACCESS_KEY: test2
AWS_REGION: us-east-1
$ ansible-vault encrypt /home/ansible/keys.yml
```

> When running encrypted file use flag --ask-vault-pass
> ansible-playbook --ask-vault-pass ec2-change-state.yml

### Test EC2 Connection with playbook

```sh
---  # connect to aws environment
- hosts: localhost
  vars_files:
    - /home/ansible/keys.yml
  tasks:
  - name: get ec2 instance list
    ec2_instance_facts:
      aws_access_key: '{{ AWS_ACCESS_KEY_ID }}'
      aws_secret_key: '{{ AWS_SECRET_ACCESS_KEY }}'
      region: '{{ AWS_REGION }}'
    register: output
  - name: display instance list
    debug:
      var: output.instances
```

[ansible]: https://docs.ansible.com/
