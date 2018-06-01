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
# 1 Ansible environment

## 1.1 Objective
- Get familiarize with the pod
- Review Ansible customization in our lab
- Run some basic commands

## 1.2 Lab excercises

### 1.2.1 Lab environment
- Make sure that you are on the right pod
- Execute the below from your Ansible Controller $ prompt
	- `$ ifconfig eth0`
	- `$ ping IP-of-your-IOS-router`
	- `$ ping IP-of-your-XR-router`
- Make sure:
	- Eth0 IP address matches with **your assigned controller IP**
	- Ping to both IOS and XR routers succeed

#### Example output
- This is no more than an example output.
- This is provided for you to compare if needed. Do not read this if the steps are executed successfully.

```
cisco@ansible-controller:~$ ifconfig eth0
eth0      Link encap:Ethernet  HWaddr 5e:00:00:00:00:00
          inet addr:172.16.101.93  Bcast:172.16.101.255  Mask:255.255.255.0			<<<
          inet6 addr: fe80::5c00:ff:fe00:0/64 Scope:Link
:
cisco@ansible-controller:~$ ping 172.16.101.91
PING 172.16.101.91 (172.16.101.91) 56(84) bytes of data.
64 bytes from 172.16.101.91: icmp_seq=1 ttl=255 time=0.914 ms
:
--- 172.16.101.91 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 0.893/0.903/0.914/0.031 ms
cisco@ansible-controller:~$ ping 172.16.101.92
PING 172.16.101.92 (172.16.101.92) 56(84) bytes of data.
64 bytes from 172.16.101.92: icmp_seq=1 ttl=255 time=1.17 ms
:
```
### 1.2.2 Ansible environment
- Verify Ansible installation. Execute the commands below for a quick view:
	- `$ which ansible`
	- `$ ansible --help`
	- `$ ansible --version`

#### Example output
- This is no more than an example output.
- This is provided for you to compare if needed. Do not read this if the steps are executed successfully.


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

### 1.2.3 Configuration file
- Ansible configuration file is preconfigured for you.
- Review the Ansible config file
	- Find default config file: `$ ansible --version`
	- Do a quick review: `$ cat /etc/ansible/ansible.cfg`
	- Read the uncommented config: `$ cat /etc/ansible/ansible.cfg | grep -v "#" | grep -v ^$`
- We uncommented the following config lines, under [default] section:
	- inventory --> use default inventory file, /etc/ansible/hosts
	- gathering: --> do not gather facts
	- host_key_checking --> Do not expect ssh keys for authentication
	- timeout --> set timeout to 10 sec.
	- retry_files_enabled --> do not create retry files

#### Example output
- This is no more than an example output.
- This is provided for you to compare if needed. Do not read this if the steps are executed successfully.


```
cisco@ansible-controller:~$ ansible --version
ansible 2.5.0
  config file = /etc/ansible/ansible.cfg	<<<
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
:
cisco@ansible-controller:~$
```
> Reference: http://docs.ansible.com/ansible/latest/reference_appendices/config.html#ansible-configuration-settings
> - Changes can be made and used in a configuration file which will be searched for in the following order:
	- ANSIBLE_CONFIG (environment variable if set)
	- ansible.cfg (in the current directory)
	- ~/.ansible.cfg (in the home directory)
	- /etc/ansible/ansible.cfg
- Ansible will process the above list and use the first file found, all others are ignored.

### 1.2.4 Inventory file
- Inventory file is preconfigured for you. We added your router IP addresses in two groups: IOS and XR.
- Review the inventory file
	- Find default inventory file: `$ grep inventory /etc/ansible/ansible.cfg | grep hosts`
	- Do a quick review: `$ cat /etc/ansible/hosts`
	- Read the uncommented config: `$ cat /etc/ansible/hosts | grep -v "#" | grep -v ^$`
