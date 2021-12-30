

<div id="top"></div>

<div align="center"> <h1> Ansible for DevOps </h1>  
Summary of the book "Ansible for DevOps" by Jeff Geerlings 
	</div>
<p>
	<br>
</p>  





| Contents|
| ---------------------- |
| 1. [**Preface**](#preface) |
| 2. [**Introduction**](#introduction) |
| 3. [**Getting Started**](#getting-started) |
| 4. [**Local Infrastructure Developement**](#local-infrastructure) |
| 5. [**Ad-Hoc Commands**](#ad-hoc-commands) |
| 6. [**Ansible Playbooks**](#ansible-playbooks) |


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
Here we can configure, monitore, and manage the infrastructure without ever logging in to an individual server using Adhoc commands

Systems administrator has many tasks:

> Apply patches and updates via yum , apt , and other package managers.

> Check resource usage (disk space, memory, CPU, swap space, network).

> Check log files.

> Manage system users and groups.

> Manage DNS settings, hosts files, etc.

> Copy files to and from servers.

> Deploy applications or run application maintenance.

> Reboot servers.

> Manage cron jobs.


* Ansible allows admins to run ad-hoc commands on one or hundreds of machines
at the same time, using the ansible command.

* Example 

  * Create a vagrant file to install 2 applications and 1 database

  * Tell ansible about these servers through an inventory file

  * Servers can be managed by tools such as Munin, Nagios, Cacti, Hyperic, etc. Website or web application can be managed by Pingdom or Server Check. in.

  * `ansible multi -a "hostname"` Check the hostnames are configured properly. `-a` is to run against all hostnames. `-f 1` to tell Ansible to use only one fork (basically, to perform the command on each server in sequence)

  * `ansible multi -a "df -h"` : Check disk space availability

  * `ansible multi -a "free -m"` : make sure there is enough memory on our servers

  * `ansible multi -a "date"` : make sure the date and time on each server is in sync
  * To get an exhaustive list of all the environment details (‘facts’, in Ansible’s lingo) for a particular server (or for a group of servers), use the command `ansible [host-or-group] -m setup` . This will provide a list of every minute bit of detail about the server (including file systems, memory, OS, network interfaces… you name it, it’s in the list).

  * `ansible multi -b -m yum -a "name=chrony state=present"` : The software chrony can be installed on all servers at a time using adhoc command as given

  * `ansible multi -b -a "chronyc tracking"` : check to make sure our servers are synced closely to the official time on a time server

* Ansible Ad-Hoc commands comes more handy with *users* and *groups*

* One can create a group and add users with adhoc commands

* Commands :

  * `ansible app -b -m group -a "name=admin state=present"`: Add an admin group on the app servers for the server administrators remove a group by setting `state=absent` , set a group id with `gid=[gid]` , and indicate that the group is a system group with `system=yes` .

  * `ansible app -b -m user -a "name=johndoe group=admin createhome=yes"` : Add user and give him folder location. Add SSH key for new user `generate_ssh_key=yes`

  * `ansible app -b -m user -a "name=johndoe state=absent remove=yes"` : Delete the user

* Generic Packages can be installed across the servers using Debian, RHEL, Fedora, git ..

  * `ansible app -b -m package -a "name=git state=present"`

* Another use of adhoc commands is remote file management. Ansible
makes it easy to copy files from your host to remote servers, create directories, manage file and directory permissions and ownership, and delete files or directories.

  * `ansible multi -m stat -a "path=/etc/environment"` : Get information about a file. check a file’s permissions, MD5, or owner, use Ansible’s stat module

  * `ansible multi -m copy -a "src=/etc/hosts dest=/tmp/hosts"` : Ansible has more advanced file copy modules like rsync , most file copy operations can be completed with Ansible’s copy module. If you include a trailing slash, only the contents of the directory will be copied into the dest . If you omit the trailing slash, the contents and the directory itself will be copied into the dest. When you want to copy hundreds of files, especially in very deeply nested directory structures, you should consider either copying then expanding an archive of the files with Ansible’s unarchive module, or using Ansible’s synchronize or rsync modules.

  * `ansible multi -b -m fetch -a "src=/etc/hosts dest=/tmp"` : Retrieve files. The fetch module works almost exactly the same as the copy module, except in reverse.

  * `ansible multi -m file -a "dest=/tmp/test mode=644 state=directory"` : Create directories

  * `ansible multi -m file -a "src=/src/file dest=/dest/symlink state=link"` : Create symlink. The `src` is the symlink’s target file, and the `dest` is the path where the symlink itself
should be.

  * `ansible multi -m file -a "dest=/tmp/test state=absent"` : Delete directories

* Some updates run for hours and this can be executed in the background. 

  * `-B <seconds>` : the maximum amount of time (in seconds) to let the job run.

  * `-P <seconds>` : the amount of time (in seconds) to wait between polling the servers for an updated job status.

  * `ansible multi -b -B 3600 -P 0 -a "yum -y update"`: running in background

* For tasks you don’t track remotely, it’s usually a good idea to log the progress of the task somewhere, and also send some sort of alert on
failure—especially, for example, when running backgrounded tasks that
perform backup operations, or when running business-critical database
maintenance tasks.

* `ansible multi -b -a "tail /var/log/messages"` : Using ansible log files can be checked

* `ansible multi -b -m cron -a "name='daily-cron-all-servers' \
hour=4 job='/path/to/daily-script.sh'"` : ansible can be advised to run cron job on servers every once in a while

* `ansible multi -b -m cron -a "name='daily-cron-all-servers' \
state=absent"` : Cron job can be deleted by using the following command


* Git updates can be rolled into the servers using ansible

  * `ansible app -b -m git -a "repo=git://example.com/path/to/repo.git dest=/opt/myapp update=yes version=1.2.4"` : it checkout to the application’s new version branch,

  * `ansible app -b -a "/opt/myapp/update.sh"` Then, run the application’s update.sh shell script

## **Ansible Playbooks** ##
Playbooks are written in YAML⁴⁹, a simple human-readable syntax popular for defining configuration. Playbooks may be included within other playbooks, and certain metadata and options cause different plays or playbooks to be run in different scenarios on different servers.

Below is an example and explanation!



![image](/images/ansible1.png)

* The commands are explained as below:

  * The first line, `---` , is how we mark this document as using YAML syntax

  * The second line, `- hosts: all` tells Ansible to run the play on all hosts that it knows about

  * The third line, become: yes tells Ansible to run all the commands through sudo, so the commands will be run as the root user.
  * The fifth line, `tasks:` , tells Ansible that what follows is a list of tasks to run as part of this play.
  * The first task begins with `name: Install Apache`. it’s a way of giving a human-readable description to the task that follows.

  * Ansible allows lists of variables to be passed into tasks using `with_items :` Define a list of items and each one will be passed into the play, referenced using the item variable `(e.g. {{ item }} )`.

  * In this case, we are using a list of items containing dicts (dictionaries) used for variable substitution; to define each element in a list of dicts with each list item in the format:
  `var1: value`, `var2: value`

  * `ansible-playbook playbook.yml --list-hosts` : List all the available hosts
