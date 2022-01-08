

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
| 7. [**Beyond Basics**](#beyond-basics) |
| 8. [**Playbook Organization - Roles, Includes, and Imports**](#playbook-organisation) |
| 9. [**Ansible Plugins and Content Collections**](#ansible-plugins) |




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

* If port 22 for SSH on the server is not used, you will need to add it to the address, like `www.example.com:2222`, since Ansible defaults to port 22

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

* Some of the command definitions:

    * `register` creates a new variable, *xyz* , to be used in the next play to determine when to run the play. register stashes the output (stdout, stderr) of the defined command in the variable name passed to it.

    * `changed_when` tells Ansible explicitly when this play results in a change to the server.

    * `var_files`: Using one or more included variable files cleans up your main playbook file, and lets you organize all your 
    configurable variables in one place
    
    * `pre_tasks`: Ansible lets you run tasks before or after the main tasks or roles  using `pre_tasks` and `post_tasks`, respectively

    * `handlers` : are special kinds of tasks you run at the end of a play by adding the `notify` option to any of the tasks in that group. The handler will only be called if one of the tasks notifying the handler makes a change to the server and it will only be notified at the end of the play. To call the handler from a task, add the option `notify: restart` apache to any task
in the play

    * `lineinfile` module does a simple task: ensures that a particular line of text exists (or doesn’t exist) in a file. First, we tell `lineinfile` the location of the file, in the `dest` parameter. Then, we give a regular expression (Python-style) to define what the line looks like. Next, we tell  `lineinfile` exactly how the resulting line should look. Finally, we explicitly `state` that we want this line to be present.




* By default, Ansible will stop all playbook execution when a task fails, and won’t notify any handlers that may need to be triggered. In some cases, this leads to unintended side effects. If you want to make sure handlers always run after a task uses notify to call the handler, even in case of playbook failure, add `--force-handlers` to your ansible-playbook command.

## **Beyond Basics** ##

### **Handlers**

* `handlers` are used to restart the a software. `tasks` can be used along with `notify` option to tell `handler` to restart

* Some cases, one may have to notify multiple `handlers` or `handlers` may have to notify additional `handlers`

* To notify multiple `handlers` from one task, use a list for the `notify` option

![image](/images/ansible2.png)

To have one `handler` notify another, add a `notify` option onto the `handler`—`handlers` are basically glorified `tasks` that can be called by the `notify` option, but since they act as `tasks` themselves, they can chain themselves to other `handlers`

![image](/images/ansible3.png)

• `Handlers` will only be run if a `task` notifies the `handler`; if a task that would’ve notified the handlers is skipped due to a `when` condition or something of the like, the `handler` will not be run.

• `Handlers` will run once, and only once, at the end of a play. If you absolutely need to override this behavior and run handlers in the middle of a playbook, you can use the `meta` module to do so (e.g. - `meta: flush_handlers` ).

• If the play fails on a particular `host` (or all hosts) before handlers are notified, the handlers will never be run. If it’s desirable to always run handlers, even after the playbook has failed, you can use the `meta` module as described above as a separate task in the playbook, or you use the command line flag `--force-handlers` when running your playbook. Handlers won’t run on any hosts that became unreachable during the playbook’s run.


### **Environment Variables**
* Set some environment variables for your remote user account, you
can do that by adding lines to the remote user’s `.bash_profile`

![image](/images/ansible4.png)

* It’s recommended you use a task’s `register` option to store the environment variable in a variable Ansible can use later

* In any case, it’s pretty simple to manage environment variables on the server with `lineinfile` . If your application requires many environment variables, you might consider using  `copy` or `template` with a local file instead of using `lineinfile` with a large list of items


### **Playbook Variables**

* `ansible-playbook example.yml --extra-vars "foo=bar"`: Variables can be passed in via the command line, when calling `ansible-playbook` ,with the `--extra-vars` option

* You can also pass in extra variables using quoted JSON, YAML, or even by passing a JSON or YAML file directly, like `--extra-vars "@even_more_vars.json"` or `--extra-vars "@even_more_vars.yml`

* Variables may be included inline with the rest of a playbook, in a `vars` section

* Variables may also be included in a separate file, using the `vars_files` section

* One can conditionally include variables files using `include_vars`


### **Inventory variables**
Variables may also be added via Ansible inventory files, either `inline` with a host definition, or after a `group`


![image](/images/ansible5.png)

* If you need to define more than a few variables, especially variables that apply to more than one or two hosts, inventory files can be cumbersome. In fact, Ansible’s
documentation recommends not storing variables within the inventory. Instead, you can use `group_vars` and `host_vars` YAML variable files within a specific path, and Ansible will assign them to individual hosts and groups defined in your inventory

### **Registered Variables**

* In Ansible, any play can `register` a variable, and once registered, that variable will be available to all subsequent tasks.

* There are many times that you will want to run a command, then use its return code, `stderr`, or `stdout` to determine whether to run a later task. For these situations, Ansible allows you to use register to store the output of a particular command in a variable at runtime.



### **Facts**
* If you don’t need to use facts, and would like to save a few seconds per-host when running playbooks (this can be especially helpful when running an Ansible playbook against dozens or hundreds of servers), you can set `gather_facts: no` in your playbook


### **Ansible Vault - Keeping secrets secret**
* It’s better to treat passwords and sensitive data specially, and
there are two primary ways to do this:
   * Use a separate secret management service, such as `Vault` by `HashiCorp`,`Keywhiz` by Square, or a hosted service like `AWS’s Key Management Service` or `Microsoft Azure’s Key Vault`.
   * Use `Ansible Vault`, which is built into Ansible and stores encrypted passwords and other sensitive data alongside the rest of your playbook.



* Ansible Vault 
   * You take any YAML file you would normally have in your playbook (e.g. a variables file, host vars, group vars, role default vars, or even task includes!), and store it in the vault.
   * Ansible encrypts the vault (‘closes the door’), using a key (a password you set).
   * You store the key (your vault’s password) separately from the playbook in a location only you control or can access.
   * You use the key to let Ansible decrypt the encrypted vault whenever you run your playbook.
   * `ansible-vault encrypt vars/api_key.yml` : To encrypt the file with Vault, run

  * `ansible-playbook main.yml --ask-vault-pass
Vault password:` Password can also be provided at runtime.

  * `ansible-playbook main.yml --vault-password-file ~/.ansible/\
vault_pass.txt` : Create the file `∼/.ansible/vault_pass.txt` with your password in it, set permissions to 600 , and tell Ansible the location of the file when you run the playbook



### **Variable Precendence**
* Variable Precendence*
   * --extra-vars passed in via the command line (these always win, no matter
what).
   * Task-level vars (in a task block).
   * Block-level vars (for all tasks in a block).
   * Role vars (e.g. [role]/vars/main.yml ) and vars from include_vars module.
   * Vars set via set_facts modules.
   * Vars set via register in a task.
   * Individual play-level vars: 1. vars_files 2. vars_prompt 3. vars
   * Host facts.
   * Playbook host_vars .
   * Playbook group_vars .
   * Inventory: 1. host_vars 2. group_vars 3. vars
   * Role default vars (e.g. `[role]/defaults/main.yml` )



### **If/then/when conditionals**

* Jinja supports conditionals, math operations and checks to perform
*  For the few cases where Jinja doesn’t provide enough power and flexibility, you can invoke Python’s built-in library functions (like string.split , [number].is_signed() ) to manipulate variables and determine whether a given task should be run, resulted in a change, failed, etc
* It’s generally best to stick with simpler Jinja filters and variables, but it’s nice to be able to use Python when you’re doing more advanced variable manipulation.
 

 **when**
 * *when* can be used as boolean

 * **changed_when** and **failed_when**
 *register* variable is used to store intermediate result and check if the variable is *changed_when* or *failed_when* to process further results

**ignore_errors**
* to avoid stopping ansible-playbook from execution

**Delegation**
* Ansible allows any task to be delegated to a particular host using *delegate_to*

**Local Action**
* Ansible has a convenient shorthand you can use, *local_action*,instead of adding the entire *delegate_to* line

**wait_for**:
* *wait for* a freshly booted server or application to start listening on a particular port

* *wait_for* can be used to pause your playbook execution to wait for many different things:

  * Using *host* and *port*, wait a maximum of *timeout* seconds for the port to be available (or not).

  * Using *path* (and *search_regex* if desired), wait a maximum of timeout  seconds for the file to be present (or absent).

  * Using *host* and *port* and *drained* for the *state* parameter, check if a given port has drained all it’s active connections.

  * Using *delay*, you can simply pause playbook execution for a given amount of time (in seconds).


* `ansible-playbook test.yml --connection=local` : speed's up the excution process by removing the SSH connection overhead


**vars_prompt**: Used in yml file. Let's user excute the *password* or *network* details
* There are a few special options you can add to prompts:
  * *private* : If set to yes , the user’s input will be hidden on the command line.
  * *default* : You can set a default value for the prompt, to save time for the end
user.
  * *encrypt / confirm / salt_size* : These values can be set for passwords so you can verify the entry (the user will have to enter the password twice if confirm is set to yes ), and encrypt it using a salt (with the specified size and crypt scheme).

* It’s preferable to use role or playbook variables, inventory variables, or even local environment variables, to maintain complete automation of the playbook run

**Tags**
* Tags allow you to run (or exclude) subsets of a playbook’s tasks.
* You can tag roles, included files, individual tasks, and even entire plays
* You will need to find a tagging style that suits your needs and lets you run (or not run) the specific parts of your playbooks you desire

**Blocks**
* Blocks allow you to group related tasks together and apply particular task parameters on the block level.

* They also allow you to handle errors inside the blocks in a way similar to most programming languages’ exception handling.

![image](/images/ansible6.png)

* *block* can be executed with *rescue* and *always*. 

## **Playbook Organization - Roles, Includes, and Imports** ##
* Ansible is flexible when it comes to organizing tasks in more efficient ways so you can make playbooks more maintainable, reusable, and powerful

### **Imports** ###
* Basic way of importing, when *vars_files* was used to place variables into a separate *vars.yml* file instead of inline with the playbook

* In the tasks: section of your playbook, you can add import_tasks directives like so

![image](/images/ansible7.png)

### **Includes** ###
* If you need to have included tasks that are dynamic that is, they need to do different things depending on how the rest of the playbook runs—then you can use *include_tasks* rather than *import_tasks*

![image](/images/ansible8.png)

**Dynamic Inlcudes**
* Until Ansible 2.0, *includes* were always processed when your playbook run started (just like *import_tasks* behaves now), so you couldn’t do things like load a particular include when some condition was met. Ansible 2.0 and later evaluates includes during playbook execution, so you can do something like the following

![image](/images/ansible9.png)

**Playbook Imports**
* create playbooks to configure all the servers in your infrastructure,
then create a master playbook that includes each of the individual playbooks.

![image](/images/ansible10.png)


**General**

* Most of the time, it’s best to start with a monolithic playbook while you’re working on the setup and configuration details, then move sets of tasks out to included files after you start seeing logical groupings

### **Roles** ###
* Using includes and Ansible roles organizes Playbooks and makes them maintainable
* There are only two directories required to make a working Ansible role:

![image](/images/ansible11.png)

* If you create a directory structure like the one shown above, with a *main.yml* file in each directory, Ansible will run all the tasks defined in *tasks/main.yml* if you call the role from your playbook using the following syntax

![image](/images/ansible12.png)
 
* Your roles can live in a couple different places: the default global Ansible role path *(configurable in /etc/ansible/ansible.cfg)*, or a roles folder in the same directory as your main playbook file

* `ansible-galaxy role init role_name` : Running this command creates an example role in the current working directory, which you can modify to suit your needs. Using the init command also ensures the role is structured correctly in case you want to someday contribute the role to
Ansible Galaxy

* *meta*: file which tells about the meta information like dependencies.

* *pre-tasks*: Can be used for firewall configuration

* Main playbook can consist of order *pre_tasks*, *roles* and *tasks*


**More flexibility with role vars and defaults**
* When running a role’s tasks, Ansible picks up variables defined in a role’s *vars/main.yml* file and *defaults/main.yml*, but will allow your playbooks to override the defaults or other role-provided variables if you want.

* **Variable precedence**: Note that Ansible handles variables placed in included files in *defaults* with less precedence than those placed in *vars*. If you have certain variables you need to allow hosts/playbooks to easily override, you should probably put them into *defaults*. If they are common variables that should almost always be the values defined in your role, put them into *vars*

**Other role parts: handlers, files, and templates**
* You can store *handlers* directly inside a *main.yml* file inside a role’s handlers directory
* You can call handlers defined in a role’s handlers folder just like those included directly in your playbooks (e.g. *notify: restart apache*)

**Files and Templates**
* when copying a file directly to the server, add the filename or the full path from within a role’s files directory, like so

![image](/images/ansible13.png)


* Similarly, when specifying a template, add the filename or the full path from within a role’s templates directory, like so:

![image](/images/ansible14.png)

* The copy module copies files from within the module’s files folder, and the template module runs given template files through the Jinja templating engine, merging in any variables available during your playbook run before copying the file to the server.

**Organizing more complex and cross-platform roles**
* As a rule of thumb, I keep my playbook and role task files under 100 lines of YAML if at all possible

### **Ansible Galaxy**

* Ansible roles are powerful and flexible; they allow you to encapsulate sets of configuration and deployable units of playbooks, variables, templates, and other files, so you can easily reuse them across different servers

* People could share roles for commonly-installed applications and services

* Ansible Galaxy, or just ‘Galaxy’, is a repository of community-contributed Ansible
content

* Galaxy offers the ability to add, download, and rate roles. One of the primary functions of the ansible-galaxy command is retrieving roles
from Galaxy. Roles must be downloaded before they can be used in playbooks

**Using role requirements files to manage dependencies**
* Define *requirements.yml* to download roles from Ansible Galaxy,
GitHub, an HTTP download, BitBucket, or your own repository
* `ansible-galaxy install -r requirements.yml` : To install the roles defined in a requirements file, use the following command

**Ansible Commands**
* Some other helpful ansible-galaxy commands you might use from time to time:
  * `ansible-galaxy role list`:  displays a list of installed roles, with version
numbers
  * `ansible-galaxy role remove [role]` removes an installed role
  * `ansible-galaxy role init` can be used to create a role template suitable for
submission to Ansible Galaxy


### **Ansible Plugins and Content Collections**

* Ansible Collections allow for easier distribution of Ansible content plugins, modules, and roles and have also helped to make Ansible’s own maintenance more evenly distributed

* Ansible plugins—Python code to extend Ansible’s functionality with new modules,filters, inventory plugins

* Adding this kind of content to a role is not ideal, and in a sense, overloads the role by putting both Python code and Ansible YAML into the same entity

* This is why, in Ansible 2.8, Collections, or more formally, Content Collections, were introduced.

* Collections allow the gathering of Ansible plugins, roles, and even playbooks into one entity, in a more structured way that Ansible, Ansible Galaxy, and Automation Hub can scan and consume.

* Collections allow the gathering of Ansible plugins, roles, and even playbooks into one entity, in a more structured way that Ansible, Ansible Galaxy, and Automation Hub can scan and consume.

* Check-out `https://docs.ansible.com/ansible/latest/dev_guide/developing_collections.html`


* `ansible-galaxy install -r requirements.yml` : Mention collection in `yml` file and before running your playbook that uses the collection, install all the required collections with ansible-galaxy

* Version constraints can be specified in `requirements.yml`

* By default collections are installed at :

   * ∼/.ansible/collections
   * /usr/share/ansible/collections

* But you can override the setting in your own projects by setting the `ANSIBLE_-COLLECTIONS_PATH` environment variable, or setting collections_path in an `ansible.cfg` file alongside your playbook.