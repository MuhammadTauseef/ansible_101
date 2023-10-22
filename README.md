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

```
ssh root@localhost -p <LOCAL PORT>
```

Where local port could be 2220 2221 and 2222 for ansible, system1 and system2 respectively.

