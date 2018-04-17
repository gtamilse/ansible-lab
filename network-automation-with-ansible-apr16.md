
# **<p align="center">NETWORK AUTOMATION</p>**
# *<p align="center">with</p>*
# **<p align="center">ANSIBLE</p>**
---
# **<p align="center">Lab Guide</p>**

---
# Table of Contents

<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Lab Access](#lab-access)
	- [Topology](#topology)
	- [Lab access](#lab-access)
- [Ansible Concepts](#ansible-concepts)
	- [Verification](#verification)
	- [Configuration file](#configuration-file)
	- [Inventory file](#inventory-file)
	- [Ansible Modules](#ansible-modules)
	- [Playbooks](#playbooks)
- [Lab Answers](#lab-answers)

<!-- /TOC -->

---

# Lab Access
- Expected time to complete: 10 min.

## Topology
![topology](./images/ansible-lab-topo.png)

## Lab access
- Lab access involves two steps:
  - Step-1: VPN into lab VPN server
  - Step-2: SSH into the Ansible server

### VPN in
- Use Cisco AnyConnect Secure Mobility client
  - Make sure to **uncheck** "block connections to untrusted servers"
- Use the below details:
  - VPN server IP address:
  - Username:
  - Password:

### SSH into "your" Ansible server
- Find your Ansible server IP address
  - Refer to the Smartsheet that Manish sent.
- If your PC is MS Windows, use *putty* or some other ssh client
  - IP: `172.16.101.x`
  - User: `cisco`
  - Password: `cisco`
- If your PC is MAC, from terminal app: `$ ssh cisco@172.16.101.x`

### Ping your routers from Ansible server
- Find your Ansible server IP address
  - Refer to the Smartsheet that Manish sent.
- On Ansible server, from Ubuntu $ prompt, `$ ping <IOS router IP>`
- And, `$ ping <XR router IP>`
- You should be able to **ssh into Ubuntu server and ping R1 and R2** to advance to the next section.
- Review the section and discuss if you have any questions.


---

# Ansible Concepts
- Expected time to complete: 30 min.
- The following concepts are covered in this section:
  - Configuration file
  - inventory file
  - Ansible Modules
  - Ansible Playbooks

## Verification
- Verify Ansible installation. Execute the commands below:
  - `$ ansible --version`
  - `$ which ansible`
  - `$ ansible --help`

### Sample output

```
cisco@ansible-controller:~$ ansible --version
ansible 2.5.0
  config file = /etc/ansible/ansible.cfg
  configured module search path = [u'/home/cisco/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 2.7.6 (default, Oct 26 2016, 20:30:19) [GCC 4.8.4]
cisco@ansible-controller:~$ which ansible
/usr/bin/ansible
cisco@server-1:~$ ansible --help
Usage: ansible <host-pattern> [options]
:
Some modules do not make sense in Ad-Hoc (include, meta, etc)
cisco@server-1:~$
```
## Configuration file

- Find Ansible config file
  - `$ ansible --version`
  - This output points to `config file = /etc/ansible/ansible.cfg`
- Browse the config file and quickly go over different sections, denoted by []
  - `$ grep -v "#" /etc/ansible/ansible.cfg | grep -v ^$`
- In this section, we will edit 4 settings, under [default] section:
  - inventory
  - host_key_checking
  - timeout
  - retry_files_enabled
- Read the config file and find the default settings for the above parameters
  - `$ grep inventory /etc/ansible/ansible.cfg`
  - `$ grep host_key /etc/ansible/ansible.cfg`
  - `$ grep timeout /etc/ansible/ansible.cfg`
  - `$ grep retry_files_enabled /etc/ansible/ansible.cfg`
- Edit the config file
  - Use your favorite editing method to edit the file
  - Ubuntu inbuilt editors: vi, vim
  - Or, edit the file on your laptop and copy to Ansible server
  - Or, copy it from reference location
  	- http://172.16.101.93 (or),
	- `$ scp cisco@172.16.101.93:/var/www/html/file .`
  - The target config lines are already there in the file but are commented. You may add new lines or simple delete # at the beginning of the line.
- After editing, the config file will look like below.

### Sample output

```
[defaults]

inventory      = /etc/ansible/hosts

host_key_checking = False

timeout = 10

retry_files_enabled = False

cisco@ansible-controller:~$ grep -v "#" /etc/ansible/ansible.cfg | grep -v ^$

[defaults]
inventory      = /etc/ansible/hosts
host_key_checking = False
timeout = 10
retry_files_enabled = False

[inventory]
[privilege_escalation]
[paramiko_connection]
[ssh_connection]
[persistent_connection]
[accelerate]
[selinux]
[colors]
[diff]
```

- Review the config file subsection and discuss if you have any questions.

## Inventory file

- Edit your default inventory file: /etc/ansible/hosts
- Create two device groups: IOS and XR
- Assign the following variables to the devices: ansible_user=cisco ansible_ssh_pass=cisco
- Find out your IOS and XR router mgmt IP addresses. Plug them in the file below.
- For editing, you may use your favorite method.

### Example output
```
[IOS]
172.16.101.X ansible_user=cisco ansible_ssh_pass=cisco

[XR]
172.16.101.X ansible_user=cisco ansible_ssh_pass=cisco

[ALL:children]
IOS
XR
```

### Verification
- Read the contents of inventory file and verify accuracy: `$ cat /etc/ansible/hosts`
- List inventory groups:

```
$ ansible --list-hosts IOS
$ ansible --list-hosts XR
$ ansible --list-hosts ALL
```

### Example output:

```
cisco@ansible-controller:~$ ansible --list-hosts IOS
  hosts (1):
    172.16.101.91
cisco@ansible-controller:~$ ansible --list-hosts XR
  hosts (1):
    172.16.101.92
cisco@ansible-controller:~$ ansible --list-hosts ALL
  hosts (2):
    172.16.101.91
    172.16.101.92
cisco@ansible-controller:~$
```

- Review the inventory subsection and discuss if you have any questions.


## Ansible Modules

- Ansible ships with several modules.
- List installed modules:

```
$ ansible-doc -l
$ ansible-doc -l | wc -l
$ ansible-doc -l | grep ios_
$ ansible-doc -l | grep iosxr
```

### Using "raw" module
- What is raw module: Executes a low-down and dirty SSH command, not going through the module subsystem
- We can execute commands on remote devices, using Ansible Raw module.
- Ensure your server has Raw module: `$ ansible-doc -l | grep raw`
- Detailed documentations: `$ ansible-doc raw` (just browse; no research)
- Syntax: `ansible <devices> -m raw -a <command>`
  - -m = module name
  - -a = arguments

### Examples
- Execute a command on **your** routers (use correct IP address)

```
$ ansible 172.16.101.91 -m raw -a "sho ip interface brief"
```

- Execute a command on all routers in XR group
  - Remember that you created XR group in "inventory" subsection.

```
$ ansible XR -m raw -a "sho ip interface brief"
```
- Execute "show ip int br" on all routers

```
$ ansible ALL -m raw -a "sho ip interface brief"
```

- Review this subsection and discuss if you have any questions.

## Playbooks
- Playbook structure:
  - Playbook contains a list of plays.
  - Each "play", mainly has 2 sections: 1) play-level parameters and 2) one or more "tasks"
  - Each "tasks" section contains a list of modules.
  - Each "module" consists a list of actions (~commands).
  - All this is written in YAML format.
- Here is a typical structure:

```
---

- name: play-1 description
  play-1-level parameters

  tasks:
    - name: task-1 description
      module-1
        action-1
        action-2

    - name: task-2 description
      module-n
        action-1
        action-n
```

- Copy the below contents into a file called p1.yml

```
---
- name: play-1-output from IOS routers
  hosts: IOS
  gather_facts: no

  tasks:
    - raw:
        show ip route summary

      register: IOS_output

    - debug:
        var: IOS_output
```

### Executing playbooks
- After creating the playbook, it can be executed by using "`ansible-playbook`" command
- Execute the below:
  - `$ ansible-playbook p1.yml --syntax-check`
  - `$ ansible-playbook p1.yml --check`
  - `$ ansible-playbook p1.yml --step`
  - `$ ansible-playbook p1.yml`

> Quick read now, research later:
>
> "gather_facts: no"
> - By default, Ansible collects system information. This is not supported in our environment and hence disabled.
>
> "register"
> - Save the result from the module in a variable. In this case, we are saving "show ip.." output in IOS_output
> - Refer: http://docs.ansible.com/ansible/latest/playbooks_conditionals.html#register-variables
>
> "debug"
> - Debug module prints data
> - reference: http://docs.ansible.com/ansible/latest/debug_module.html


### Optional playbook
- Try this if you have any time left.
- The above playbook is similar to the one below; just another YAML representation.
- copy the below contents into a file and name it p2.yml

```
---
- name: play-1-output from IOS routers
  hosts: IOS
  gather_facts: no
  tasks:
    - raw: show ip route summary
      register: IOS_output
    - debug: var=IOS_output

- name: play-2-output from XR routers
  hosts: XR
  gather_facts: no
  tasks:
    - raw: show route summary
      register: XR_output
    - debug: var=XR_output
```

- Execute the playbook p2.yml:
  - `$ ansible-playbook p2.yml`
- Review this subsection and discuss if you have any questions.

---

# Lab Answers
- http://172.16.101.93

