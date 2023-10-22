# Ansible Introduction 101

## Docker installation on windows step by step guide
https://www.youtube.com/watch?v=aCRMnDLnWmU

##  How to build this Dockerfile.
```
docker build -t alpine-ssh -f Dockerfile .
```

## How to run multiple containers
```
docker run -d -p 2220:22 --name ansible alpine-ssh
docker run -d -p 2221:22 --name system1 alpine-ssh
docker run -d -p 2222:22 --name system2 alpine-ssh
```


## How to connect to containers
root password is set as: **password**

From local machine
```
ssh root@localhost -p <LOCAL PORT>
```

From docker
```
docker exec -it <Continer NAME> bash
```
Where local port could be 2220 2221 and 2222 for ansible, system1 and system2 respectively.

Find ip addresses of both system1 and system2 
From ansible, connect to both system1 and system2 and accept fingerprint prompt by typing yes
```
ssh root@<system1 IP>
ssh root@<system2 IP>
```

## Generate keypairs and copy keys
On Ansible container
```
ssh-keygen -t ed25519 -C "default"
ssh-copy-id -i ~/.ssh/id_ed25519.pub <system1 IP>
ssh-copy-id -i ~/.ssh/id_ed25519.pub <system2 IP>

ssh-keygen -f ~/.ssh/ansible -t ed25519 -C "ansible"
ssh-copy-id -i ~/.ssh/ansible.pub <system1 IP>
ssh-copy-id -i ~/.ssh/ansible.pub <system2 IP>
```

## Connect with keys
From ansible container below connections should work now
```
Using default key
ssh <system1 IP>
ssh <system2 IP>

Or using ansible key
ssh -i ~/.ssh/ansible <system1 IP>
ssh -i ~/.ssh/ansible <system2 IP>
```

## Install Ansible and python

on Ansible container
```
apk update
apk add ansible
ansible --version
```

On system1 and system2
```
apk update
apk add python3
```

## Create inventory file
On Ansible container

```
echo "<system1 IP>" > inventory
echo "<system2 IP>" >> inventory
```

## Create docker network
After below from each docker container you can reach other containers with just container names i.e. ansible, system1 and system2
```
docker network create ansiblenet
docker network connect ansiblenet ansible
docker network connect ansiblenet system1
docker network connect ansiblenet system2

```
## Running ad hoc commands

### ping
```
ansible all --key-file ~/.ssh/ansible -i inventory -m ping
```

copy ansible.cfg file and above command will be reduced to below

```
ansible all -m ping
```

### gathering facts
```
ansible all --list-hosts
ansible all -m gather_facts
ansible all -m gather_facts --limit <system1 IP>
```


## Running elevated commands
apk for Alpine or apt for Ubuntu
```
ansible all -m apk -a update_cache=true --become --ask-become-pass
ansible all -m apk -a name=tcpdump --become --ask-become-pass
ansible all -m apk -a "name=tcpdump state=latest" --become --ask-become-pass
ansible all -m apk -a "upgrade=true" --become --ask-become-pass
```
