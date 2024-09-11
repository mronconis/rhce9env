# RHCE V9
The repository aims to provide an environment for practice with Ansible.

## Pre-requisites

### Setup python virtualenv
Run command to create Python virtual environment:
```
virtualenv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

### Install Ansible dependencies
Before runnign the following command download collection `redhat.rhel_system_roles` from Red Hat Automation Hub (for details see: https://access.redhat.com/articles/3050101):
```
ansible-galaxy collection install -r requirements.yml -p .collections
```

## Start env
```
vagrant up --provision
```

### Run a specific playbook
```
ansible-playbook -i .vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory --limit <host/group> <playbook>
```

## Pause env
```
vagrant suspend
```

## Destroy env
```
vagrant destroy --force
```