- Verification
	- `$ ansible --list-hosts IOS`
	- `$ ansible --list-hosts XR`
	- `$ ansible --list-hosts ALL`

#### Example output
- This is no more than an example output.
- Refer only if needed. Do not read this if the steps are executed successfully.

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
:
cisco@ansible-controller:~$
```
> Reference: http://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html
> - It is possible to have multiple inventory files.
> - It is possible to have conflicting variables setting. To focus on the basics, we are not going into details in this lab.


### 1.2.5 Ansible modules
- Ansible ships with several modules.
- Try the below commands for a quick review:
	- `$ ansible-doc --help`
	- `$ ansible-doc -l | grep ^ios_`
	- `$ ansible-doc -l | grep iosxr`
	- `$ ansible-doc -l`
	- `$ ansible-doc ios_command`

#### Example output

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

> Reference: For your research later; not now.
> - http://docs.ansible.com/ansible/latest/modules/modules_by_category.html

---

### 1.2.6 Ad-hoc commands
- Let us use a few modules in this section: `raw`, `ios_command`, and `iosxr_command`
- Note that not all modules are suitable to use in ad-hoc command.
- Syntax: `ansible <devices> -m <module> -a <command>`
	- devices must exist in the inventory file
- Execute the below ad-hoc commands (from your home directory, /home/cisco)
- `$ ansible ALL -m raw -a "show clock"`
- `$ ansible all -m raw -a "ping vrf Mgmt-intf 172.16.101.93"`
- `$ ansible IOS --connection local -m ios_command -a "commands='show ip route summ'"`
- `$ ansible XR --connection local -m iosxr_command -a "commands='show route summ'"`

#### Stretch excercises
> This is for later use. It is possible to use ad-hoc commands with more parameters, as below:
>
> `$ ansible IOS -c local -m ios_command -a "authorize=true commands='show run'"`
>
> `$ ansible IOS -c local -m ios_command -a "username=cisco password=cisco auth_pass=cisco authorize=true commands='show run'"`
>
> `$ ansible XR -c local -m iosxr_facts -a "username=cisco password=cisco gather_subset=hardware"`

- Review the section and discuss if you have any questions.
---

# 2 Playbook primer

## 2.1 Raw module
- Let us use raw module in a playbook
- Create a playbook file (p1-raw.yml) with the below content, in your home directory

```
---
- name: get interface info from all hosts
  hosts: ALL

  tasks:
    - name: execute show ip interface brief
      raw:
        show ip interface brief
```
- Predict the outcome of this playbook.
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

- As you may have noticed, command output is not displayed.
- Create playbook p2-raw.yml, to print the output from the routers

```
cisco@ansible-controller:~$ cat p2-raw.yml

---
- name: get interface info from all hosts
  hosts: ALL

  tasks:
    - name: execute show ip interface brief
      raw:
        show ip interface brief

      register: P2_RAW_OUTPUT

    - name: print data saved in the variable
      debug:
        var: P2_RAW_OUTPUT
```
- Predict the outcome of this playbook.
- Execute the playbook
	- `$ ansible-playbook p2-raw.yml`
- If you notice, text output is misformatted.
- Create another playbook p3-raw.yml, to print the output from routers, in multiple lines

```
cisco@ansible-controller:~$ cat p3-raw.yml

---
- name: get interface info from all hosts
  hosts: ALL

  tasks:
    - name: execute show intf
      raw:
        show ip int br

      register: P3_RAW_OUTPUT

    - name: print data saved in the variable
      debug:
        var: P3_RAW_OUTPUT.stdout_lines
