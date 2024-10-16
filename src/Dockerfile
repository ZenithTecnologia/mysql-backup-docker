FROM registry.access.redhat.com/ubi9/python-312:1 AS build
LABEL creator=tess@ten7.com
LABEL mantainer=dev@zenithtecnologia.com.br

ENV CNB_GROUP_ID=
ENV CNB_USER_ID=
ENV HOME=

USER root
WORKDIR /root

RUN dnf repoquery podman-catatonit --location | tail -n1 | xargs rpm -Uvh --nodeps && \
    dnf remove -y scl-utils && \
    dnf clean all && \
    rm -rf /var/cache/yum

RUN userdel -fr default

# Update the package list and install Ansible.
RUN pip install ansible-core

# Copy the Ansible configuration files
COPY ansible-hosts /etc/ansible/hosts
COPY ansible.cfg /etc/ansible/ansible.cfg
COPY ansible /ansible

# Run the build.
RUN ansible-galaxy install -fr /ansible/requirements.yml
RUN ansible-playbook -i /ansible/inventories/backup.yml /ansible/build.yml

# Switch to the service account.
USER backup
WORKDIR /
ENV HOME=/home/backup

ENTRYPOINT ["/usr/libexec/catatonit/catatonit", "--"]
# Set the entrypoint and default command.
CMD ["mysql-backup"]
