Tested with: ![Docker](https://img.shields.io/badge/20.10-Docker-blue)   
Installs latest image binaries: ![Python](https://img.shields.io/badge/3.11-Python-9cf) ![Ansible](https://img.shields.io/badge/7.2-Ansible-red) ![Ansible Core](https://img.shields.io/badge/2.15-Ansible%20Core-orange)

&ensp;&ensp;  
# Deploy

To start using the ansible docker image, build it:
```
$ docker build -t ansible .
```

To use this image in an offline environment:
```
# Export it
$ docker save -o ansible.docker.tar.gz ansible

# Import it
$ docker load < ansible.docker.tar.gz
```

To deploy the image:
```
$ docker-compose up -d
```


&ensp;&ensp;  
# Usage

## Prepare Ansible

> Everything copied to the below folders is available in the docker container @/home/ansible

1. Copy Ansible Inventories into the *inventories* folder
2. Copy Ansible Playbooks into the *playbooks* folder
3. Copy Ansible Collections into the *collections* folder
4. Copy Ansible Roles into the *roles* folder
5. Copy Ansible Vault Keys into the *vault* folder

## Run Ansible

```
$ docker ps -a
$ docker exec -it <container_id> /bin/bash

# Always activate the virtual environment when running Ansible
ebo@<container_id>:~$ source bin/activate
ebo@<container_id>:~$ ansible-playbook -i <inventory> <playbook> [ --vault-id=<vault> ]
ebo@<container_id>:~$ deactivate
ebo@<container_id>:~$ exit
```

&ensp;&ensp;  
# Additional information

1. This docker image uses **virtualenv**. This avoids Python dependencies between different Python versions to conflict with eachother.  
   As Ubuntu 22.04 already uses the latest version of Python, this is actually not required, but it is a best practice.
2. **StrictHostKeyChecking** is enabled by default. To disable this, execute the follwing in the docker container:
    ```
    ebo@<container_id>:~$ export ANSIBLE_HOST_KEY_CHECKING=False
    ```