```
- Predict the outcome of this playbook.
- Execute the playbook
	- `$ ansible-playbook p3-raw.yml --syntax-check`
	- `$ ansible-playbook p3-raw.yml`
- Review the section and discuss if you have any questions.

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

> Reference:
> - this is for future reference only (don't spend time on this now).
> - Pay attention to sections: parameters and return values.
> - http://docs.ansible.com/ansible/latest/modules/ios_command_module.html
> - http://docs.ansible.com/ansible/latest/modules/modules_by_category.html

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
- Review the section and discuss if you have any questions

---

## 1.8 Variables
- Variables are used to store information. This information can be used in a playbook by calling the specific variable.
- Let us use custom variables in a playbook. Note that custom variables defined in a playbook are valid only within that playbook.
- Create a playbook, p8-vars.yml, with the below contents

```
cisco@ansible-controller:~$ cat p8-vars.yml

---
- name: get config of gig1 and gig2
  hosts: IOS
  connection: local
  vars:
    INTF1: GigabitEthernet1
    INTF2: GigabitEthernet2

  tasks:
    - name: disable proxy-arp
      ios_command:
        commands:
          - sho run int {{INTF1}}
          - sho run int {{INTF2}}

      register: BLAH

    - name: print stuff
      debug:
        var: BLAH
```
- Predict the outcome of executing the above playbook
- Run the playbook
	- `$ ansible-playbook p8-vars.yml --syntax-check`
	- `$ ansible-playbook p8-vars.yml`

#### Sample output
```
cisco@ansible-controller:~$ ansible-playbook p8-vars.yml --syntax-check

playbook: p8-vars.yml
cisco@ansible-controller:~$ ansible-playbook p8-vars.yml

PLAY [get config of gig1 and gig2] *******************************************************************

TASK [disable proxy-arp] *****************************************************************************
ok: [172.16.101.91]

TASK [print stuff] ***********************************************************************************
ok: [172.16.101.91] => {
    "BLAH": {
        "changed": false,
        "failed": false,
        "stdout": [
            "Building configuration...\n\nCurrent configuration : 188 bytes\n!\ninterface GigabitEthernet1\n description OOB Management\n vrf forwarding Mgmt-intf\n ip address 172.16.101.91 255.255.255.0\n negotiation auto\n cdp enable\n no mop enabled\n no mop sysid\nend",
            "Building configuration...\n\nCurrent configuration : 177 bytes\n!\ninterface GigabitEthernet2\n description Connected to XRV\n ip address 10.0.0.5 255.255.255.252\n ip ospf cost 1\n negotiation auto\n cdp enable\n no mop enabled\n no mop sysid\nend"
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
            ],
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
    }
}

PLAY RECAP *******************************************************************************************
172.16.101.91              : ok=2    changed=0    unreachable=0    failed=0
```


> Reference: http://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html
> - Here, we are covering a small part of variables at basic level. After completing this lab, we recommend to read the above page.
>

- Review the section and discuss if you have any questions
# TODO: specify default variables

---

## 1.9 Loops
- Loops are used to perform a task repeatedly with a set of different items.
- Create a playbook, p9-loops.yml, with the below contents

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

      register: STUFF

    - name: print stuff
      debug:
        var: STUFF
```
- Predict the outcome of executing the above playbook
- Run the playbook
	- `$ ansible-playbook p9-loops.yml --syntax-check`
	- `$ ansible-playbook p9-loops.yml`

#### Sample output
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

## 1.10 Conditionals

- Conditionals are used to decide whether to run a task or not. In this section, we will be working on "when" condition.
- Create a playbook, p10-conditionals.yml, with the below contents

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

      register: IOSRT

    - debug: var=IOSRT.stdout_lines

    - name: run "show route summ" on XR routers
      when: SHVER.stdout | join('') is search('IOS XR')
      raw: show route summary

      register: XRRT

    - debug: var=XRRT.stdout_lines
```

> Reference: http://docs.ansible.com/ansible/latest/user_guide/playbooks_conditionals.html#the-when-statement

#### Stretch excercise

# find IOS or XR

```

```
- Review the section and discuss if you have any questions

---

## 1.11 Importing playbooks
- We can call a playbook from another playbook
- We will be calling (or importing) using import_playbook module.
- Create a playbook, p11-import.yml, with the below contents

```
cisco@ansible-controller:~$ cat p11-import.yml

