Tested with: ![Docker](https://img.shields.io/badge/20.10-Docker-blue)   
Installs latest image binaries: ![Python](https://img.shields.io/badge/3.11-Python-9cf) ![Ansible](https://img.shields.io/badge/7.3-Ansible-red) ![Ansible Core](https://img.shields.io/badge/2.14-Ansible%20Core-orange)

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
ansible@<container_id>:~$ ansible-playbook -i <inventory> <playbook> [ --vault-id=<vault> ]
ansible@<container_id>:~$ exit
```

&ensp;&ensp;  
# Additional information

1. This docker image uses **virtualenv**. This avoids Python dependencies between different Python versions to conflict with eachother.  
   As Ubuntu 22.04 already uses the latest version of Python, this is actually not required, but it is a best practice.
2. **StrictHostKeyChecking** is enabled by default. To disable this, execute the following in the docker container:
    ```
    ansible@<container_id>:~$ export ANSIBLE_HOST_KEY_CHECKING=False
    ```
   The same can be done using the *ansible.cfg* file
   ```
   [defaults]
   ...
   host_key_checking=False
   ```
3. When **installation of requirements** (using **`ansible-galaxy collection install -r ...`** ) is necessary, there are two options:
   1. Download the requirements from outside the container
      * Downloading the requirements within the collection
         * e.g. ```cd .../ansible/collections/<collection> && ansible-galaxy collection install -r requirements.yml -p $(pwd)```
         * Your SSH key should already be added to `git`
         * The *ansible_collections* folder will be created, which can be re-used in- and outside of the container
   2. Download the requirements from inside the container
      * Downloading the requirements within the collection.
         * e.g. ```cd ~/ansible/collections/<collection> && ansible-galaxy collection install -r requirements.yml -p $(pwd)```
         * Your container's SSH key should be added to `git` (see *~/.ssh/id_rsa.pub*)
         * The *ansible_collections* folder will be created, which can be re-used in- and outside of the container
         * **Permissions** of the volumes are important in this scenario
      * Downloading the requirements outside a collection, will download the dependencies in the *~/.ansible* by default, locally in the container only. Specify another location if necessary
         * e.g. ```cd ~/ansible/collections/<collection> && ansible-galaxy collection install -r requirements.yml -p ~/ansible/requirements```
         * This way, requirements can be re-used between collections/roles.
         * **Do not forget this folder when using Ansible in an offline environment** 

> **SOME NOTES**
>    
> 1. Downloading requirements offline is not possible.  
> Therefor, you should always make sure they're available in the *\<collection\>/ansible_collections* folder or ~/ansible/requirements folder.
> 2. **`roles_path`** and **`collections_paths`** are set the the *roles* and *collections* folders respectively. Both of them will also check the *requirements* folder.  
> The same can be done using environment variables too: **`ROLES_PATH`** and **`COLLECTIONS_PATHS`** 