# PPA deadsnakes does not yet support ubuntu:23.04
FROM ubuntu:22.04

# Run noninteractive
ENV DEBIAN_FRONTEND=noninteractive

# Set default shell to bash
RUN rm -rf /bin/sh && ln -s /bin/bash /bin/sh

# PPA deadsnakes: access latest Python version
RUN apt-get update -y && apt-get install -y software-properties-common && add-apt-repository ppa:deadsnakes/ppa

# Install python
RUN apt-get update -y && apt-get install -y python3.11 python3-pip sshpass

# Create 'ansible' user
RUN useradd -ms /bin/bash ansible
USER ansible
WORKDIR /home/ansible

# Update PATH
ENV PATH="/home/ansible/.local/bin:${PATH}"

# Python dependencies
RUN python3.11 -m pip install --upgrade pip virtualenv

# Create virtualenv
RUN cd /home/ansible && mkdir -p ./{inventories,playbooks,collections,roles,vault} && virtualenv -p python3.11 ./

# Install ansible dependencies in virtualenv
RUN source bin/activate && python3.11 -m pip install --upgrade ansible pywinrm && deactivate

# Run indefinitely
CMD /usr/bin/tail -f /dev/null