---
- name: route summary from IOS routers
  import_playbook: p4-ioscmd.yml

- name: route summary from XR routers
  import_playbook: p5-xrcmd.yml
```
- Read the playbooks p4-ioscmd.yml and p5-xrcmd.yml
	- `$ cat p4-ioscmd.yml`
	- `$ cat p5-xrcmd.yml`
- Predict the outcome of executing the playbook, p11-import.yml
- Run the playbook
	- `$ ansible-playbook p11-import.yml --syntax-check`
	- `$ ansible-playbook p11-import.yml`
- Review the section and discuss if you have any questions.
---

## 1.12 Ansible Vault

### Objective
- Encrypt inventory file using Ansible Vault feature.
- Ansible vault has many other security features. Here we are limiting to just one.

### Lab Excercise
- Ansible files can be encrypted using Vault.
- Let us use vault to encrypt the inventory file and later decrypt.

#### Pre-check

- Pay attention the owner and file privilages (`-rw-r--r--`). Quick view.
	- `$ ls -l /etc/ansible/hosts`
	- `$ cat /etc/ansible/hosts`
- Make sure the playbook runs without any errors (ok=2)
	- `$ ansible-playbook p4-ioscmd.yml`

#### Encrypt inventory file and execute a playbook

- Just for a quick view
	- `$ ansible-vault --help`
- Encrypt the inventory file
	- `$ sudo ansible-vault encrypt /etc/ansible/hosts`
	- sudo password: `cisco`
	- New vault password: `cisco123`
- Read the encrypted file
	- `$ sudo cat /etc/ansible/hosts`
	- `$ sudo ansible-vault view /etc/ansible/hosts`
- Playbook execution without vault key must fail.
	- `$ sudo ansible-playbook p4-ioscmd.yml`
	- Read the message about inventory file
- Execute the playbook with vault key
- `$ sudo ansible-playbook p4-ioscmd.yml --ask-vault-pass`
	- supply sudo and vault passwords as prompted.
	- The playbook should run fine now.

#### Decrypt and restore

- Now decrypt the inventory file
	- `$ sudo ansible-vault decrypt /etc/ansible/hosts`
- Pay attention the owner and file privilages
	- `$ ls -l /etc/ansible/hosts`
	- Notice that the file permissions are not changed back to the original values.
- Change the file permissions to the previous settings.
	- `$ sudo chmod 644 /etc/ansible/hosts`
	- `$ ls -l /etc/ansible/hosts`
	- Make sure that the file permissions are: `-rw-r--r--`
- Verify
	- `$ cat /etc/ansible/hosts`
	- `$ ansible-playbook p4-ioscmd.yml`
	- Ensure the playbook runs successfully before proceeding to next section.

#### Conclusion
- Ansible files can be encrypted using Vault.
- It is possible to use the encrypted files in playbooks using the option --ask-vault-pass
- When vault encrypt files, in addition to encrypting the file, it changes the file permissions. When decrypting the file, earlier permissions won't be restored.
- There are other features of vault that are to be explored.
- Review the section and discuss if you have any questions.

#### Lab capture
- This section is to be referred on-need basis. If your lab steps go smooth, there is no need to refer this section.

```
cisco@ansible-controller:~$ ls -l /etc/ansible/hosts
-rw-r--r-- 1 root root 1333 May 31 15:03 /etc/ansible/hosts
cisco@ansible-controller:~$ cat /etc/ansible/hosts
# This is the default ansible 'hosts' file.
#
# It should live in /etc/ansible/hosts
#
#   - Comments begin with the '#' character
#   - Blank lines are ignored
#   - Groups of hosts are delimited by [header] elements
#   - You can enter hostnames or ip addresses
#   - A hostname/ip can be a member of multiple groups

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
:
cisco@ansible-controller:~$ ansible-playbook p4-ioscmd.yml

