# **<p align="center">CISCO LIVE 2018</p>**
# **<p align="center">ORLANDO, FL</p>**

# **<p align="center">Network Automation with Ansible</p>**
# **<p align="center">TECDEV-4500</p>**
---
# **<p align="center">Lab Guide</p>**
<p align="center">|Yogi Raghunathan|Gopal Naganaboyina|</p>
<p align="center">|Gowtham Tamilselvan|Muthu Ayyanar|</p>

---

# Table of Contents

<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [**<p align="center">CISCO LIVE 2018</p>**](#p-aligncentercisco-live-2018p)
- [**<p align="center">ORLANDO, FL</p>**](#p-aligncenterorlando-flp)
- [**<p align="center">Network Automation with Ansible</p>**](#p-aligncenternetwork-automation-with-ansiblep)
- [**<p align="center">TECDEV-4500</p>**](#p-aligncentertecdev-4500p)
- [**<p align="center">Lab Guide</p>**](#p-aligncenterlab-guidep)
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
- Verify Ansible installation. Execute the commands below for a quick view:
	- `$ which ansible`
	- `$ ansible --help`
	- `$ ansible --version`

#### Sample output
```
cisco@ansible-controller:~$ which ansible
/usr/bin/ansible
cisco@server-1:~$ ansible --help
Usage: ansible <host-pattern> [options]
:
Some modules do not make sense in Ad-Hoc (include, meta, etc)
cisco@server-1:~$
cisco@ansible-controller:~$ ansible --version
ansible 2.5.0
  config file = /etc/ansible/ansible.cfg
  configured module search path = [u'/home/cisco/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 2.7.6 (default, Oct 26 2016, 20:30:19) [GCC 4.8.4]
```

### 1.1.2 Configuration file
- Configuration file is preconfigured for you. We uncommented the following config lines, under [default] section:
	- inventory --> use default inventory file, /etc/ansible/hosts
	- gathering: --> do not gather facts
	- host_key_checking --> Do not expect ssh keys for authentication
	- timeout --> set timeout to 10 sec.
	- retry_files_enabled --> do not create retry files
- Review the Ansible config file
	- Find default config file: `$ ansible --version`
	- Do a quick review: `$ cat /etc/ansible/ansible.cfg`
	- Read the uncommented config: `$ cat /etc/ansible/ansible.cfg | grep -v "#" | grep -v ^$`

#### Sample output:

```
cisco@ansible-controller:~$ ansible --version
ansible 2.5.0
  config file = /etc/ansible/ansible.cfg
  configured module search path = [u'/home/cisco/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 2.7.6 (default, Oct 26 2016, 20:30:19) [GCC 4.8.4]
cisco@ansible-controller:~$ cat /etc/ansible/ansible.cfg | grep -v "#" | grep -v ^$
[defaults]
inventory      = /etc/ansible/hosts
gathering = explicit
host_key_checking = False
timeout = 10
retry_files_enabled = False

cisco@ansible-controller:~$
```

### 1.1.3 Inventory file
- Inventory file is preconfigured for you. We added your router IP addresses in two groups: IOS and XR.
- Review the inventory file
	- Find default inventory file: `$ grep inventory /etc/ansible/ansible.cfg | grep hosts`
	- Do a quick review: `$ cat /etc/ansible/hosts`
	- Read the uncommented config: `$ cat /etc/ansible/hosts | grep -v "#" | grep -v ^$`
- Verification
	- `$ ansible --list-hosts IOS`
	- `$ ansible --list-hosts XR`
	- `$ ansible --list-hosts ALL`

#### Sample output

```
cisco@ansible-controller:~$ grep inventory /etc/ansible/ansible.cfg | grep hosts
inventory      = /etc/ansible/hosts


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
ansible_connection=local

cisco@ansible-controller:~$
```
### 1.1.4 Ansible modules
- Ansible ships with several modules.
- Try the below commands to get a overview of the included modules:
	- `$ ansible-doc --help`
	- `$ ansible-doc -l | grep ^ios_`
	- `$ ansible-doc -l | grep iosxr`
	- `$ ansible-doc -l | grep iosxr | wc -l`
	- `$ ansible-doc -l | wc -l`
	- `$ ansible-doc -l`

#### Sample output

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
cisco@ansible-controller:~$ ansible-doc -l | grep iosxr | wc -l
9
cisco@ansible-controller:~$ ansible-doc -l | wc -l
1652
cisco@ansible-controller:~$
```

## 1.2 Ad-hoc commands
- Let us use a few modules in this section: `raw`, `ios_command`, and `iosxr_command`
- Note that not all modules are suitable to use in ad-hoc command.
- Syntax: `ansible <devices> -m <module> -a <command>`
	- devices must exist in the inventory file
- `$ ansible 172.31.56.91 -m raw -a "show ip int brief"`
- `$ ansible ALL -m raw -a "show clock"`
- `$ ansible all -m raw -a "ping vrf Mgmt-intf 172.16.101.93"`
- `$ ansible IOS -m ios_command -a "commands='show ip route summ'"`
- `$ ansible XR -m iosxr_command -a "commands='show route summ'"`

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

## 1.3 Raw module
- Let us use raw module in a playbook
- Create a playbook file (p1-raw.yml) with the below content.

```
$ cat p1-raw.yml

---
- name: get time from all hosts, using raw module
  hosts: all

  tasks:
    - name: execute show clock
      raw:
        show clock
```

- Execute the play book
	- `$ ansible-playbook p1-raw.yml --syntax-check`
	- `$ ansible-playbook p1-raw.yml --step`
	- `$ ansible-playbook p1-raw.yml`

#### Sample output

```
cisco@ansible-controller:~$ ansible-playbook p1-raw.yml

PLAY [get time from all hosts, using raw module] ******************************************************************

TASK [execute show clock] *****************************************************************************************
changed: [172.16.101.91]
changed: [172.16.101.92]

PLAY RECAP ********************************************************************************************************
172.16.101.91              : ok=1    changed=1    unreachable=0    failed=0
172.16.101.92              : ok=1    changed=1    unreachable=0    failed=0

cisco@ansible-controller:~$
```

- As you may have noticed, router time info is not displayed.
- Create playbook p2-raw.yml, to print the output from the routers

```
cisco@ansible-controller:~$ cat p2-raw.yml

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
- Execute the playbook
	- `$ ansible-playbook p2-raw.yml`
- If you notice, all the output is in one line.

- Create another playbook p3-raw.yml, to print the output from routers, in multiple lines

```
cisco@ansible-controller:~$ cat p3-raw.yml

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
---

## 1.4 IOS command module
- Let us use ios_command module to execute some exec commands on the remote devices
- In this playbook, we will be using ios_command module in the tasks (instead of raw module)
- Create a playbook, p4-ioscmd.yml, with the below contents

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
- Execute the playbook
	- `$ ansible-playbook p4-ioscmd.yml --syntax-check`
	- `$ ansible-playbook p4-ioscmd.yml`

#### Sample output

```
cisco@ansible-controller:~$ ansible-playbook p4-ioscmd.yml

PLAY [collect ip route summary from all IOS devices] **************************************************************

TASK [execute route summary command] ******************************************************************************
ok: [172.16.101.91]

TASK [print output] ***********************************************************************************************
ok: [172.16.101.91] => {
    "P4_OUTPUT.stdout_lines": [
        [
            "IP routing table name is default (0x0)",
            "IP routing table maximum-paths is 32",
            "Route Source    Networks    Subnets     Replicates  Overhead    Memory (bytes)",
            "application     0           0           0           0           0",
            "connected       0           4           0           384         1216",
            "static          0           0           0           0           0",
            "ospf 1          0           1           0           96          308",
            "  Intra-area: 1 Inter-area: 0 External-1: 0 External-2: 0",
            "  NSSA External-1: 0 NSSA External-2: 0",
            "bgp 1           0           0           0           0           0",
            "  External: 0 Internal: 0 Local: 0",
            "internal        3                                               1432",
            "Total           3           5           0           480         2956"
        ]
    ]
}

PLAY RECAP ********************************************************************************************************
172.16.101.91              : ok=2    changed=0    unreachable=0    failed=0
```

---

## 1.5 XR command module
- Let us use iosxr_command module to execute some exec commands on the remote devices
- In this playbook, we will be using iosxr_command module in the tasks
- Create a playbook, p5-xrcmd.yml, with the below contents

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
- Execute the playbook
	- `$ ansible-playbook p5-xrcmd.yml --syntax-check`
	- `$ ansible-playbook p5-xrcmd.yml`

#### Sample output
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

## 1.6 IOS config module
- Let us use ios_config module to config IOS devices
- Create a playbook, p6-iosconfig.yml, with the below contents

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

- Check if loopback101 interface was already configured
	- `ansible IOS -m raw -a "show run int loop101"`
- If needed, edit the playbook to use an available loopback interface
- Execute the playbook
	- `ansible-playbook p6-iosconfig.yml`
- - Check if loopback101 interface is created by p6-iosconfig.yml playbook
	- `ansible IOS -m raw -a "show run int loop101"`
- Review the section and discuss if you have any questions


---

## 1.7 XR config module
- Let us use iosxr_config module to config XR devices
- Create a playbook, p7-xrconfig.yml, with the below contents"

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

- Predict the outcome of executing the above playbook
- Check the ACL before configuring:
	- `$ ansible XR -m raw -a "sho run ipv4 access-list"`
- Run the playbook
	- `$ ansible-playbook p7-xrconfig.yml`
- Check for the post-playbook config
	- `$ ansible XR -m raw -a "sho run ipv4 access-list"`

### Sample output
```
cisco@ansible-controller:~$ ansible XR -m raw -a "sho run ipv4 access-list"
172.16.101.92 | SUCCESS | rc=0 >>


Mon May 28 16:45:40.825 UTC
% No such configuration item(s)


IMPORTANT:  READ CAREFULLY
:
Shared connection to 172.16.101.92 closed.
Connection to 172.16.101.92 closed by remote host.


cisco@ansible-controller:~$ ansible-playbook p7-xrconfig.yml

PLAY [configure ACL test7 on all XR devices] *********************************************************

TASK [configure acl test7] ***************************************************************************
changed: [172.16.101.92]

PLAY RECAP *******************************************************************************************
172.16.101.92              : ok=1    changed=1    unreachable=0    failed=0

cisco@ansible-controller:~$ ansible XR -m raw -a "sho run ipv4 access-list"
172.16.101.92 | SUCCESS | rc=0 >>


Mon May 28 16:45:54.404 UTC
ipv4 access-list test7
 1 remark configured by p7
 10 permit ipv4 host 1.1.1.1 any
 20 permit ipv4 host 2.2.2.2 any
 30 permit ipv4 host 3.3.3.3 any
!

IMPORTANT:  READ CAREFULLY
:
Connection to 172.16.101.92 closed by remote host.
```

---

## 1.8 Variables



> Reference: http://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-scopes
>

---

## 1.8 Conditionals
## 1.9 Loops
## 1.10 Importing playbooks
# 2 Automation Excercises
## 2.1 Configuration backup
## 2.2 Configure OSPF
## 2.3 Method of Procedure (MOP)
## 2.4 Bulk configuration
## 2.5 Encryption
