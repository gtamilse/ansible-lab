
# **<p align="center">NETWORK AUTOMATION</p>**
# *<p align="center">with</p>*
# **<p align="center">ANSIBLE</p>**
---
# **<p align="center">Lab Guide</p>**

---
# Table of Contents

<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [**<p align="center">NETWORK AUTOMATION</p>**](#p-aligncenternetwork-automationp)
- [*<p align="center">with</p>*](#p-aligncenterwithp)
- [**<p align="center">ANSIBLE</p>**](#p-aligncenteransiblep)
- [**<p align="center">Lab Guide</p>**](#p-aligncenterlab-guidep)
- [Table of Contents](#table-of-contents)
- [Lab Access](#lab-access)
	- [Topology](#topology)
	- [Lab access](#lab-access)
		- [VPN connect](#vpn-connect)
		- [SSH into "your" Ansible server](#ssh-into-your-ansible-server)
		- [Ping your routers from Ansible server](#ping-your-routers-from-ansible-server)
- [Ansible Concepts](#ansible-concepts)
	- [Verification](#verification)
		- [Sample output](#sample-output)
	- [Configuration file](#configuration-file)
		- [Sample output](#sample-output)
	- [Inventory file](#inventory-file)
		- [Example output](#example-output)
		- [Verification](#verification)
		- [Example output:](#example-output)
	- [Ansible Modules](#ansible-modules)
		- [Using "raw" module](#using-raw-module)
		- [Examples](#examples)
	- [Playbooks](#playbooks)
		- [Executing playbooks](#executing-playbooks)
		- [Optional playbook](#optional-playbook)
- [Basic Playbooks](#basic-playbooks)
	- [Raw module](#raw-module)
	- [IOS and IOS XR Command modules](#ios-and-ios-xr-command-modules)
		- [IOS Command module](#ios-command-module)
		- [IOS XR Command module](#ios-xr-command-module)
	- [IOS and IOS XR Config modules](#ios-and-ios-xr-config-modules)
		- [IOS Config module](#ios-config-module)
		- [IOS XR Config module](#ios-xr-config-module)
	- [Variables](#variables)
		- [Homework excercise](#homework-excercise)
	- [Conditionals](#conditionals)
	- [Loops](#loops)
- [Automating Network Operations tasks](#automating-network-operations-tasks)
	- [Exercise 1 - Configure OSPF on all routers](#exercise-1-configure-ospf-on-all-routers)
	- [Exercise 2 - Automate router running-config backups](#exercise-2-automate-router-running-config-backups)
- [Run Ansible Playbook rtr-cfg-bkup everyday at 5:15 am UTC to backup router configs](#run-ansible-playbook-rtr-cfg-bkup-everyday-at-515-am-utc-to-backup-router-configs)
	- [Exercise 3 - Create snapshot tool](#exercise-3-create-snapshot-tool)
	- [Exercise 4 - Ansible Vault](#exercise-4-ansible-vault)
- [Copy paste the contents of the inventory file into this vi editor.](#copy-paste-the-contents-of-the-inventory-file-into-this-vi-editor)
- [Lab Files](#lab-files)

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

### VPN connect
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
---

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

---

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

---

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

---

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

# Basic Playbooks
- Expected time to complete: 45mins
- This section covers the following:
  - Raw module
  - Commands module (IOS and IOSXR)
  - Config module (IOS and IOSXR)
  - Variables
  - Conditionals
  - Loops

## Raw module
- In the last section, you used "raw" module in Ansible command line. Here, we are going to use it in a playbook.
- Goal: Collect "show ip route summary" output from all routers in the group, IOS.
- Create a playbook with the contents below:

```
cisco@ansible-controller:~$ cat basic_raw.yml

---
- name: show ip route summary from IOS devices
  hosts: IOS
  gather_facts: false

  tasks:
    - name: exec CLI using raw module
      raw:
        sho ip route summary

      register: IOS_output

    - name: print output
      debug:
        var: IOS_output
```
- Execute: `$ ansible-playbook basic_raw.yml --syntax-check`
- Execute: `$ ansible-playbook basic_raw.yml`

---

## IOS and IOS XR Command modules
- The module names are: **ios_command** and **iosxr_command**

### IOS Command module
- Goal: Capture time and interface list from routers in IOS group
- Create a playbook with the contents below:

```
cisco@ansible-controller:~$ cat basic_ios_cmd.yml

---
- name: IOS Module Router Config
  hosts: IOS
  gather_facts: false
  connection: local

  tasks:
    - name: Collect Router time and intf list
      ios_command:
         authorize: yes
         commands:
           - show clock
           - show ip int brief

      register: OUTPUT

    - name: print output
      debug: var=OUTPUT.stdout_lines
```
- Execute: `$ ansible-playbook basic_ios_cmd.yml --syntax-check`
- Execute: `$ ansible-playbook basic_ios_cmd.yml`

### IOS XR Command module

- Goal: Capture time and interface list from routers in XR group
- Create a playbook with the contents below:

```
cisco@ansible-controller:~$ cat basic_xr_cmd.yml

---
- name: IOS Module Router Config
  hosts: XR
  gather_facts: false
  connection: local

  tasks:
    - name: Collect Router time and intf list
      iosxr_command:
        commands:
          - show clock
          - show ip int brief

      register: OUTPUT

    - name: print output
      debug: var=OUTPUT.stdout_lines
```

- Execute: `$ ansible-playbook basic_xr_cmd.yml --syntax-check`
- Execute: `$ ansible-playbook basic_xr_cmd.yml`

---

## IOS and IOS XR Config modules
- The module names are: ios_command and iosxr_command

### IOS Config module
- Goal: Configure interface loopback1 and assign an IP address on all routers in IOS group

```
cisco@ansible-controller:~$ cat basic_ios_config.yml

---
- name: IOS Module Router Config
  hosts: IOS
  gather_facts: false
  connection: local

  tasks:
    - name: Configure Interface Setting
      ios_config:
        parents: "interface loopback 1"
        lines:
          - "description test"
          - "ip address 1.1.1.1 255.255.255.255"
```
- Execute: `$ ansible-playbook basic_ios_config.yml --syntax-check`
- Execute: `$ ansible-playbook basic_ios_config.yml`

### IOS XR Config module
- Goal-1: Configure interface loopback1 and assign an IP address on all routers in XR group

```
cisco@ansible-controller:~$ cat basic_xr_config.yml

---
- name: XR Module Router Config
  hosts: XR
  gather_facts: false
  connection: local

  tasks:
    - name: Configure Interface Setting
      iosxr_config:
        parents: "interface loopback 1"
        lines:
          - "description test"
          - "ip address 1.1.1.2 255.255.255.255"
```
- Execute: `$ ansible-playbook basic_xr_config.yml --syntax-check`
- Execute: `$ ansible-playbook basic_xr_config.yml`
- :
- **Goal-2**: Create loopback1, assign an IP address and display its config on all routers in XR group
- Create one playbook with two tasks. Task-1 to configure loopback-1 and task-2 to read the running config.

```
cisco@ansible-controller:~$ cat basic_xr_config_show.yml

---
- name: XR Module Router Config
  hosts: XR
  gather_facts: false
  connection: local

  tasks:
    - name: Configure Interface Setting
      iosxr_config:
        parents: "interface loopback 1"
        lines:
          - "description test"
          - "ip address 1.1.1.2 255.255.255.255"

    - name: read loopback1 intf config
      iosxr_command:
        authorize: yes
        commands:
          - show run int loop1

      register: OUTPUT

    - name: print output
      debug: var=OUTPUT.stdout_lines

```
- Execute: `$ ansible-playbook basic_xr_config_show.yml --syntax-check`
- Execute: `$ ansible-playbook basic_xr_config_show.yml`

---

## Variables

- Goal: display interface config of loopback1. Use "variables", so that this playbook can be easily modified for other interfaces.
- Copy the below contents to a playbook file.


```
cisco@ansible-controller:~$ cat basic_var.yml

---
- name: play-1-output from IOS routers
  hosts: XR
  gather_facts: false
  connection: local
  vars:
    INTF: loopback1

  tasks:
    - name: read config
      iosxr_command:
        commands:
          show run int {{INTF}}

      register: DATA

    - name: print output
      debug: var=DATA.stdout_lines
```
- Execute: `$ ansible-playbook basic_var.yml --syntax-check`
- Execute: `$ ansible-playbook basic_var.yml`

### Homework excercise
- Copy basic_xr_config_show.yml to basic_xr_config_show_var.yml
- Edit basic_xr_config_show_var.yml. Use variables so that INT=loopback1.
- The benefit with this playbook should be: it can be modified to create and read loopback2 by simply editing variables section.

---


## Conditionals
- Goal: Scan the invemtory and find if a router is running IOS or XR

```
cisco@ansible-controller:~$ cat cond_router_os.yml

---
- name: Verify Router is running IOS-XE
  hosts: ALL
  gather_facts: false

  tasks:
    - name: Collect Router Version
      raw: show version

      register: SHVER

    - name: conditional task to verify if router is an IOS XE router
      when: SHVER.stdout | join('') is search('IOS XE')
      debug: msg="{{inventory_hostname}} is an IOS XE Router."

    - name: conditional task to verify if router is an IOS XR router
      when: SHVER.stdout | join('') is search('IOS XR')
      debug: msg="{{inventory_hostname}} is an IOS XR Router."
```

> For later research:
> - debug module has a parameter called, msg. In the earlier examples, we used debug/var.
> - More info at: http://docs.ansible.com/ansible/latest/modules/debug_module.html


---

## Loops
- Goal: Execute several commands, using loops

```
cisco@ansible-controller:~$ cat basic_loops.yml

---
- name: Backup IOS-XE Config
  hosts: IOS
  gather_facts: false
  connection: local

  tasks:
    - name: Collect Router Version and interface brief
      ios_command:
         authorize: yes
         commands: "{{ item }}"

      register: DATA

      with_items:
           - show clock
           - show ip int bri

    - debug: var=DATA
```

> - Refer to http://docs.ansible.com/ansible/devel/user_guide/playbooks_loops.html

---


# Automating Network Operations tasks

This section contains 4 exercises; in each exercise you will create Ansible playbooks to automate a certain network operations task.

   1. Configure OSPF on all Routers
   2. Automate router running-config backups
   3. Create snapshot tool
   4. Utilize Ansible-vault to encrypt sensitive files
---
## Exercise 1 - Configure OSPF on all routers

In the previous exercises you learned how to utilize ansible variables, conditionals, and loops to create a simple playbook. In this exercise you will take it a step further and create one playbook with multiple plays running against multiple hosts.
   * You will create a playbook to configure OSPF on both IOS and XR router
   * You will setup pre and post checks to ensure OSPF is working correctly

Use the below IOSXE and IOS-XR configurations to configure OSPF.
```
IOSXE OSPF Configuration:

router ospf 1
  router-id 192.168.0.1
  log-adjacency-changes
  passive-interface Loopback0
  network 192.168.0.1 0.0.0.0 area 0
  network 10.0.0.4 0.0.0.3 area 0

IOS-XR OSPF Configuration:

router ospf 1
 log adjacency changes
 router-id 192.168.0.2
 area 0
  interface Loopback0
   passive enable
  !
  interface GigabitEthernet0/0/0/0
   cost 100
  !
 !

```
**Step 1 -** Create a single YAML playbook to configure OSPF on both routers.

This playbook will contain multiple plays:
   * First play will configure OSPF on the IOS router
   * Second play will configure OSPF on the XR router
   * Third play will check if OSPF is working properly on both routers

In this step, setup 3 plays to be run on the respective hosts.

```
cisco@Ansible-Controller:~/project1$ vi multi-host-ospf-config.yml

---
- name: IOS OSPF CONFIG
  hosts: IOS
  gather_facts: false
  connection: local

  tasks:

- name: XR OSPF Config
  hosts: XR
  gather_facts: false
  connection: local

  tasks:

- name: Post-Check OSPF Config on all routers
  hosts: all
  gather_facts: false

  tasks:

```

**Step 2 -** Setup tasks under the IOSXE and IOS-XR plays to configure OSPF. Use OSPF configuration provided above.

```
cisco@Ansible-Controller:~/project1$ vi multi-host-ospf-config.yml

---
- name: IOS OSPF CONFIG

  tasks:
    - name: configure ospf in CSR1Kv
      ios_config:
          parents: "router ospf 1"
          lines:
            - "router-id 192.168.0.1"
            - "log-adjacency-changes"
            - "passive-interface Loopback0"
            - "network 192.168.0.1 0.0.0.0 area 0"
            - "network 10.0.0.4 0.0.0.3 area 0"

      register: iosxe_ospf_cfg

    - debug: var=iosxe_ospf_cfg

- name: XR OSPF Config

  tasks:
    - name: configure ospf in XRv
      iosxr_config:
        parents: "router ospf 1"
        lines:
          - "router-id 192.168.0.2"
          - " log adjacency changes"
          - "area 0"
          - "interface Loopback0"
          - "passive enable"
          - "exit"
          - "interface GigabitEthernet0/0/0/0 cost 1"
          - "exit"

      register: iosxr_ospf_cfg

    - debug: var=iosxr_ospf_cfg

```
**Step 3 -** Edit the playbook so that it only configures OSPF, if OSPF is not  already present on the router.

Setup a pre-check task, which will run before the configure ospf task, to first check if OSPF is already configured on the router.

In order to accomplish this task, you will need to use the Ansible meta module. The meta task is a special kind of task which can directly influence the Ansible internal execution/state.

* Refer to [**Meta Module**](http://docs.ansible.com/ansible/latest/meta_module.html) document for more information on its parameter requirements.

In this step, you will use it as a conditional to end the play if OSPF is already configured and move to the next play.
```
cisco@Ansible-Controller:~/project1$ vi multi-host-ospf-config.yml

---
- name: IOS OSPF CONFIG
  hosts: IOS
  gather_facts: false
  connection: local

  tasks:
    - name: pre-check for ospf config
      ios_command:
        commands:
          - show run | be router ospf

      register: iosxe_ospf_pre

    - meta: end_play
      when: iosxe_ospf_pre.stdout | join('') | search('router ospf')

    - name: configure ospf in CSR1Kv
      ios_config:
          parents: "router ospf 1"
          lines:
            - "router-id 192.168.0.1"
            - "log-adjacency-changes"
            - "passive-interface Loopback0"
            - "network 192.168.0.1 0.0.0.0 area 0"
            - "network 10.0.0.4 0.0.0.3 area 0"

      register: iosxe_ospf_cfg

    - debug: var=iosxe_ospf_cfg


- name: XR OSPF Config
  hosts: XR
  gather_facts: false
  connection: local

  tasks:
    - name: pre-check for ospf config
      iosxr_command:
        commands:
          - show run router ospf

      register: iosxr_ospf_pre

    - meta: end_play
      when: iosxr_ospf_pre.stdout | join('') | search('router ospf')

    - name: configure ospf in XRv
      iosxr_config:
        parents: "router ospf 1"
        lines:
          - "router-id 192.168.0.2"
          - " log adjacency changes"
          - "area 0"
          - "interface Loopback0"
          - "passive enable"
          - "exit"
          - "interface GigabitEthernet0/0/0/0 cost 1"
          - "exit"

      register: iosxr_ospf_cfg

    - debug: var=iosxr_ospf_cfg

```

The conditional statement above takes the register value and concatenates it into a string which can then be searched for specific keywords. The meta module only gets executed if the when condition is true.

**Step 4 -** Setup a Post Check task to collect the "show ip route ospf" cli from both routers.

Note you will need to pause the execution of this task to allow for OSPF sessions to be established and routes to be exchanged.

```
cisco@Ansible-Controller:~/project1$ vi multi-host-ospf-config.yml

- name: Post-Check OSPF Config on all routers
  hosts: all
  gather_facts: false

  tasks:
    - pause: seconds=60   ##pause task execution for 60 seconds
    - name: post-check ospf config
      raw: "show ip route ospf"

      register: post_check

    - debug: var=post_check.stdout_lines

```
**Step 5 -** Check the ansible playbook for syntax errors and if no errors are present then execute the playbook.

```
cisco@Ansible-Controller:~/project1$ ansible-playbook multi-host-ospf-config.yml --syntax-check

playbook: multi-host-ospf-config.yml
cisco@Ansible-Controller:~/project1$ ansible-playbook multi-host-ospf-config.yml -v

```

- Check the playbook execution to verify OSPF configuration was successful. Observe the post-check "show ip route ospf" output to verify OSPF routes are being exchanged between the two routers (check loopback ip).
- Rerun the playbook once more and observe the configure opsf tasks being skipped due to the conditional check in the pre-check task.

---

## Exercise 2 - Automate router running-config backups

In this exercise, you will create a playbook to capture the running config from all nodes. Then you will setup a cron job to execute the playbook once a day and backup the router running-config files.

**Step 1 -** Create an ansible playbook with a single play which will collect "show run" output from all the nodes.

```
cisco@Ansible-Controller:~/project1$ vi rtr-cfg-bkup-1.yml

---
- name: Get Router Config from All Routers
  hosts: all
  gather_facts: no

  tasks:
    - name: Collect Show run from all routers
      raw: "show run"

      register: runcfg
```

Note when using the raw module do not set the connection to local. The raw module simply takes the input argument and executes the command on the remote host.

**Step 2 -** Edit the previous play to add a new task which will perform a time lookup. Then add another task to save the output to a file.

Use the set_fact option to set a variable "time" equal to the current time value on the server.
```
  tasks:
    - name: Collect Show run from all routers
      raw: "show run"

      register: runcfg

    - set_fact: time="{{lookup('pipe','date \"+%Y-%m-%d-%H-%M\"')}}"

    - name: save output to a file
      connection: local
      copy: content="\n ===show run=== \n {{ runcfg.stdout }}" dest="./{{ inventory_hostname }}_run_cfg_{{ time }}.txt"

```

Note when saving the output to a file you need to set the connection back to local since the output files need to be saved on the server itself.

**Step 3 -** Setup a cron on the Ansible Controller to execute the playbook once a day and backup the config files.

 You can test the cronjob by setting the execution time to run a one or two mins from the current to verify the job gets executed without errors.

 Execute the "date" command to get the current time on the server.

```
cisco@Ansible-Controller:~/project1$ date
Sun Feb 25 23:51:49 UTC 2018
cisco@Ansible-Controller:~/project1$

cisco@Ansible-Controller:~/project1$ sudo vi /etc/crontab

#Run Ansible Playbook rtr-cfg-bkup everyday at 5:15 am UTC to backup router configs

15 5 * * * cisco /usr/bin/ansible-playbook -i /etc/ansible/hosts  /home/cisco/rtr-cfg-bkup-1.yml

```
---
## Exercise 3 - Create snapshot tool

In this exercise, you will create a snapshot tool (playbook) which will take two sets of captures, a pre and post capture, and compare the two files to find the differences. Commonly done during network maintenance windows.

There are 3 plays in this playbook:
    1. Collect Pre-Check Captures
    2. Collect Post-Check Captures
    3. Compare the two files

The first two plays are the mostly the same, with subtle variations to indicate where its a pre-capture or post-captures. The last play will perform the difference comparison and this is executed locally on the Ansible-Controller.

**Step 1 -** Create a YAML playbook with the first pre-check play to collect and save the following commands from the CSR router. Execute the playbook to create the Pre_Check_CSR.txt output file.

You can verify the contents of the Pre_check_CSR.txt file by using the more command. {ex: more Pre_check_CSR.txt}

CSR commands to collect:
   * show version | in Software,|uptime
   * show ip int bri
   * show ip route | be Gateway
   * show run

```
cisco@Ansible-Controller:~/project1$ vi iosxe-snapshot-tool.yml

---
- name: IOS XE Pre-Check Captures
  hosts: IOS
  gather_facts: false
  connection: local
  tags: pre_play

  tasks:
   - name: Collect Pre Check Commands
     ios_command:
        authorize: yes
        commands:
           - show version | in Software,|uptime
           - show ip int bri
           - show ip route | be Gateway
           - show run

     register: precheck

   - debug: var=precheck.stdout_lines

   - name: Save Pre Check output to a file
     copy:
      content="\n ===Pre-Show-Version=== \n\n {{ precheck.stdout[0] }} \n\n ===Pre-Show-Ip-Int-Brief==== \n\n {{ precheck.stdout[1] }} \n\n ===Pre-Show-Ip-Route=== \n\n {{ precheck.stdout[2] }} \n\n ===Pre-Show-Run=== \n\n {{ precheck.stdout[3] }} "
      dest="./Pre_check_ios.txt"
```

In this play you are setting a tag value "pre_play" as part of the pre-check play. Ansible allows tags to be added at the play or task level. When executing a playbook, you can select which plays or tasks to perform by listing the tag id as part of the --tags= syntax.

Ex: ansible-playbook iosxe-snapshot-tool.yml --tags=pre_play

**Step 2 -** Create a second play inside the previous iosxe-snapshot-tool playbook to collect and save the post-check commands (similar to pre-check play).

```
cisco@Ansible-Controller:~/project1$ vi iosxe-snapshot-tool.yml

- name: IOS XE Post-Check Captures
  hosts: IOS
  gather_facts: false
  connection: local
  tags: post_play

  tasks:
   - name: Collect Post Check Commands
     ios_command:
        authorize: yes
        commands:
           - show version | in Software,|uptime
           - show ip int bri
           - show ip route | be Gateway
           - show run

     register: postcheck

   - debug: var=postcheck.stdout_lines

   - name: Save Post Check output to a file
     copy:
      content="\n ===Post-Show-Version=== \n\n {{ postcheck.stdout[0] }} \n\n ===Post-Show-Ip-Int-Brief==== \n\n {{ postcheck.stdout[1] }} \n\n ===Post-Show-Ip-Route=== \n\n {{ postcheck.stdout[2] }} \n\n ===Post-Show-Run=== \n\n {{ postcheck.stdout[3] }} "
      dest="./Post_check_ios.txt"
```

**Step 3 -** Execute the snapshot tool playbook with just the 2nd play (post-check) to create the Post_check_CSR.txt output file.

You can verify the contents of the Post_check_CSR.txt file by using the more command. {more Post_check_CSR.txt}

```
cisco@Ansible-Controller:~/project1$ ansible-playbook iosxe-snapshot-tool.yml --tags=post_play
```

**Step 4 -** Edit the iosxe-snapshot-tool playbook again to create the final play which will compare the pre-capture and post-capture files. This play will be executed on the localhost, ansible-controller, and uses the shell and command modules to read the files and find the difference.

```
cisco@Ansible-Controller:~/project1$ vi iosxe-snapshot-tool.yml

- name: Check diff between pre- and post- files
  hosts: localhost
  tags: diff_play

  tasks:
   - name: Read Pre Capture File
     shell: cat ./Pre_check_ios.txt
     register: precheck

   - name: Read Post Capture File
     shell: cat ./Post_check_ios.txt
     register: postcheck

   - debug:
       msg: " Precheck is different than post-check"
     when:  precheck.stdout != postcheck.stdout

   - debug:
       msg: " Precheck is same as post-check"
     when: precheck.stdout == postcheck.stdout

   - name: Compare files
     command: >
        diff ./Pre_check_ios.txt ./Post_check_ios.txt
     register: difference
     failed_when: difference.rc > 1

   - debug: var=difference.stdout_lines

```

Diff cmd will show Failed status when the two files are different, this error code is not helpful as we rather know contents of the failure. Setting rc > 1 will allow task to complete without failed error code and allow us to capture the difference.

**Step 5 -** Execute just the 3rd play in snapshot tool playbook to perform the diff action and identify the differences between the pre-check and post-check files.

```
cisco@Ansible-Controller:~/project1$ ansible-playbook iosxe-snapshot-tool.yml --tags=diff_play
```

---
## Exercise 4 - Ansible Vault

Ansible Vault is an ansible feature that can be used to encrypt sensitive data such as passwords and keys. The command line tool "ansible-vault" can encrypt any structure data file used in Ansible, for example the inventory or variable files.

STEP1: Create an encrypted inventory file, using the ansible-vault create option.
```
cisco@Ansible-Controller:~/project1$ cat /etc/ansible/hosts | egrep -v ^#
cisco@ansible-controller:~$ cat /etc/ansible/hosts | egrep -v ^#
[IOS]
172.16.101.88 ansible_user=cisco ansible_ssh_pass=cisco

[XR]
172.16.101.89 ansible_user=cisco ansible_ssh_pass=cisco

[ALL:children]
IOS
XR

cisco@Ansible-Controller:~/project1$ ansible-vault create encrypt-inventory.txt {vault password - cisco123}
New Vault password:
Confirm New Vault password:

#Copy paste the contents of the inventory file into this vi editor.
```
STEP 2: Ensure file is encrypted by trying to view the contents of the encrypt-inventory.txt file.
```
cisco@Ansible-Controller:~/project1$ more encrypt-inventory.txt
$ANSIBLE_VAULT;1.1;AES256
30363964613235386263663039376435393237306163623230366561613863623031353439323233
3335383862316434616665326331316433343930663733320a336334373664376633336333333566
35663963336464393862316562666136343832396434363135643831306336343365343035643864
3563363463363731630a393634323631396133323964306632306561663064373730313962626265
33323434373865636361366563373762626363636664333139623133376363653434383061373564
66643436643232316232366637643337323032633536363637353865393634326262656239353038
33666265623034323366653130393035636235623766363639306463636630653538613937666130
38366431373266653962376566646133393266363337313534366533303666303430616636306330
35656164363562323565623730616264383234333636373430343261363861333136616630393331
61333261356164656366363633343263326465306664666431373531343862393365613934303637
33306663656236663136386561373538356330333134633166343666326537303733386131663966
64346232643537333535666332396663366664386465393133303432386334333866396662353438
62633863306664383539613236666333343264393032313035383136313465313365343031336262
31363439643465653165633239393231363939656435653964343034616439366166366663626230
653631343363316565376435636435376438
```
Use the Ansible-vault view option and provide the encrytpion password inorder to view contents of the encrypted file. Similarly  the ansible-vault edit option can be used to edit the encrypted file.
```
cisco@Ansible-Controller:~/project1$ ansible-vault view encrypt-inventory.txt
Vault password: cisco123
[IOS]
172.16.101.88 ansible_user=cisco ansible_ssh_pass=cisco

[XR]
172.16.101.89 ansible_user=cisco ansible_ssh_pass=cisco

[ALL:children]
IOS
XR

cisco@Ansible-Controller:~/project1$ ansible-vault edit encrypt-inventory.txt

```
STEP 3: Try to execute a playbook with the new encrypted inventory file. You have to specify the inventory file manually since this is not the default file listed inside ansible.cfg file.
```
cisco@Ansible-Controller:~/project1$ ansible-playbook -i encrypt-inventory.txt ios-rtr-cfg-1.yml
 [WARNING]:  * Failed to parse /home/cisco/project1/encrypt-inventory.txt with ini plugin: Attempting to decrypt but no vault secrets
found

 [WARNING]: Unable to parse /home/cisco/project1/encrypt-inventory.txt as an inventory source

 [WARNING]: No inventory was parsed, only implicit localhost is available

 [WARNING]: Could not match supplied host pattern, ignoring: all

 [WARNING]: provided hosts list is empty, only localhost is available

 [WARNING]: Could not match supplied host pattern, ignoring: csr


PLAY [Get Int IP address] *****************************************************************************************************************
skipping: no hosts matched

PLAY RECAP ********************************************************************************************************************************
```
Ansible was not able to execute the playbook because it was not able to decrypt the inventory file to identify the hosts.

In order to execute the playbook using an encyrpted inventory file you must enable the --ask-vault-pass option during playbook execution to prompt for decryption password.

STEP 4: Execute the playbook again using the —ask-vault-pass option and providing the vault password {cisco123}
```
cisco@Ansible-Controller:~/project1$ ansible-playbook -i encrypt-inventory.txt ios-rtr-cfg-1.yml --ask-vault-pass

```
STEP 5: Ansible can also encrypt or decrypt an existing file by using the "ansible-vault encrypt/decrypt <file>" opiton. Decrypt the "encrypt-inventory.txt" file you created in step 1.
```
cisco@Ansible-Controller:~/project1$ ansible-vault decrypt encrypt-inventory.txt
Vault password:
Decryption successful

cisco@Ansible-Controller:~/project1$ more encrypt-inventory.txt

```

---

# Lab Files
- To browse the files: http://172.16.101.93
- If you want to copy any file: `$ scp cisco@172.16.101.93:/var/www/html/file .`

---

**<p align="center">End of Lab</p>**

---