PLAY [collect ip route summary from all IOS devices] *************************************************

TASK [execute route summary command] *****************************************************************
ok: [172.16.101.91]

TASK [print output] **********************************************************************************
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

PLAY RECAP *******************************************************************************************
172.16.101.91              : ok=2    changed=0    unreachable=0    failed=0

cisco@ansible-controller:~$
cisco@ansible-controller:~$ ansible-vault --help
Usage: ansible-vault [create|decrypt|edit|encrypt|encrypt_string|rekey|view] [options] [vaultfile.yml]

:
cisco@ansible-controller:~$ sudo ansible-vault encrypt /etc/ansible/hosts
[sudo] password for cisco:
New Vault password:
Confirm New Vault password:
Encryption successful
cisco@ansible-controller:~$ sudo cat /etc/ansible/hosts
'$ANSIBLE_VAULT;1.1;AES256
66366161326466633561633433343264646366646330633430313061663639333836386531313936
3562333338303936633733396662613166323439393639390a336661333532646534373138363663
30386630383365386639633461366332363939373465386132373930653235353466333032633663
3262363565343539650a613264636664336339313532363938393766333130616364336133386531
:
cisco@ansible-controller:~$
cisco@ansible-controller:~$ sudo ansible-vault view /etc/ansible/hosts
Vault password:
# This is the default ansible 'hosts' file.
#
# It should live in /etc/ansible/hosts
#
#   - Comments begin with the '#' character
#   - Blank lines are ignored
#   - Groups of hosts are delimited by [header] elements
#   - You can enter hostnames or ip addresses
#   - A hostname/ip can be a member of multiple groups

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
:
cisco@ansible-controller:~$ sudo ansible-playbook p4-ioscmd.yml
[sudo] password for cisco:
 [WARNING]:  * Failed to parse /etc/ansible/hosts with yaml plugin: Attempting to decrypt but no
vault secrets found

 [WARNING]:  * Failed to parse /etc/ansible/hosts with ini plugin: Attempting to decrypt but no vault
