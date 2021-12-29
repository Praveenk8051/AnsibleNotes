# AnsibleNotes

Summary of the book "Ansible for DevOps" by Jeff Geerlings



| Contents|
| ---------------------- |
| 1. [**Prefeace**](#preface) |
| 2. [**Introduction**](#introduction) |
| 3. [**Getting Started**](#getting-started) |
| 4. [**Local Infrastructure Developement**](#local-infrastructure) |
| 5. [**Ad-Hoc Commands**](#ad-hoc-commands) |


## **Preface** ##
* DevOps is meant to shorten the distance between the developers
writing the code, and the operators running the application.

* Vagrant is an excellent tool for managing local virtual machines to mimic real-world infrastructure locally (or in the cloud), and Ansible — the subject of this book — is an excellent tool for provisioning servers, managing their configuration, and deploying applications, even on my local workstation!


## **Introduction** ##
* As data centers grew, and hosted applications became more complex, administrators realized they couldn’t scale their manual systems management as fast as the applications they were enabling. That’s why server provisioning and configuration management tools came to flourish.

* Ansible is extensible with modules written in a programming language you already know

## **Getting Started** ##
* *snowflake servers*— servers that are uniquely configured and impossible to recreate from scratch because they were hand-configured without documentation

* **Idempotence** is the ability to run an operation which produces the same result whether run once or multiple times

### Inventory File ###
* Ansible uses an inventory file (basically, a list of servers) to communicate with  servers. Like a hosts file (at /etc/hosts ) that matches IP addresses to domain names, an Ansible inventory file matches servers (IP addresses or domain names) to groups

* If ort 22 for SSH on the server is not used, you will need to add it to the address, like www.example.com:2222, since Ansible defaults to port 22

```ansible -i hosts.ini example -m ping -u [username]```

[username] is the user you use to log into the server. Ansible assumes you’re using passwordless (key-based) login for SSH. If you insist on using passwords, add the --ask-pass ( -k ) flag to Ansible commands (you may also need to install the sshpass package for this to work).

```ansible -i hosts.ini example -a "free -h" -u [username]```

`free -h` (to see memory statistics), `df -h` (to see disk usage statistics)



## **Local Infrastructure Development - Ansible and Vagrant** ##
* For speedier testing and development of Ansible playbooks, and for testing in general, it’s a very good idea to work locally. Local development and testing of infrastructure is both safer and faster than doing it on remote/live machines—especially in production environments

* *cowboy coding* — working directly in a production environment, not
documenting or encapsulating changes in code, and not having a way to
roll back to a previous version.

* **Vagrant**, a server provisioning tool, and **VirtualBox**, a local virtualization environment, make a potent combination for testing infrastructure and individual server configurations locally. Both applications are free and open source, and work well on Mac, Linux, or Windows hosts.

* Commands:

```vagrant box add geerlingguy/centos7```

```vagrant init geerlingguy/centos7```

```vagrant up```

Vagrant uses Ruby syntax 

Vagrant downloaded a pre-built 64-bit CentOS 7 virtual machine image (you can build your own virtual machine ‘boxes’, if you so desire), loaded the image into VirtualBox with the configuration defined in the default Vagrantfile (which is now in the folder you created earlier), and booted the virtual machine.

Managing this virtual server is extremely easy: ``vagrant halt`` will shut down the VM, ``vagrant up`` will bring it back up, and ``vagrant destroy`` will completely delete the machine from VirtualBox. A simple ``vagrant up`` again will re-create it from the base box you originally downloaded.

Now that you have a running server, you can use it just like you would any other server, and you can connect via SSH. To connect, enter ``vagrant ssh`` from the folder where the Vagrantfile is located. If you want to connect manually, or connect from another application, enter ``vagrant ssh-config`` to get the required SSH details.

```vagrant provision``` : When `vagrant provision` (or vagrant up the first time), Vagrant passes off the VM to Ansible, and tells Ansible to run a defined Ansible playbook.

* Multi-machine management: Vagrant is able to configure and control mul-
tiple VMs within one Vagrantfile.

## **Ad-Hoc Commands**