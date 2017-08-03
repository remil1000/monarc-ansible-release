# Ansible playbook for MONARC deployement

This playbook is used to deploy the whole MONARC architecture in accordance to
the figure below.

![MONARC architecture](images/monarc-architecture.png "MONARC architecture")


## Requirements

* install Python 2 on all servers. Actually ansible 2.2 features only a tech
  preview of Python 3 support;
* [ansible](https://www.ansible.com/) must be installed on the configuration
  server. We have tested with version 2.2.1.0 of ansible.


## Usage

    $ sudo apt-get install ansible
    $ git clone https://github.com/monarc-project/ansible-ubuntu.git
    $ cd ansible-ubuntu/playbook
    $ ansible-playbook -i ../inventory/ monarc.yaml --user ansible -k -K

*-k -K* forces the SSH authentication by simple password. In this case
*sshpass* must be installed on the configuration server.

However, it is strongly recommended to use a SSH key associated to a user
dedicated to ansible. The *ansible* user must be created on each servers.
In this case, run the following command:

    $ ansible-playbook -i ../inventory/ monarc.yaml --user ansible --ask-sudo-pass


### Tips

* create an user named *ansible* on each server;
* from the configuration server: ssh-copy-id ansible@IP-OF-BO/FO/RPX
* add the IP of the BO,FO,RPX in the file /etc/hosts of the configuration server;
* add the *ansible* user in the groups:
 * sudo: _sudo usermod -aG sudo ansible_
 * www-data: _sudo usermod -aG www-data ansible_


### Notes

1. In the file __inventory/hosts__:

* the section *dev* is for the FO;
* the section *master* is for the BO;
* master in the section *dev:vars* is the ip/domain of the BO;
* publicHost in the section *dev:vars* is the ip/domain of the RPX.

2. Adding an attribute for the ansible inventory is done with the command:

    $ ssh monarc@IP-OF-THE-BO sudo -u www-data /usr/local/bin/new_monarc_clients.sh | ./ansible-ubuntu/playbook/add_inventory.py

The command above should be launched on the configuration server with cron.


## Roles

There are three roles, described below.

### monarcco

Common tasks for the front and the back-office.

### monarcbo

[Backoffice](https://github.com/monarc-project/MonarcAppBO).
Only one per environment (dev, preprod, prod...).

### monarcfo

[Frontoffice](https://github.com/monarc-project/MonarcAppFO).
Can be multiple installation per environment to balance to the load.


## Python scripts

The `add_inventory.py` and `del_inventory.py` scripts are used to dynamically
edit the inventory files of the configuration server.