secrets found

 [WARNING]: Unable to parse /etc/ansible/hosts as an inventory source

 [WARNING]: No inventory was parsed, only implicit localhost is available

 [WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit
localhost does not match 'all'

 [WARNING]: Could not match supplied host pattern, ignoring: IOS


PLAY [collect ip route summary from all IOS devices] *************************************************
skipping: no hosts matched

PLAY RECAP *******************************************************************************************

cisco@ansible-controller:~$
cisco@ansible-controller:~$ sudo ansible-playbook p4-ioscmd.yml --ask-vault-pass
Vault password:

PLAY [collect ip route summary from all IOS devices] *************************************************

TASK [execute route summary command] *****************************************************************
ok: [172.16.101.91]

TASK [print output] **********************************************************************************
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

PLAY RECAP *******************************************************************************************
172.16.101.91              : ok=2    changed=0    unreachable=0    failed=0

cisco@ansible-controller:~$
cisco@ansible-controller:~$ sudo ansible-playbook p4-ioscmd.yml
[sudo] password for cisco:
 [WARNING]:  * Failed to parse /etc/ansible/hosts with yaml plugin: Attempting to decrypt but no
vault secrets found

 [WARNING]:  * Failed to parse /etc/ansible/hosts with ini plugin: Attempting to decrypt but no vault
secrets found

 [WARNING]: Unable to parse /etc/ansible/hosts as an inventory source

 [WARNING]: No inventory was parsed, only implicit localhost is available

 [WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit
localhost does not match 'all'

 [WARNING]: Could not match supplied host pattern, ignoring: IOS


PLAY [collect ip route summary from all IOS devices] *************************************************
skipping: no hosts matched

PLAY RECAP *******************************************************************************************

cisco@ansible-controller:~$
cisco@ansible-controller:~$ ls -l /etc/ansible/hosts
-rw------- 1 root root 1333 May 31 17:26 /etc/ansible/hosts
cisco@ansible-controller:~$
cisco@ansible-controller:~$ sudo chmod 644 /etc/ansible/hosts
cisco@ansible-controller:~$ ls -l /etc/ansible/hosts
-rw-r--r-- 1 root root 1333 May 31 17:26 /etc/ansible/hosts
cisco@ansible-controller:~$ cat /etc/ansible/hosts
# This is the default ansible 'hosts' file.
#
# It should live in /etc/ansible/hosts
#
#   - Comments begin with the '#' character
#   - Blank lines are ignored
#   - Groups of hosts are delimited by [header] elements
#   - You can enter hostnames or ip addresses
#   - A hostname/ip can be a member of multiple groups

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
:
cisco@ansible-controller:~$ ansible-playbook p4-ioscmd.yml


PLAY [collect ip route summary from all IOS devices] *************************************************

TASK [execute route summary command] *****************************************************************
ok: [172.16.101.91]

TASK [print output] **********************************************************************************
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

PLAY RECAP *******************************************************************************************
172.16.101.91              : ok=2    changed=0    unreachable=0    failed=0

cisco@ansible-controller:~$
```

---

# 2 Automation Excercises

---
## 2.1 Router config backup
### Objective
- In this exercise, let us create a playbook to take routers config backup.

### Approach
- We can use one play with two tasks.
- Task-1 to collect the config and save in a variable. We will be using "raw" module for this.
- Task-2 to save the contents of the variable into a file. We will be using "copy" module for this.

### Lab excercise
- Review the contents in the below box.
- Task-1 collects and saves running-config in a variable named as RUNCFG
- Task-2 saves the contents of RUNCFG in a file in the home directory.
- Task-2 contains a wellknown variable (aka default variable) called, inventory_hostname. It means, the current inventory device.
- Create the playbook with file name, p21-confback.yml, with the contents below.

```
---
- name: backup all routers config
  hosts: all

  tasks:
    - name: collect config  from all routers
      connection: ssh
      raw:
        show run

      register: RUNCFG

    - name: save output to a file
      connection: local
      copy:
        content: "{{ RUNCFG.stdout }}"
        dest: "/home/cisco/{{ inventory_hostname }}.txt"
```
- Predict the outcome of running this playbook
- Run the playbook
	- `$ ansible-playbook p21-confback.yml --syntax-check`
	- `$ ansible-playbook p21-confback.yml`
- Look for config files in `/home/cisco` directory

### Conclusion
- We applied two modules, raw and copy, to get the router config.
- Note that the file names may be overwritten when you rerun the playbook. And, you need to manually run the playbook.
- The optional excercise at the end of this section addresses the above tow problems and enhance this playbook. Feel free to follow the optional playbook if you want to review the other playbook.
- Review the section and discuss if you have any questions.


> Rerefence:
> - Copy module: http://docs.ansible.com/ansible/latest/modules/copy_module.html
> - inventory_hostname: http://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html

### Lab captures
```
cisco@ansible-controller:~$ cat p21-confback.yml
---
- name: backup all routers config
  hosts: all

  tasks:
    - name: collect config  from all routers
      connection: ssh
      raw:
        show run

      register: RUNCFG

    - name: save output to a file
      connection: local
      copy:
        content: "{{ RUNCFG.stdout }}"
        dest: "/home/cisco/{{ inventory_hostname }}.txt"

cisco@ansible-controller:~$ ansible-playbook p21-confback.yml --syntax-check

playbook: p21-confback.yml

cisco@ansible-controller:~$ ansible-playbook p21-confback.yml

PLAY [backup all routers config] *********************************************************************

TASK [collect config  from all routers] **************************************************************
changed: [172.16.101.91]
changed: [172.16.101.92]

TASK [save output to a file] *************************************************************************
changed: [172.16.101.91]
changed: [172.16.101.92]

PLAY RECAP *******************************************************************************************
172.16.101.91              : ok=2    changed=2    unreachable=0    failed=0
172.16.101.92              : ok=2    changed=2    unreachable=0    failed=0

cisco@ansible-controller:~$ ls -l 172.16.101.*
-rw-rw-r-- 1 cisco cisco 5066 May 31 21:14 172.16.101.91.txt
-rw-rw-r-- 1 cisco cisco 2008 May 31 21:14 172.16.101.92.txt

cisco@ansible-controller:~$ cat 172.16.101.91.txt | more

Building configuration...

Current configuration : 4768 bytes
!
! Last configuration change at 17:02:00 UTC Mon May 28 2018 by cisco
!
version 16.5
service timestamps debug datetime msec
service timestamps log datetime msec
platform qfp utilization monitor load 80
no platform punt-keepalive disable-kernel-core
platform console serial
:
cisco@ansible-controller:~$ cat 172.16.101.92.txt | more


Thu May 31 21:16:06.567 UTC
Building configuration...
!! IOS XR Configuration 6.2.2.15I
!! Last configuration change at Mon May 28 16:45:47 2018 by cisco
!
!  IOS-XR Config generated on 2018-03-27 20:08
! by autonetkit_0.24.0
hostname R2-XRv
```

### Optional excercise
- Create a playbook to backup routers' config, with the below requirements:
	- Config backup should be taken daily at 01:00 hrs UTC
	- Filename of the config files should include timestamp to maintain uniqueness.
- Execute your playbook and verify if the results meet the requirements.
- Fyi, a playbook file is here: [op21-confback.yml](https://github.com/gtamilse/ansible-lab/blob/master/optional/op21-confback.yml)
- Fyi, lab captures file is at: [op21-confback-labcap.txt](https://github.com/gtamilse/ansible-lab/blob/master/optional/op21-confback-labcap.txt)
---

## 2.2 Method of Procedure (MOP)

- MOP is a step-by-step sequence of tasks executed on network devices to achieve a preplanned objective.
- In this exercise, we will have the playbook capture pre-task data, configure OSPF, and capture post-task data.
- This is a simplified version of a generic MOP. To use in production, it requires a lot of enhancement.

### Objective
- Configure OSPF on both IOS and XR routers and enable OSPF on loopback and back-to-back Gigabit Ethernet interfaces.

### Approach
- Step-1: Capture pre-config data
- Step-2: Configure OSPF on both the routers
- Step-3: Capture post-config data

### Lab exercise

#### Step-1: Pre-config data capture
- Let us capture relavent data before configuring ospf on the routers
- Create a playbook, p22-preconfig.yml, with the below contents
- This playbook has two plays: first captures data from IOS devices and the second play will capture data from XR devices.

```
---
- name: pre-config captures from IOS routers
  hosts: IOS
  connection: local

  tasks:
    - name: collect precheck commands
      ios_command:
        authorize: yes
        commands:
           - show run | section ospf
           - show ip ospf interface brief
           - show ip ospf neighbor
           - show ip route ospf

      register: IOSPRE

    - name: save output to a file
      copy:
        content=" \n\n {{ IOSPRE.stdout[0] }} \n\n {{ IOSPRE.stdout[1] }} \n\n {{ IOSPRE.stdout[2] }} \n\n {{ IOSPRE.stdout[3] \n\n }} "
        dest="/home/cisco/p22-precheck-ios.txt"

- name: pre-config captures from XR routers
  hosts: XR
  connection: local

  tasks:
    - name: collect precheck commands
      iosxr_command:
        commands:
           - show run router ospf
           - show ospf interface brief
           - show ospf neighbor
           - show route ospf

      register: XRPRE

    - name: save output to a file
      copy:
        content=" \n\n {{ XRPRE.stdout[0] }} \n\n {{ XRPRE.stdout[1] }} \n\n {{ XRPRE.stdout[2] }} \n\n {{ XRPRE.stdout[3] \n\n }} "
        dest="/home/cisco/p22-precheck-xr.txt"
```

#### Step-2: Configure OSPF
- Let us make a playbook with two plays
	- play-1 to configure OSPF on IOS routers
	- play-2 to configure ospf on XR routers
- Create a playbook, p22-config.yml, with the below contents

```
---
- name: configure ospf on IOS routers
  hosts: IOS
  connection: local

  tasks:
    - name: configure IOS ospf
      ios_config:
          parents: "router ospf 1"
          lines:
            - "router-id 192.168.0.1"
            - "passive-interface Loopback0"
            - "network 192.168.0.1 0.0.0.0 area 0"
            - "network 10.0.0.4 0.0.0.3 area 0"
          save_when: changed

- name: configure ospf on XR routers
  hosts: XR
  connection: local

  tasks:
    - name: configure XR ospf
      iosxr_config:
        parents: "router ospf 1"
        lines:
          - "router-id 192.168.0.2"
          - "area 0"
          - "interface Loopback0"
          - "passive enable"
          - "exit"
          - "interface GigabitEthernet0/0/0/0"
          - "exit"
```
- Predict the outcome of this playbook.
- Note that the playbook execution may be inconsistent if OSPF is already configured on the router
- Make sure that OSPF config doesnt exist on the routers
	- `$ ansible IOS -m raw -a "sho run | sec ospf"`
	- `$ ansible XR -m raw -a "sho run router ospf"`
- Important: If there is ospf config, **delete** it for this excercise.
- Run the playbook"
	- `$ ansible-playbook p22-config.yml --syntax-check`
	- `$ ansible-playbook p22-config.yml`
- Verify (OSPF route should be there)
	- `$ ansible IOS -m raw -a "show ip route ospf"`

# bug: final playbook will fail because this plybook config's ospf

#### Step-3: Post-config data capture

- Capture relavent data before configuring ospf on the routers
- This playbook has two plays:
	- play-1 to capture data from IOS devices
	- play-2 to capture data from XR devices.
- Create a playbook, p22-postconfig.yml, with the below contents

```
---
- name: post-config captures from IOS routers
  hosts: IOS
  connection: local

  tasks:
    - name: collect postcheck commands
      ios_command:
        authorize: yes
        commands:
           - show run | section ospf
           - show ip ospf interface brief
           - show ip ospf neighbor
           - show ip route ospf

      register: IOSPOST

    - name: save output to a file
      copy:
        content=" \n\n {{ IOSPOST.stdout[0] }} \n\n {{ IOSPOST.stdout[1] }} \n\n {{ IOSPOST.stdout[2] }} \n\n {{ IOSPOST.stdout[3] \n\n }} "
        dest="/home/cisco/p22-postcheck-ios.txt"

- name: post-config captures from XR routers
  hosts: XR
  connection: local

  tasks:
    - name: collect postcheck commands
      iosxr_command:
        commands:
           - show run router ospf
           - show ospf interface brief
           - show ospf neighbor
           - show route ospf

      register: XRPOST

    - name: save output to a file
      copy:
        content=" \n\n {{ XRPOST.stdout[0] }} \n\n {{ XRPOST.stdout[1] }} \n\n {{ XRPOST.stdout[2] }} \n\n {{ XRPOST.stdout[3] \n\n }} "
        dest="/home/cisco/p22-postcheck-xr.txt"
```

### Conclusion

### Optional exercise

- Step-4: report the difference in the captures. This will help the operator to determine the success of the config task.

> Reference
>



### Lab captures




---

## 2.4 Bulk configuration

---

# Appendix

## Reference
## Optional excercise op-21

```

```


## Optional excercise op-22
## Optional excercise op-23

---
