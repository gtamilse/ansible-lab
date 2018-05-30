# **<p align="center">CISCO LIVE 2018</p>**
# **<p align="center">ORLANDO, FL</p>**

# **<p align="center">Network Automation with Ansible</p>**
# **<p align="center">TECDEV-4500</p>**
---
# **<p align="center">Lab Guide</p>**
<p align="center">|Gopal Naganaboyina| Yogi Raghunathan | </p>
<p align="center">|Gowtham Tamilselvan| Muthu Ayyanar|</p>

---

# Table of Contents

<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->


- [Table of Contents](#table-of-contents)
- [1 Introduction](#1-introduction)
	- [1.1 Environment](#11-environment)
	- [1.2 Ad-hoc commands](#12-ad-hoc-commands)
	- [1.3 Raw module](#13-raw-module)
	- [1.4 IOS command module](#14-ios-command-module)
	- [1.5 XR command module](#15-xr-command-module)
	- [1.6 IOS config module](#16-ios-config-module)
	- [1.7 XR config module](#17-xr-config-module)
	- [1.8 Variables](#18-variables)
	- [1.8 Conditionals](#18-conditionals)
	- [1.9 Loops](#19-loops)
	- [1.10 Importing playbooks](#110-importing-playbooks)
- [2 Automation Excercises](#2-automation-excercises)
	- [2.1 Configuration backup](#21-configuration-backup)
	- [2.2 Configure OSPF](#22-configure-ospf)
	- [2.3 Method of Procedure (MOP)](#23-method-of-procedure-mop)
	- [2.4 Bulk configuration](#24-bulk-configuration)
	- [2.5 Encryption](#25-encryption)

<!-- /TOC -->

---

# 1 Introduction
## 1.1 Environment
### Objective
- blah
- blah

### 1.1.1 Ansible installation
- The following are the steps to install Ansible in an ubuntu environment. This is for reference. You are not required to do this excercise.
	- `$ sudo apt-get update`
	- `$ sudo apt-get install software-properties-common`
	- `$ sudo apt-add-repository ppa:ansible/ansible`
	- `$ sudo apt-get update`
	- `$ sudo apt-get install ansible`

#### Lab Exercise 1 - Ansible Verification
- Verify Ansible installation. Execute the commands below for a quick view:
	- `$ which ansible`
	- `$ ansible --help`
	- `$ ansible --version`

##### Lab Captures:

step 1: Identifies the directory ansible is installed.

```
cisco@ansible-controller:~$ which ansible
/usr/bin/ansible

```
Step 2: Validate that ansible has been successfully installed

```
cisco@server-1:~$ ansible --help
Usage: ansible <host-pattern> [options]
:
Some modules do not make sense in Ad-Hoc (include, meta, etc)

```
Step 3: Check the version of Ansible installed

```
cisco@server-1:~$
cisco@ansible-controller:~$ ansible --version
ansible 2.5.0
  config file = /etc/ansible/ansible.cfg
  configured module search path = [u'/home/cisco/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 2.7.6 (default, Oct 26 2016, 20:30:19) [GCC 4.8.4]
```

### 1.1.2 Review Ansible Environment
#### 1.1.2.1 Lab Exercise 2: Review Configuration file
- Review the Ansible config file
	- Find default config file: `$ ansible --version`
	- Do a quick review: `$ cat /etc/ansible/ansible.cfg`
	- Read the uncommented config: `$ cat /etc/ansible/ansible.cfg | grep -v "#" | grep -v ^$`

- Configuration file is preconfigured for you. We uncommented the following config lines, under [default] section:
	- inventory --> use default inventory file, /etc/ansible/hosts
	- gathering: --> do not gather facts
	- host_key_checking --> Do not expect ssh keys for authentication
	- timeout --> set timeout to 10 sec.
	- retry_files_enabled --> do not create retry files

##### Lab Captures:

Step 1: Identify the location of ansible.cfg.
```
cisco@ansible-controller:~$ ansible --version
ansible 2.5.0
  config file = /etc/ansible/ansible.cfg
  configured module search path = [u'/home/cisco/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 2.7.6 (default, Oct 26 2016, 20:30:19) [GCC 4.8.4]
```

Step 2: view the preconfigured setting of ansible.cfg file. Refer to ansible documentation for more informations around the variables used.

```
cisco@ansible-controller:~$ cat /etc/ansible/ansible.cfg | grep -v "#" | grep -v ^$
[defaults]
inventory      = /etc/ansible/hosts
gathering = explicit
host_key_checking = False
timeout = 10
retry_files_enabled = False
cisco@ansible-controller:~$
```
Note: # All parameters can be overridden in ansible-playbook or with command line flags. Ansible will read ANSIBLE_CONFIG,
ansible.cfg in the current working directory, .ansible.cfg in the home directory or /etc/ansible/ansible.cfg, whichever it
finds first

#### 1.1.2.2 Lab Exercise 3: Review the Inventory file
- Inventory file is preconfigured for you. We added your router IP addresses in two groups: IOS and XR.
- Review the inventory file
	- Find default inventory file: `$ grep inventory /etc/ansible/ansible.cfg | grep hosts`
	- Do a quick review: `$ cat /etc/ansible/hosts`
	- Read the uncommented config: `$ cat /etc/ansible/hosts | grep -v "#" | grep -v ^$`
- Verification
	- `$ ansible --list-hosts IOS`
	- `$ ansible --list-hosts XR`
	- `$ ansible --list-hosts ALL`

##### Lab Captures:

Step 1: Identify the location of the Inventory/hosts file.

```
cisco@ansible-controller:~$ grep inventory /etc/ansible/ansible.cfg | grep hosts
inventory      = /etc/ansible/hosts

```
step 2: Update the contents of the hosts file to have correct address configured as per your pod assignment.

```
cisco@ansible-controller:~$ cat /etc/ansible/hosts | grep -v "#" | grep -v ^$

[IOS]
172.16.101.91

[XR]
172.16.101.92

[ALL:children]
IOS
XR
[ALL:vars]
ansible_user=cisco
ansible_ssh_pass=cisco

cisco@ansible-controller:~$
```

### 1.1.4 Lab Exercise 4: Ansible modules
- Ansible ships with several modules.
- Try the below commands to get a overview of the included modules:
	- `$ ansible-doc --help`
	- `$ ansible-doc -l | grep ^ios_`
	- `$ ansible-doc -l | grep iosxr`
	- `$ ansible-doc -l | grep iosxr | wc -l`
	- `$ ansible-doc -l | wc -l`
	- `$ ansible-doc -l`

##### Lab Captures:

Step 1: In addition to web based documentation, the following CLI can be leveraged for accessing ansible documentation

```
cisco@ansible-controller:~$ ansible-doc --help
Usage: ansible-doc [-l|-F|-s] [options] [-t <plugin type> ] [plugin]

plugin documentation tool

Options:
  -a, --all             **For internal testing only** Show documentation for
                        all plugins.
  -h, --help            show this help message and exit
  -l, --list            List available plugins
  -F, --list_files      Show plugin names and their source files without
                        summaries (implies --list)
  -M MODULE_PATH, --module-path=MODULE_PATH
                        prepend colon-separated path(s) to module library
                        (default=[u'/home/cisco/.ansible/plugins/modules',
                        u'/usr/share/ansible/plugins/modules'])
  -s, --snippet         Show playbook snippet for specified plugin(s)
  -t TYPE, --type=TYPE  Choose which plugin type (defaults to "module")
  -v, --verbose         verbose mode (-vvv for more, -vvvv to enable
                        connection debugging)
  --version             show program's version number and exit

See man pages for Ansible CLI options or website for tutorials
https://docs.ansible.com

```
Step 2: List the IOS modules supported with ansible

```
cisco@ansible-controller:~$
cisco@ansible-controller:~$ ansible-doc -l | grep ^ios_
ios_banner                                           Manage multiline banners on Cisco IOS devices
ios_command                                          Run commands on remote devices running Cisco IOS
ios_config                                           Manage Cisco IOS configuration sections
ios_facts                                            Collect facts from remote devices running Cisco IOS
ios_interface                                        Manage Interface on Cisco IOS network devices
ios_l2_interface                                     Manage Layer-2 interface on Cisco IOS devices.
ios_l3_interface                                     Manage L3 interfaces on Cisco IOS network devices.
ios_linkagg                                          Manage link aggregation groups on Cisco IOS network devic...
ios_lldp                                             Manage LLDP configuration on Cisco IOS network devices.
ios_logging                                          Manage logging on network devices
ios_ping                                             Tests reachability using ping from Cisco IOS network devi...
ios_static_route                                     Manage static IP routes on Cisco IOS network devices
ios_system                                           Manage the system attributes on Cisco IOS devices
ios_user                                             Manage the aggregate of local users on Cisco IOS device
ios_vlan                                             Manage VLANs on IOS network devices
ios_vrf                                              Manage the collection of VRF definitions on Cisco IOS dev...
```
Step 3: List the XR modules supported with ansible

```
cisco@ansible-controller:~$ ansible-doc -l | grep iosxr
iosxr_banner                                         Manage multiline banners on Cisco IOS XR devices
iosxr_command                                        Run commands on remote devices running Cisco IOS XR
iosxr_config                                         Manage Cisco IOS XR configuration sections
iosxr_facts                                          Collect facts from remote devices running IOS XR
iosxr_interface                                      Manage Interface on Cisco IOS XR network devices
iosxr_logging                                        Configuration management of system logging services on ne...
iosxr_netconf                                        Configures NetConf sub-system service on Cisco IOS-XR dev...
iosxr_system                                         Manage the system attributes on Cisco IOS XR devices
iosxr_user                                           Manage the aggregate of local users on Cisco IOS XR devic...
```
Step 4: List current number of modules supported with Ansible. With increasing adoption of ansible and support from open source community, the number of modules supported increases release over release.

```
cisco@ansible-controller:~$ ansible-doc -l | grep iosxr | wc -l
9
cisco@ansible-controller:~$ ansible-doc -l | wc -l
1652
cisco@ansible-controller:~$
```

## 1.2 Lab Exercise 5: Using Ad-hoc commands

- Let us use a few modules in this section: `raw`, `ios_command`, and `iosxr_command`
- Note that not all modules are suitable to use in ad-hoc command.
- Syntax: `ansible <devices> -m <module> -a <command>`
	- devices must exist in the inventory file
- `$ ansible ALL -m raw -a "show clock"`
- `$ ansible IOS -m ios_command -a "commands='show ip route summ'" -c local'
- `$ ansible XR -m iosxr_command -a "commands='show route summ'"-c local`

##### Lab Captures:

Step 1: Validate you are able to execute the show clock command to all the hosts using the raw modules. Conenction type local is calledto chenged the default behavior from ssh.

```
cisco@ansible-controller:~$ ansible ALL -m raw -a "show clock"
172.16.101.98 | SUCCESS | rc=0 >>

*13:28:32.439 UTC Wed May 30 2018Connection to 172.16.101.98 closed by remote host.
Shared connection to 172.16.101.98 closed.
172.16.101.99 | SUCCESS | rc=0 >>
Wed May 30 13:29:58.977 UTC
13:29:59.017 UTC Wed May 30 2018

IMPORTANT:  READ CAREFULLY
Welcome to the Demo Version of Cisco IOS XRv (the "Software").
The Software is subject to and governed by the terms and conditions
of the End User License Agreement and the Supplemental End User
License Agreement accompanying the product, made available at the
time of your order, or posted on the Cisco website at
www.cisco.com/go/terms (collectively, the "Agreement").
As set forth more fully in the Agreement, use of the Software is
strictly limited to internal use in a non-production environment
solely for demonstration and evaluation purposes.  Downloading,
installing, or using the Software constitutes acceptance of the
Agreement, and you are binding yourself and the business entity
that you represent to the Agreement.  If you do not agree to all
of the terms of the Agreement, then Cisco is unwilling to license
the Software to you and (a) you may not download, install or use the
Software, and (b) you may return the Software as more fully set forth
in the Agreement.

Shared connection to 172.16.101.99 closed.
Connection to 172.16.101.99 closed by remote host.
```

Step 2: In this step, module ios_command is leveraged to execute show ip route command.

```
cisco@ansible-controller:~$ ansible IOS -m ios_command -a "commands='show ip route summ'" -c local
[sudo] password for cisco:
172.16.101.98 | SUCCESS => {
    "changed": false,
    "stdout": [
        "IP routing table name is default (0x0)\nIP routing table maximum-paths is 32\nRoute Source    Networks    Subnets     Replicates  Overhead    Memory (bytes)\napplication     0           0           0           0           0\nconnected       0           3           0           288         912\nstatic          0           0           0           0           0\ninternal        2                                               968\nTotal           2           3           0           288         1880"
    ],
    "stdout_lines": [
        [
            "IP routing table name is default (0x0)",
            "IP routing table maximum-paths is 32",
            "Route Source    Networks    Subnets     Replicates  Overhead    Memory (bytes)",
            "application     0           0           0           0           0",
            "connected       0           3           0           288         912",
            "static          0           0           0           0           0",
            "internal        2                                               968",
            "Total           2           3           0           288         1880"
        ]
    ]
}
cisco@ansible-controller:~$
```
Step 3: In this step, you will leverage the IOSXR module, to gather the route summary command.

```
cisco@ansible-controller:~$ sudo ansible XR -m iosxr_command -a "commands='show route summ'" -c local
172.16.101.99 | SUCCESS => {
    "changed": false,
    "stdout": [
        "Route Source                     Routes     Backup     Deleted     Memory(bytes)\nconnected                        1          1          0           320          \nlocal                            2          0          0           320          \ndagr                             0          0          0           0            \nTotal                            3          1          0           640"
    ],
    "stdout_lines": [
        [
            "Route Source                     Routes     Backup     Deleted     Memory(bytes)",
            "connected                        1          1          0           320          ",
            "local                            2          0          0           320          ",
            "dagr                             0          0          0           0            ",
            "Total                            3          1          0           640"
        ]
    ]
}
cisco@ansible-controller:~$
```

Note: Note the difference via executing the command between the Raw and specific module while passing the arguments.

#### Stretch excercises
> This is for later use. It is possible to use ad-hoc commands with more parameters, as below:
>
> `$ ansible IOS -c local -m ios_command -a "authorize=true commands='show run'"`
>
> `$ ansible all -c local -m ios_command -a "username=cisco password=cisco auth_pass=cisco authorize=true commands='show run'"``
>
> `$ ansible XR -c local -m iosxr_facts -a "username=cisco password=cisco gather_subset=hardware"``

- Review the section and discuss if you have any questions.
---

## 1.3 Lab Exercise 6: Raw module
- Let us create a playbook using raw module.
- Create a playbook file (p1-raw.yml) with the below content.

```
---
- name: get time from all hosts, using raw module
  hosts: all

  tasks:
    - name: execute show clock
      raw:
        show clock
```
- Execute the playbook
	- `$ ansible-playbook p1-raw.yml --syntax-check`
	- `$ ansible-playbook p1-raw.yml --step`
	- `$ ansible-playbook p1-raw.yml`

##### Lab Captures:

Step 1: Use either vi or ATOM your preferred editor of choice. The following example is shown with vi

```
cisco@ansible-controller:~$ vi p1-raw.yml
```

Step 2:  Copy & paste the following contents in the editor.

```
---
- name: get time from all hosts, using raw module
  hosts: all

  tasks:
    - name: execute show clock
      raw:
        show clock
```

step 3: Use the --syntax-check option with playbook to check for any syntax errors

```
cisco@ansible-controller:~$ ansible-playbook p1-raw.yml --syntax-check

playbook: p1-raw.yml

```

- No errors reported in playbook.

step 4: For step by step "task" execution, you can use the --step option

```
cisco@ansible-controller:~$ sudo ansible-playbook p1-raw.yml --step

PLAY [get time from all hosts, using raw module] **********************************************************************************************
Perform task: TASK: execute show clock (N)o/(y)es/(c)ontinue: y

Perform task: TASK: execute show clock (N)o/(y)es/(c)ontinue: *********************************************************************************

TASK [execute show clock] *********************************************************************************************************************
changed: [172.16.101.98]
changed: [172.16.101.99]

PLAY RECAP ************************************************************************************************************************************
172.16.101.98              : ok=1    changed=1    unreachable=0    failed=0
172.16.101.99              : ok=1    changed=1    unreachable=0    failed=0

cisco@ansible-controller:~$
```

With step, there is a option to select task to execute with Y or N.

Step 5: Execute the playbook

```
cisco@ansible-controller:~$ ansible-playbook p1-raw.yml

PLAY [get time from all hosts, using raw module] **********************************************************************************************

TASK [execute show clock] *********************************************************************************************************************
changed: [172.16.101.98]
changed: [172.16.101.99]

PLAY RECAP ************************************************************************************************************************************
172.16.101.98              : ok=1    changed=1    unreachable=0    failed=0
172.16.101.99              : ok=1    changed=1    unreachable=0    failed=0

cisco@ansible-controller:~$
```

- As you may have noticed, router output is not displayed.
- Let us now create playbook p2-raw.yml, to print the output from the routers
- Create a playbook file (p2-raw.yml) with the below content.

Step 6: Create a file p2-raw.yml with the following contents

```
cisco@ansible-controller:~$ vi p2-raw.yml
```

Step 7: Copy & paste the following contents in the editor.

```
---
- name: get time from all hosts, using raw module
  hosts: ALL

  tasks:
    - name: execute show clock
      raw:
        show clock

      register: P2_RAW_OUTPUT

    - name: print data saved in the variable
      debug:
        var: P2_RAW_OUTPUT
```

Step 8: Execute the playbook

```
cisco@ansible-controller:~$ ansible-playbook p2-raw.yml
[sudo] password for cisco:

PLAY [get time from all hosts, using raw module] ***************************************************

TASK [execute show clock] **************************************************************************
changed: [172.16.101.98]
changed: [172.16.101.99]

TASK [print data saved in the variable] ************************************************************
ok: [172.16.101.98] => {
    "P2_RAW_OUTPUT": {
        "changed": true,
        "failed": false,
        "rc": 0,
        "stderr": "Connection to 172.16.101.98 closed by remote host.\r\nShared connection to 172.16.101.98 closed.\r\n",
        "stdout": "\r\n*17:15:38.529 UTC Wed May 30 2018",
        "stdout_lines": [
            "",
            "*17:15:38.529 UTC Wed May 30 2018"
        ]
    }
}
ok: [172.16.101.99] => {
    "P2_RAW_OUTPUT": {
        "changed": true,
        "failed": false,
        "rc": 0,
        "stderr": "\n\n\nIMPORTANT:  READ CAREFULLY\nWelcome to the Demo Version of Cisco IOS XRv (the \"Software\").\nThe Software is subject to and governed by the terms and conditions\nof the End User License Agreement and the Supplemental End User\nLicense Agreement accompanying the product, made available at the\ntime of your order, or posted on the Cisco website at\nwww.cisco.com/go/terms (collectively, the \"Agreement\").\nAs set forth more fully in the Agreement, use of the Software is\nstrictly limited to internal use in a non-production environment\nsolely for demonstration and evaluation purposes.  Downloading,\ninstalling, or using the Software constitutes acceptance of the\nAgreement, and you are binding yourself and the business entity\nthat you represent to the Agreement.  If you do not agree to all\nof the terms of the Agreement, then Cisco is unwilling to license\nthe Software to you and (a) you may not download, install or use the\nSoftware, and (b) you may return the Software as more fully set forth\nin the Agreement.\n\n\nShared connection to 172.16.101.99 closed.\r\nConnection to 172.16.101.99 closed by remote host.\r\n",
        "stdout": "\r\n\r\nWed May 30 17:17:05.333 UTC\r\n17:17:05.383 UTC Wed May 30 2018\r\n",
        "stdout_lines": [
            "",
            "",
            "Wed May 30 17:17:05.333 UTC",
            "17:17:05.383 UTC Wed May 30 2018"
        ]
    }
}

PLAY RECAP *****************************************************************************************
172.16.101.98              : ok=2    changed=1    unreachable=0    failed=0
172.16.101.99              : ok=2    changed=1    unreachable=0    failed=0
```
Note: with the default debug/print, stderr, stdout and stdout_lines are reported.

Step 8: Create another playbook p3-raw.yml, to print the output from routers, in multiple lines

```
cisco@ansible-controller:~$ vi p3-raw.yml
---
- name: get time from all hosts, using raw module
  hosts: ALL

  tasks:
    - name: execute show clock
      raw:
        show clock

      register: P3_RAW_OUTPUT

    - name: print data saved in the variable
      debug:
        var: P3_RAW_OUTPUT.stdout_lines
```
Step 9: Execute the playbook

```
cisco@ansible-controller:~$ ansible-playbook p3-raw.yml

PLAY [get time from all hosts, using raw module] **********************************************************************************************

TASK [execute show clock] *********************************************************************************************************************
changed: [172.16.101.98]
changed: [172.16.101.99]

TASK [print data saved in the variable] *******************************************************************************************************
ok: [172.16.101.98] => {
    "P3_RAW_OUTPUT.stdout_lines": [
        "",
        "*20:44:24.542 UTC Tue May 29 2018"
    ]
}
ok: [172.16.101.99] => {
    "P3_RAW_OUTPUT.stdout_lines": [
        "",
        "",
        "Tue May 29 20:45:50.104 UTC",
        "20:45:50.194 UTC Tue May 29 2018"
    ]
}

PLAY RECAP ************************************************************************************************************************************
172.16.101.98              : ok=2    changed=1    unreachable=0    failed=0
172.16.101.99              : ok=2    changed=1    unreachable=0    failed=0

cisco@ansible-controller:~$
```

## 1.4 Writing playbooks
### 1.4.1 Lab Excercise 6: Playbook using IOS command module:

- Let us use ios_command module to execute some exec commands on the remote devices
- In this playbook, we will be using ios_command module in the tasks (instead of raw module)

#### Lab Captures

Step 1: Create a playbook, p4-ioscmd.yml, with the below contents

```
cisco@ansible-controller:~$ cat p4-ioscmd.yml

---
- name: collect ip route summary from all IOS devices
  hosts: IOS

  tasks:
    - name: execute route summary command
      ios_command:
        commands:
          show ip route summary

      register: P4_OUTPUT

    - name: print output
      debug:
        var: P4_OUTPUT.stdout_lines
```
step 2: Execute the playbook

```
cisco@ansible-controller:~$ ansible-playbook p4-ioscmd.yml -c local

PLAY [collect ip route summary from all IOS devices] ***********************************************

TASK [execute route summary command] ***************************************************************
ok: [172.16.101.98]

TASK [print output] ********************************************************************************
ok: [172.16.101.98] => {
    "P4_OUTPUT.stdout_lines": [
        [
            "IP routing table name is default (0x0)",
            "IP routing table maximum-paths is 32",
            "Route Source    Networks    Subnets     Replicates  Overhead    Memory (bytes)",
            "application     0           0           0           0           0",
            "connected       0           3           0           288         912",
            "static          0           0           0           0           0",
            "internal        2                                               968",
            "Total           2           3           0           288         1880"
        ]
    ]
}

PLAY RECAP *****************************************************************************************
172.16.101.98              : ok=2    changed=0    unreachable=0    failed=0

cisco@ansible-controller:~$
```

### 1.4.2 XR Command Module

### Lab Excercise 7: Playbook using XR command module

- Let us use iosxr_command module to execute some exec commands on the remote devices
- In this playbook, we will be using iosxr_command module in the tasks
-

#### Lab Captures:

Step 1: Create a playbook, p5-xrcmd.yml, with the below contents

```
cisco@ansible-controller:~$ cat p5-xrcmd.yml

---
- name: collect ip route summary from all XR devices
  hosts: XR

  tasks:
    - name: execute route summary command
      iosxr_command:
        commands:
          show route summary

      register: P5_OUTPUT

    - name: print output
      debug:
        var: P5_OUTPUT.stdout_lines
```

Step 2: Checks for playbook syntax and then execute the playbook

```
cisco@ansible-controller:~$ ansible-playbook p5-xrcmd.yml --syntax-check

playbook: p5-xrcmd.yml
cisco@ansible-controller:~$ ansible-playbook p5-xrcmd.yml

PLAY [collect ip route summary from all XR devices] ***************************************************************

TASK [execute route summary command] ******************************************************************************
ok: [172.16.101.92]

TASK [print output] ***********************************************************************************************
ok: [172.16.101.92] => {
    "P5_OUTPUT.stdout_lines": [
        [
            "Route Source                     Routes     Backup     Deleted     Memory(bytes)",
            "local                            4          0          0           640          ",
            "connected                        1          3          0           640          ",
            "dagr                             0          0          0           0            ",
            "ospf 1                           1          0          0           160          ",
            "bgp 1                            0          0          0           0            ",
            "Total                            6          3          0           1440"
        ]
    ]
}

PLAY RECAP ********************************************************************************************************
172.16.101.92              : ok=2    changed=0    unreachable=0    failed=0
```

- Review the section and discuss if you have any questions.

---
Stretch excercise ???
2 plays: ios and iosxr

---

## 1.5 Configuration management

## 1.5.1 IOS config module
### Lab Excercise 8: Configuration using IOS Module

IOS config module
- Let us use ios_config module to config IOS devices
- Create a playbook, p6-iosconfig.yml, with the below contents

#### Lab Captures:

Step 1: Create a playbook p6-iosconfig.yml with the below contents

```
cisco@ansible-controller:~$ cat p6-iosconfig.yml

---
- name: configure loopback1 interface on IOS devices
  hosts: IOS
  connection: local

  tasks:
    - name: configure loopback101 interface
      ios_config:
        parents: interface loopback101
        lines:
          - description test config by p6
          - ip address 1.1.1.101 255.255.255.255
          - shutdown
```
step 2: Execute the playbook
```
cisco@ansible-controller:~$ ansible-playbook p6-iosconfig.yml -c local

PLAY [configure loopback1 interface on IOS devices] ************************************************

TASK [configure loopback101 interface] *************************************************************
changed: [172.16.101.98]

PLAY RECAP *****************************************************************************************
172.16.101.98              : ok=1    changed=1    unreachable=0    failed=0

cisco@ansible-controller:~$
```

Step 3:  Check if loopback101 interface is created by p6-iosconfig.yml playbook

```
cisco@ansible-controller:~$ ansible IOS -m raw -a "show run int loop101"
172.16.101.98 | SUCCESS | rc=0 >>

Building configuration...

Current configuration : 108 bytes
!
interface Loopback101
 description test config by p6
 ip address 1.1.1.101 255.255.255.255
 shutdown
end
Connection to 172.16.101.98 closed by remote host.
Shared connection to 172.16.101.98 closed.

```
- Review the section and discuss if you have any questions

---
## 1.5.2 XR config module

#### Lab Excercise 9: Configuration using XR Module

- Let us use iosxr_config module to config XR devices

#### Lab Captures:

Step 1: Create a playbook, p7-xrconfig.yml, with the below contents"

```
cisco@ansible-controller:~$ cat p7-xrconfig.yml

---
- name: configure ACL test7 on all XR devices
  hosts: XR
  connection: local

  tasks:
    - name: configure acl test7
      iosxr_config:
        lines:
          - 10 permit ipv4 host 1.1.1.1 any
          - 20 permit ipv4 host 2.2.2.2 any
          - 30 permit ipv4 host 3.3.3.3 any
        parents: ipv4 access-list test7
```

Step 2: Predict the outcome of executing the above playbook. Check the ACL before configuring:

```
cisco@ansible-controller:~$ ansible XR -m raw -a "sho run ipv4 access-list"
[sudo] password for cisco:
172.16.101.99 | SUCCESS | rc=0 >>

Wed May 30 19:09:54.199 UTC
% No such configuration item(s)

IMPORTANT:  READ CAREFULLY
Welcome to the Demo Version of Cisco IOS XRv (the "Software").
The Software is subject to and governed by the terms and conditions
of the End User License Agreement and the Supplemental End User
License Agreement accompanying the product, made available at the
time of your order, or posted on the Cisco website at
www.cisco.com/go/terms (collectively, the "Agreement").
As set forth more fully in the Agreement, use of the Software is
strictly limited to internal use in a non-production environment
solely for demonstration and evaluation purposes.  Downloading,
installing, or using the Software constitutes acceptance of the
Agreement, and you are binding yourself and the business entity
that you represent to the Agreement.  If you do not agree to all
of the terms of the Agreement, then Cisco is unwilling to license
the Software to you and (a) you may not download, install or use the
Software, and (b) you may return the Software as more fully set forth
in the Agreement.

Shared connection to 172.16.101.99 closed.
Connection to 172.16.101.99 closed by remote host.

cisco@ansible-controller:~$
```

Step 3: Execute the playbook - P7-xrconfig.yml

```
cisco@ansible-controller:~$ ansible-playbook p7-xrconfig.yml
PLAY [configure ACL test7 on all XR devices] *******************************************************
TASK [configure acl test7] *************************************************************************
changed: [172.16.101.99]
PLAY RECAP *****************************************************************************************
172.16.101.99              : ok=1    changed=1    unreachable=0    failed=0
cisco@ansible-controller:~$
```

Step 4: Check for the post-playbook config
```
cisco@ansible-controller:~$ sudo ansible XR -m raw -a "sho run ipv4 access-list"
172.16.101.99 | SUCCESS | rc=0 >>


Wed May 30 19:16:19.943 UTC
ipv4 access-list test7
 10 permit ipv4 host 1.1.1.1 any
 20 permit ipv4 host 2.2.2.2 any
 30 permit ipv4 host 3.3.3.3 any
!
...
cisco@ansible-controller:~$
```
- Review the section and discuss if you have any questions

---

## 1.5.3 Variables

#### Lab Excercise 10: Playbooks using Variables

- Let us use custom variables in a playbook. Note that custom variables defined in a playbook are valid only within that playbook.

#### Lab Capture:

Step 1: Create a playbook, p8-vars.yml, with the below contents

```
cisco@ansible-controller:~$ cat p8-vars.yml

---
- name: get config of gig1 and gig2
  hosts: IOS
  connection: local
  vars:
    intf1: GigabitEthernet1
    intf2: GigabitEthernet2

  tasks:
    - name: disable proxy-arp
      ios_command:
        commands:
          - sho run int {{intf1}}
          - sho run int {{intf2}}

      register: intf_out

    - name: print stuff
      debug:
        var: intf_out.stdout_lines
```
Step 2: Predict the outcome of executing the above playbook. Execute the playbook
	- `$ ansible-playbook p8-vars.yml --syntax-check`
	- `$ ansible-playbook p8-vars.yml`
```
cisco@ansible-controller:~$ sudo ansible-playbook p8-vars.yml
[sudo] password for cisco:

PLAY [get config of gig1 and gig2] *****************************************************************

TASK [disable proxy-arp] ***************************************************************************
ok: [172.16.101.98]

TASK [print stuff] *********************************************************************************
ok: [172.16.101.98] => {
    "intf_out.stdout_lines": [
        [
            "Building configuration...",
            "",
            "Current configuration : 188 bytes",
            "!",
            "interface GigabitEthernet1",
            " description OOB Management",
            " vrf forwarding Mgmt-intf",
            " ip address 172.16.101.98 255.255.255.0",
            " negotiation auto",
            " cdp enable",
            " no mop enabled",
            " no mop sysid",
            "end"
        ],
        [
            "Building configuration...",
            "",
            "Current configuration : 170 bytes",
            "!",
            "interface GigabitEthernet2",
            " description to R2-XRv",
            " ip address 10.0.0.5 255.255.255.252",
            " ip ospf cost 1",
            " negotiation auto",
            " cdp enable",
            " no mop enabled",
            " no mop sysid",
            "end"
        ]
    ]
}

PLAY RECAP *****************************************************************************************
172.16.101.98              : ok=2    changed=0    unreachable=0    failed=0

cisco@ansible-controller:~$
```

> Reference: http://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-scopes
> - Here, we are covering a small part of variables at basic level. After completing this lab, we recommend to read the above page.
>

- Review the section and discuss if you have any questions
---

## 1.5.4 Playbooks using Loops

#### Lab Exercise 11: Playbooks with loop function

- Loops are used to perform a task repeatedly with a set of different items.


#### Lab Captures:

Step 1: Create a playbook, p9-loops.yml, with the below contents

```
cisco@ansible-controller:~$ cat p9-loops.yml

---
- name: get config of gig1 and gig2 from IOS devices
  hosts: IOS
  connection: local

  tasks:
    - name: get config of gig1 and gig2 and time
      ios_command:
        commands:
          - "{{item}}"

      with_items:
           - show run int gig1
           - show run int gig2
           - show clock

      register: command

    - name: print command
      debug:
        var: command
```
Step 2: Predict the outcome of executing the above playbook. Execute the playbook
	- `$ ansible-playbook p8-vars.yml --syntax-check`
	- `$ ansible-playbook p8-vars.yml`

```
cisco@ansible-controller:~$ ansible-playbook p9-loops.yml --syntax-check

playbook: p9-loops.yml

cisco@ansible-controller:~$ ansible-playbook p9-loops.yml

PLAY [get config of gig1 and gig2 from IOS devices] **************************************************

TASK [get config of gig1 and gig2 and time] **********************************************************
ok: [172.16.101.91] => (item=show run int gig1)
ok: [172.16.101.91] => (item=show run int gig2)
ok: [172.16.101.91] => (item=show clock)

TASK [print stuff] ***********************************************************************************
ok: [172.16.101.91] => {
    "STUFF": {
        "changed": false,
        "msg": "All items completed",
        "results": [
            {
                "_ansible_ignore_errors": null,
                "_ansible_item_result": true,
                "_ansible_no_log": false,
                "_ansible_parsed": true,
                "changed": false,
                "failed": false,
                "invocation": {
                    "module_args": {
                        "auth_pass": null,
                        "authorize": null,
                        "commands": [
                            "show run int gig1"
                        ],
                        "host": null,
                        "interval": 1,
                        "match": "all",
                        "password": null,
                        "port": null,
                        "provider": {
                            "auth_pass": null,
                            "authorize": null,
                            "host": null,
                            "password": null,
                            "port": null,
                            "ssh_keyfile": null,
                            "timeout": null,
                            "username": null
                        },
                        "retries": 10,
                        "ssh_keyfile": null,
                        "timeout": null,
                        "username": null,
                        "wait_for": null
                    }
                },
                "item": "show run int gig1",
                "stdout": [
                    "Building configuration...\n\nCurrent configuration : 188 bytes\n!\ninterface GigabitEthernet1\n description OOB Management\n vrf forwarding Mgmt-intf\n ip address 172.16.101.91 255.255.255.0\n negotiation auto\n cdp enable\n no mop enabled\n no mop sysid\nend"
                ],
                "stdout_lines": [
                    [
                        "Building configuration...",
                        "",
                        "Current configuration : 188 bytes",
                        "!",
                        "interface GigabitEthernet1",
                        " description OOB Management",
                        " vrf forwarding Mgmt-intf",
                        " ip address 172.16.101.91 255.255.255.0",
                        " negotiation auto",
                        " cdp enable",
                        " no mop enabled",
                        " no mop sysid",
                        "end"
                    ]
                ]
            },
            {
                "_ansible_ignore_errors": null,
                "_ansible_item_result": true,
                "_ansible_no_log": false,
                "_ansible_parsed": true,
                "changed": false,
                "failed": false,
                "invocation": {
                    "module_args": {
                        "auth_pass": null,
                        "authorize": null,
                        "commands": [
                            "show run int gig2"
                        ],
                        "host": null,
                        "interval": 1,
                        "match": "all",
                        "password": null,
                        "port": null,
                        "provider": {
                            "auth_pass": null,
                            "authorize": null,
                            "host": null,
                            "password": null,
                            "port": null,
                            "ssh_keyfile": null,
                            "timeout": null,
                            "username": null
                        },
                        "retries": 10,
                        "ssh_keyfile": null,
                        "timeout": null,
                        "username": null,
                        "wait_for": null
                    }
                },
                "item": "show run int gig2",
                "stdout": [
                    "Building configuration...\n\nCurrent configuration : 177 bytes\n!\ninterface GigabitEthernet2\n description Connected to XRV\n ip address 10.0.0.5 255.255.255.252\n ip ospf cost 1\n negotiation auto\n cdp enable\n no mop enabled\n no mop sysid\nend"
                ],
                "stdout_lines": [
                    [
                        "Building configuration...",
                        "",
                        "Current configuration : 177 bytes",
                        "!",
                        "interface GigabitEthernet2",
                        " description Connected to XRV",
                        " ip address 10.0.0.5 255.255.255.252",
                        " ip ospf cost 1",
                        " negotiation auto",
                        " cdp enable",
                        " no mop enabled",
                        " no mop sysid",
                        "end"
                    ]
                ]
            },
            {
                "_ansible_ignore_errors": null,
                "_ansible_item_result": true,
                "_ansible_no_log": false,
                "_ansible_parsed": true,
                "changed": false,
                "failed": false,
                "invocation": {
                    "module_args": {
                        "auth_pass": null,
                        "authorize": null,
                        "commands": [
                            "show clock"
                        ],
                        "host": null,
                        "interval": 1,
                        "match": "all",
                        "password": null,
                        "port": null,
                        "provider": {
                            "auth_pass": null,
                            "authorize": null,
                            "host": null,
                            "password": null,
                            "port": null,
                            "ssh_keyfile": null,
                            "timeout": null,
                            "username": null
                        },
                        "retries": 10,
                        "ssh_keyfile": null,
                        "timeout": null,
                        "username": null,
                        "wait_for": null
                    }
                },
                "item": "show clock",
                "stdout": [
                    "*21:40:30.056 UTC Mon May 28 2018"
                ],
                "stdout_lines": [
                    [
                        "*21:40:30.056 UTC Mon May 28 2018"
                    ]
                ]
            }
        ]
    }
}

PLAY RECAP *******************************************************************************************
172.16.101.91              : ok=2    changed=0    unreachable=0    failed=0

cisco@ansible-controller:~$
```
> Reference: http://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html


- Review the section and discuss if you have any questions

---

## 1.5.5 Playbooks with Conditionals

#### Lab Exercise 12: Playbooks with Conditionals

- Conditionals are used to decide whether to run a task or not. In this section, we will be working on "when" condition.

#### Lab Captures:

Step 1: Create a playbook, p10-conditionals.yml, with the below contents

```
cisco@ansible-controller:~$ cat p10-conditionals.yml

---
- name: get route summary from IOS and XR routers
  hosts: ALL

  tasks:
    - name: collect version info
      raw: show version

      register: SHVER

    - name: run "show ip route summ" on IOS routers
      when: SHVER.stdout | join('') is search('IOS XE')
      raw: show ip route summary

      register: IOSRTR

    - debug: var=IOSRTR.stdout_lines

    - name: run "show route summ" on XR routers
      when: SHVER.stdout | join('') is search('IOS XR')
      raw: show route summary

      register: XRRTR

    - debug: var=XRRTR.stdout_lines
```

STEP 2: Execute the playbook.

```
cisco@ansible-controller:~$ ansible-playbook p10-conditionals.yml

PLAY [get route summary from IOS and XR routers] ***************************************************

TASK [collect version info] ************************************************************************
changed: [172.16.101.98]
changed: [172.16.101.99]

TASK [run "show ip route summ" on IOS routers] *****************************************************
skipping: [172.16.101.99]
changed: [172.16.101.98]

TASK [debug] ***************************************************************************************
ok: [172.16.101.98] => {
    "IOSRTR.stdout_lines": [
        "",
        "IP routing table name is default (0x0)",
        "IP routing table maximum-paths is 32",
        "Route Source    Networks    Subnets     Replicates  Overhead    Memory (bytes)",
        "application     0           0           0           0           0",
        "connected       0           3           0           288         912",
        "static          0           0           0           0           0",
        "internal        2                                               968",
        "Total           2           3           0           288         1880"
    ]
}
ok: [172.16.101.99] => {
    "IOSRTR.stdout_lines": "VARIABLE IS NOT DEFINED!"
}

TASK [run "show route summ" on XR routers] *********************************************************
skipping: [172.16.101.98]
changed: [172.16.101.99]

TASK [debug] ***************************************************************************************
ok: [172.16.101.98] => {
    "XRRTR.stdout_lines": "VARIABLE IS NOT DEFINED!"
}
ok: [172.16.101.99] => {
    "XRRTR.stdout_lines": [
        "",
        "",
        "Wed May 30 19:57:23.174 UTC",
        "Route Source                     Routes     Backup     Deleted     Memory(bytes)",
        "connected                        1          1          0           320          ",
        "local                            2          0          0           320          ",
        "dagr                             0          0          0           0            ",
        "Total                            3          1          0           640          ",
        ""
    ]
}

PLAY RECAP *****************************************************************************************
172.16.101.98              : ok=4    changed=2    unreachable=0    failed=0
172.16.101.99              : ok=4    changed=2    unreachable=0    failed=0

cisco@ansible-controller:~$
```


> Reference: http://docs.ansible.com/ansible/latest/user_guide/playbooks_conditionals.html#the-when-statement

---


## 1.10 Importing playbooks
# 2 Automation Excercises
## 2.1 Configuration backup
## 2.2 Configure OSPF
## 2.3 Method of Procedure (MOP)
## 2.4 Bulk configuration
## 2.5 Encryption
