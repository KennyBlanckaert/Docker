# PPA deadsnakes does not yet support ubuntu:23.04
FROM ubuntu:22.04

# Run noninteractive
ENV DEBIAN_FRONTEND=noninteractive

# Set default shell to bash
RUN rm -rf /bin/sh && ln -s /bin/bash /bin/sh

# PPA deadsnakes: access latest Python version
RUN apt-get update -y && apt-get install -y nano vim software-properties-common && add-apt-repository ppa:deadsnakes/ppa

# Install python
RUN apt-get update -y && apt-get install -y python3.11 python3-pip sshpass curl git

# Add docker repository
RUN mkdir -m 0755 -p /etc/apt/keyrings && curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg 
RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install docker
RUN apt-get update -y && apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Create 'ansible' user
RUN useradd -ms /bin/bash ansible
USER ansible
WORKDIR /home/ansible

# Update PATH
ENV PATH="/home/ansible/.local/bin:${PATH}"

# Generate SSH keypair
RUN ssh-keygen -t rsa -f $HOME/.ssh/id_rsa -N ""

# Python dependencies
RUN python3.11 -m pip install --upgrade pip virtualenv

# Create virtualenv
RUN cd /home/ansible && mkdir -p ./{inventories,playbooks,collections,roles,vault} && virtualenv -p python3.11 ./

# Install ansible dependencies in virtualenv
RUN source bin/activate && python3.11 -m pip install --upgrade ansible molecule[ansible] molecule[docker] molecule[lint] docker docker-compose pywinrm && deactivate

# Enable virtualenv on every 'docker exec'
RUN echo "cd ~ && source bin/activate" >> .bashrc

# Run indefinitely
CMD /usr/bin/tail -f /dev/null
