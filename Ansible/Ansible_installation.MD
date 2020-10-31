# Ansible Installation

Ansible is an open-source automation platform. It is very, very simple to set up and yet powerful. Ansible can help you with configuration management, application deployment, task automation.

### Pre-requisites

1. An AWS EC2 instance (on Control node)

### Installation steps:
#### on Amazon EC2 instance

1. Install python and python-pip
   ```sh
   yum install python
   yum install python-pip
   ```
1. Install ansible using pip check for version
    ```sh
    pip install ansible
   ansible --version
   ```
   
1. Create a user called ansadmin (on Control node and Managed host)  
   ```sh
   useradd ansadmin
   passwd ansadmin
   ```
1. Below command grant sudo access to ansadmin user. But we strongly recommended using "visudo" command if you are aware vi or nano editor.  (on Control node and Managed host)
   ```sh
   echo "ansadmin ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
   ```
   
1. Log in as a ansadmin user on master and generate ssh key (on Control node)
   ```sh 
   sudo su - ansadmin
   ssh-keygen
   ```
2. login into your docker host as root user
   ```
   [root@ip-172-31-39-218 ~]# useradd ansadmin
   [root@ip-172-31-39-218 ~]# passwd ansadmin
   
   get ip addr from docker machine and login from ansible
   
   docker> ip addr (take 2: ehth0 - private ip)
   ansible try ssh into docker machine > and you will be able to login without password
   
   [ec2-user@ip-172-31-11-34 ~]$ su - ansadmin
   Password:
   Last login: Sat Oct 31 03:51:06 UTC 2020 on pts/0
   [ansadmin@ansible-control-node ~]$ ssh 'ansadmin@172.31.39.218'

   
1. Copy keys onto all ansible managed hosts (on Control node)
   ```sh 
   ssh-copy-id ansadmin@<target-server>
   ```

2. Ansible server used to create images and store on docker registry. Hence install docker, start docker services and add ansadmin to the docker group. 
   ```sh
   yum install docker
   
   # start docker services 
   service docker start
   service docker start 
   
   # add user to docker group 
   usermod -aG docker ansadmin
   
2. By default user auth is not enabled in Ec2 instance so we should edit password auth to no to y in /etc/ssh/sshd_config/ search PasswordAuthentication no- change from no to yes

Reload -   service sshd reload

3. to switch to amsadmin account
su - ansadmin

4. To change hostname from command prompt   > hostname ansible-control-node

5. from ansible host do below to make sure u are able to ping docker host


[root@ansible-control-node /]# mkdir /etc/ansible

create a file /etc/ansible/hosts and put- take ip address of docker host and paste it

[root@ansible-control-node ~]# cat hosts
172.31.39.218
localhost

now do ansible ping from ansible host

>> ansible all -m ping

you are able to ping as u see sucess

172.31.39.218 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}

   ```
   

1. Create a directory /etc/ansible and create an inventory file called "hosts" add control node and managed hosts IP addresses to it. 
 
### Validation test

   
1. Run ansible command as ansadmin user it should be successful (Master)
   ```sh 
   ansible all -m ping
   ```
