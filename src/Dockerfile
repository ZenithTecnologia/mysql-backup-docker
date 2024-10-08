FROM registry.access.redhat.com/ubi9/python-312:1 AS fetch
LABEL creator=tess@ten7.com
LABEL mantainer=dev@zenithtecnologia.com.br

# Update the package list and install Ansible.
RUN pip install ansible-core

# Copy the Ansible configuration files
COPY ansible-hosts /etc/ansible/hosts
COPY ansible.cfg /etc/ansible/ansible.cfg
COPY ansible /ansible

USER root

# Fix environment for flightdeck ubi compatibility
RUN ln -s /usr/sbin/groupadd /usr/sbin/addgroup

# Run the build.
RUN ansible-galaxy install -fr /ansible/requirements.yml
RUN ansible-playbook -i /ansible/inventories/backup.yml /ansible/build.yml

# Switch to the service account.
USER backup
WORKDIR /home/backup

# Set the entrypoint and default command.
CMD ["mysql-backup"]
