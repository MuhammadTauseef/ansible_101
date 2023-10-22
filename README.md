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