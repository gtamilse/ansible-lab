# **<p align="center">CISCO LIVE 2018</p>**
# **<p align="center">ORLANDO, FL</p>**

# **<p align="center">Network Automation with Ansible</p>**
# **<p align="center">TECDEV-4500</p>**

---
# **<p align="center">Lab Guide</p>**

---
<p align="center">Gopal Naganaboyina | Yogi Raghunathan </p>

---

# Table of Contents

<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [**<p align="center">CISCO LIVE 2018</p>**](#p-aligncentercisco-live-2018p)
- [**<p align="center">ORLANDO, FL</p>**](#p-aligncenterorlando-flp)
- [**<p align="center">Network Automation with Ansible</p>**](#p-aligncenternetwork-automation-with-ansiblep)
- [**<p align="center">TECDEV-4500</p>**](#p-aligncentertecdev-4500p)
- [**<p align="center">Lab Guide</p>**](#p-aligncenterlab-guidep)
- [Table of Contents](#table-of-contents)
- [Part-1: Ansible introduction](#part-1-ansible-introduction)
	- [1.1 Objective](#11-objective)
	- [1.2 Lab exercises](#12-lab-exercises)
		- [1.2.3 Configuration file](#123-configuration-file)
			- [Example output](#example-output)
		- [Reference](#reference)
		- [1.2.4 Inventory file](#124-inventory-file)
			- [Example output](#example-output)
		- [Reference](#reference)
		- [1.2.5 Ansible modules](#125-ansible-modules)
			- [Example output](#example-output)
		- [Reference](#reference)
		- [1.2.6 Ad-hoc commands](#126-ad-hoc-commands)
		- [Optional exercises](#optional-exercises)
		- [Conclusion](#conclusion)
			- [Example output](#example-output)
- [Part-2: Playbook primer](#part-2-playbook-primer)
	- [2.1 Raw module](#21-raw-module)
		- [Lab exercise](#lab-exercise)
		- [Conclusion](#conclusion)
		- [Example output](#example-output)
	- [1.4 IOS command module](#14-ios-command-module)
		- [Lab exercise](#lab-exercise)
		- [Conclusion](#conclusion)
		- [Reference](#reference)
		- [Example output](#example-output)
	- [1.5 XR command module](#15-xr-command-module)
		- [Lab exercise](#lab-exercise)
		- [Conclusion](#conclusion)
		- [Optional exercise](#optional-exercise)
		- [Example output](#example-output)
	- [1.6 IOS config module](#16-ios-config-module)
		- [Lab exercise](#lab-exercise)
		- [Conclusion](#conclusion)
		- [Example output](#example-output)
	- [1.7 XR config module](#17-xr-config-module)
		- [Lab exercise](#lab-exercise)
		- [Conclusion](#conclusion)
		- [Example output](#example-output)
	- [1.8 Variables](#18-variables)
		- [Lab exercise](#lab-exercise)
		- [Optional exercise](#optional-exercise)
		- [Conclusion](#conclusion)
		- [Example output](#example-output)
		- [Reference](#reference)
	- [1.9 Loops](#19-loops)
		- [Lab exercise](#lab-exercise)
		- [Conclusion](#conclusion)
		- [Example output](#example-output)
		- [Reference](#reference)
	- [1.10 Conditionals](#110-conditionals)
		- [Lab exercise](#lab-exercise)
		- [Conclusion](#conclusion)
		- [Optional exercise](#optional-exercise)
		- [Example output](#example-output)
		- [Reference](#reference)
	- [1.11 Importing playbooks](#111-importing-playbooks)
		- [Lab exercise](#lab-exercise)
		- [Conclusion](#conclusion)
		- [Example output](#example-output)
	- [1.12 Ansible Vault](#112-ansible-vault)
		- [Objective](#objective)
		- [Lab exercise](#lab-exercise)
			- [Pre-check](#pre-check)
			- [Encrypt inventory file and execute a playbook](#encrypt-inventory-file-and-execute-a-playbook)
			- [Decrypt and restore](#decrypt-and-restore)
		- [Conclusion](#conclusion)
		- [Example output](#example-output)
- [This is the default ansible 'hosts' file.](#this-is-the-default-ansible-hosts-file)
- [](#)
- [It should live in /etc/ansible/hosts](#it-should-live-in-etcansiblehosts)
- [](#)
- [- Comments begin with the '#' character](#-comments-begin-with-the-character)
- [- Blank lines are ignored](#-blank-lines-are-ignored)
- [- Groups of hosts are delimited by [header] elements](#-groups-of-hosts-are-delimited-by-header-elements)
- [- You can enter hostnames or ip addresses](#-you-can-enter-hostnames-or-ip-addresses)
- [- A hostname/ip can be a member of multiple groups](#-a-hostnameip-can-be-a-member-of-multiple-groups)
- [This is the default ansible 'hosts' file.](#this-is-the-default-ansible-hosts-file)
- [](#)
- [It should live in /etc/ansible/hosts](#it-should-live-in-etcansiblehosts)
- [](#)
- [- Comments begin with the '#' character](#-comments-begin-with-the-character)
- [- Blank lines are ignored](#-blank-lines-are-ignored)
- [- Groups of hosts are delimited by [header] elements](#-groups-of-hosts-are-delimited-by-header-elements)
- [- You can enter hostnames or ip addresses](#-you-can-enter-hostnames-or-ip-addresses)
- [- A hostname/ip can be a member of multiple groups](#-a-hostnameip-can-be-a-member-of-multiple-groups)
- [This is the default ansible 'hosts' file.](#this-is-the-default-ansible-hosts-file)
- [](#)
- [It should live in /etc/ansible/hosts](#it-should-live-in-etcansiblehosts)
- [](#)
- [- Comments begin with the '#' character](#-comments-begin-with-the-character)
- [- Blank lines are ignored](#-blank-lines-are-ignored)
- [- Groups of hosts are delimited by [header] elements](#-groups-of-hosts-are-delimited-by-header-elements)
- [- You can enter hostnames or ip addresses](#-you-can-enter-hostnames-or-ip-addresses)
- [- A hostname/ip can be a member of multiple groups](#-a-hostnameip-can-be-a-member-of-multiple-groups)
- [2 Automating common tasks](#2-automating-common-tasks)
	- [2.1 Router config backup](#21-router-config-backup)
		- [Objective](#objective)
		- [Approach](#approach)
		- [Lab exercise](#lab-exercise)
		- [Conclusion](#conclusion)
		- [Optional exercise](#optional-exercise)
		- [Reference](#reference)
		- [Example output](#example-output)
	- [Device health monitoring](#device-health-monitoring)
		- [Objective](#objective)
		- [Approach](#approach)
		- [Lab exercise](#lab-exercise)
- [PUT PLAYBOOK HERE](#put-playbook-here)
		- [Conclusion](#conclusion)
			- [Example output](#example-output)
- [PUT OUTPUT](#put-output)
	- [2.2 Method of Procedure (MOP)](#22-method-of-procedure-mop)
		- [Objective](#objective)
		- [Approach](#approach)
		- [Lab exercise](#lab-exercise)
			- [Step-1: Pre-config data capture](#step-1-pre-config-data-capture)
			- [Step-2: Configure OSPF](#step-2-configure-ospf)
			- [Step-3: Post-config data capture](#step-3-post-config-data-capture)
			- [Step-4: Include first 3 steps in a playbook](#step-4-include-first-3-steps-in-a-playbook)
		- [Conclusion](#conclusion)
		- [Optional exercise](#optional-exercise)
		- [Reference](#reference)
		- [Example output](#example-output)
	- [Generate iBGP config using roles and upload to routers](#generate-ibgp-config-using-roles-and-upload-to-routers)
		- [objectives](#objectives)
		- [Approach](#approach)
			- [Lab Exercise](#lab-exercise)
		- [Conclusion](#conclusion)
			- [Example output:](#example-output)
- [Appendix](#appendix)
	- [Reference](#reference)
	- [Ansible installation](#ansible-installation)
	- [Optional exercise op5-cmd.yml](#optional-exercise-op5-cmdyml)
		- [Lab exercise](#lab-exercise)
		- [Example output](#example-output)
	- [Optional exercise op8-vars.yml](#optional-exercise-op8-varsyml)
		- [Example output](#example-output)
	- [Optional exercise op10-conditionals.yml](#optional-exercise-op10-conditionalsyml)
		- [Example output](#example-output)
	- [Optional exercise op21-confback.yml](#optional-exercise-op21-confbackyml)
		- [Step-1: Playbook, op21-confback.yml](#step-1-playbook-op21-confbackyml)
		- [Step-2: Cronjob](#step-2-cronjob)
- [Run Ansible Playbook rtr-cfg-bkup everyday at 3:00 am UTC to backup router configs](#run-ansible-playbook-rtr-cfg-bkup-everyday-at-300-am-utc-to-backup-router-configs)
		- [Reference](#reference)
		- [Example output](#example-output)
- [/etc/crontab: system-wide crontab](#etccrontab-system-wide-crontab)
- [Unlike any other crontab you don't have to run the `crontab'](#unlike-any-other-crontab-you-dont-have-to-run-the-crontab)
- [command to install the new version when you edit this file](#command-to-install-the-new-version-when-you-edit-this-file)
- [and files in /etc/cron.d. These files also have username fields,](#and-files-in-etccrond-these-files-also-have-username-fields)
- [that none of the other crontabs do.](#that-none-of-the-other-crontabs-do)
- [m h dom mon dow user	command](#m-h-dom-mon-dow-user-command)
- [Run Ansible Playbook rtr-cfg-bkup everyday at 3:00 am UTC to backup router configs](#run-ansible-playbook-rtr-cfg-bkup-everyday-at-300-am-utc-to-backup-router-configs)
	- [Optional exercise op-22 (MOP)](#optional-exercise-op-22-mop)
		- [Approach](#approach)
		- [Add delay](#add-delay)
		- [Diff file](#diff-file)
		- [MOP playbook](#mop-playbook)
		- [Example output](#example-output)

<!-- /TOC -->


---

# Part-1: Ansible introduction

## 1.1 Objective
- Review Ansible customization in your pod
- Run Ansible ad-hoc commands

## 1.2 Lab exercises
- Configuration file
- Inventory file
- Ansible modules
- Ad-hoc commands


### 1.2.3 Configuration file
- Ansible configuration file is preconfigured for you.
- We uncommented the following config lines, under [default] section:
	- inventory --> use default inventory file, /etc/ansible/hosts
	- gathering: --> set to explicit, to not gather facts by default
	- host_key_checking --> Do not expect ssh keys for authentication
	- timeout --> set timeout to 10 sec.
	- retry_files_enabled --> do not create retry files
- Execute the below to review the Ansible installation and the config file:

```
$ which ansible
$ ansible --help
$ ansible --version
$ cat /etc/ansible/ansible.cfg
$ cat /etc/ansible/ansible.cfg | grep -v "#" | grep -v ^$
```

#### Example output
- Just for reference, if you need to compare your lab output to someone else's.
- You don't need to refer this if your steps go smooth.

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
deprecation_warnings = False
retry_files_enabled = False
:
cisco@ansible-controller:~$
```
### Reference
> This is for later use. Skip for now. http://docs.ansible.com/ansible/latest/reference_appendices/config.html#ansible-configuration-settings
> - Changes can be made and used in a configuration file which will be searched for in the following order:
>   - ANSIBLE_CONFIG (environment variable if set)
>   - ansible.cfg (in the current directory)
>   - ~/.ansible.cfg (in the home directory)
>   - /etc/ansible/ansible.cfg
> - Ansible will process the above list and use the first file found, all others are ignored.

---

### 1.2.4 Inventory file
- Inventory file is preconfigured for you. We added your router IP addresses in two groups: IOS and XR.
- Review the inventory file:

```
$ grep inventory /etc/ansible/ansible.cfg | grep hosts
$ cat /etc/ansible/hosts
$ cat /etc/ansible/hosts | grep -v "#" | grep -v ^$
```
- Verification:

```
$ ansible --list-hosts IOS
$ ansible --list-hosts XR
$ ansible --list-hosts ALL
$ ansible --list-hosts all
```
#### Example output
- Reference purpose only. Feel free to skip it.

```
cisco@ansible-controller:~$ grep inventory /etc/ansible/ansible.cfg | grep hosts
inventory      = /etc/ansible/hosts

cisco@ansible-controller:~$ grep -v "#" /etc/ansible/hosts | grep -v ^$
localhost
[IOS]
R1	ansible_host=172.16.101.XX ansible_user=cisco ansible_ssh_pass=cisco
[XR]
R2	ansible_host=172.16.101.XX ansible_user=cisco ansible_ssh_pass=cisco
[ALL:children]
IOS
XR
:
```
### Reference

> - This snippet is for future reference.  http://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html
> - It is possible to have multiple inventory files.

---

### 1.2.5 Ansible modules
- Ansible ships with several modules.
- Try the below commands for a **quick review**

```
$ ansible-doc --help
$ ansible-doc -l | grep ^ios_
$ ansible-doc -l | grep iosxr
$ ansible-doc -l
$ ansible-doc ios_command
```

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
### Reference

> For your research later; **not now**
> - http://docs.ansible.com/ansible/latest/modules/modules_by_category.html

---

### 1.2.6 Ad-hoc commands
- Let us use a few modules in this section: `raw`, `ios_command`, and `iosxr_command`
- Note that not all modules are suitable for use in ad-hoc command.
- Syntax: `ansible <devices> -m <module> -a <command>`
	- devices must exist in the inventory file
- Execute the below ad-hoc commands (from your home directory, /home/cisco)

```
$ ansible --list-hosts IOS
$ ansible ALL -m raw -a "show clock"
$ ansible all -m raw -a "ping vrf Mgmt-intf 172.16.101.93"
$ ansible IOS --connection local -m ios_command -a "commands='show ip route summ'"
$ ansible XR --connection local -m iosxr_command -a "commands='show route summ'"
```


### Optional exercises

> - Do this section if you are ahead of schedule, else skip and come back later.
> - This lab will be available to you for a few days after Cisco Live.
> - It is possible to use ad-hoc commands with more arguments, as below:
> ```
> $ ansible IOS -c local -m ios_command -a "authorize=true commands='show run int gig1'"
> $ ansible IOS -c local -m ios_command -a "username=cisco password=cisco auth_pass=cisco authorize=true commands='show run int gig1'"
> $ ansible XR -c local -m iosxr_facts -a "username=cisco password=cisco gather_subset=hardware"
> ```

### Conclusion

- Review the section and discuss if you have any questions.


#### Example output

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
cisco@ansible-controller:~$ ansible --list-hosts all
  hosts (2):
    172.16.101.91
    172.16.101.92
cisco@ansible-controller:~$
cisco@ansible-controller:~$ ansible ALL -m raw -a "show clock"
172.16.101.91 | SUCCESS | rc=0 >>

*03:27:05.914 UTC Sun Jun 3 2018Shared connection to 172.16.101.91 closed.
Connection to 172.16.101.91 closed by remote host.


172.16.101.92 | SUCCESS | rc=0 >>


Sun Jun  3 03:28:36.549 UTC
03:28:36.589 UTC Sun Jun 3 2018

:

cisco@ansible-controller:~$ ansible all -m raw -a "ping vrf Mgmt-intf 172.16.101.93"
172.16.101.91 | SUCCESS | rc=0 >>

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 172.16.101.93, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 msShared connection to 172.16.101.91 closed.
Connection to 172.16.101.91 closed by remote host.


172.16.101.92 | SUCCESS | rc=0 >>


Sun Jun  3 03:28:53.207 UTC
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 172.16.101.93, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
:
cisco@ansible-controller:~$ ansible IOS --connection local -m ios_command -a "commands='show ip route summ'"
172.16.101.91 | SUCCESS => {
    "changed": false,
    "stdout": [
        "IP routing table name is default (0x0)\nIP routing table maximum-paths is 32\nRoute Source    Networks    Subnets     Replicates  Overhead    Memory (bytes)\napplication     0           0           0           0           0\nconnected       0           4           0           384         1216\nstatic          0           0           0           0           0\nospf 1          0           1           0           96          308\n  Intra-area: 1 Inter-area: 0 External-1: 0 External-2: 0\n  NSSA External-1: 0 NSSA External-2: 0\nbgp 1           0           0           0           0           0\n  External: 0 Internal: 0 Local: 0\ninternal        3                                               1432\nTotal           3           5           0           480         2956"
    ],
    "stdout_lines": [
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
cisco@ansible-controller:~$ ansible XR --connection local -m iosxr_command -a "commands='show route summ'"
172.16.101.92 | SUCCESS => {
    "changed": false,
    "stdout": [
        "Route Source                     Routes     Backup     Deleted     Memory(bytes)\nlocal                            5          0          0           800          \nconnected                        1          4          0           800          \ndagr                             0          0          0           0            \nospf 1                           1          0          0           160          \nbgp 1                            0          0          0           0            \nTotal                            7          4          0           1760"
    ],
    "stdout_lines": [
        [
            "Route Source                     Routes     Backup     Deleted     Memory(bytes)",
            "local                            5          0          0           800          ",
            "connected                        1          4          0           800          ",
            "dagr                             0          0          0           0            ",
            "ospf 1                           1          0          0           160          ",
            "bgp 1                            0          0          0           0            ",
            "Total                            7          4          0           1760"
        ]
    ]
}
```

---

# Part-2: Playbook primer
- Ansible playbook exercises require creating YAML style playbook files.
- There are two options to create playbook files.
	- Option-1: Create the file in "Atom" editor, on the laptop. Atom is preconfigured with remote-sync: when you save the file in the default local directory(cl2018), it will automatically get copied to the home directory of your Ansible controller (/home/cisco).
	- Option-2: Use Ubuntu's "vi" or "vim" editor, which is included in your Ansible controller.
- Playbook files are also posted at: `https://github.com/gtamilse/ansible-lab/tree/master/playbooks`

## 2.1 Raw module
### Lab exercise

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
- Execute the play book:

```
$ ansible-playbook p1-raw.yml --syntax-check
$ ansible-playbook p1-raw.yml --step
$ ansible-playbook p1-raw.yml
```

- As you may have noticed, command output is not displayed.
- Create another playbook p3-raw.yml, to print the output from routers

```
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

```
$ ansible-playbook p3-raw.yml --syntax-check
$ ansible-playbook p3-raw.yml
```

### Conclusion

- In this section you used the raw module to collect and display command output from devices that are in the group, named ALL.
- Review the section and discuss if you have any questions.


### Example output

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
cisco@ansible-controller:~$ ansible-playbook p3-raw.yml --syntax-check

playbook: p3-raw.yml
cisco@ansible-controller:~$ ansible-playbook p3-raw.yml

PLAY [get interface info from all hosts] *************************************************************

TASK [execute show intf] *****************************************************************************
changed: [172.16.101.91]
changed: [172.16.101.92]

TASK [print data saved in the variable] **************************************************************
ok: [172.16.101.91] => {
    "P3_RAW_OUTPUT.stdout_lines": [
        "",
        "Interface              IP-Address      OK? Method Status                Protocol",
        "GigabitEthernet1       172.16.101.91   YES TFTP   up                    up      ",
        "GigabitEthernet2       10.0.0.5        YES TFTP   up                    up      ",
        "Loopback0              192.168.0.1     YES TFTP   up                    up      ",
        "Loopback1              1.1.1.1         YES manual up                    up      "
    ]
}
ok: [172.16.101.92] => {
    "P3_RAW_OUTPUT.stdout_lines": [
        "",
        "",
        "Sun Jun  3 19:07:57.578 UTC",
        "",
        "Interface                      IP-Address      Status          Protocol Vrf-Name",
        "Loopback0                      192.168.0.2     Up              Up       default ",
        "Loopback1                      1.1.1.2         Up              Up       default ",
        "Loopback99                     1.1.1.99        Up              Up       default ",
        "Loopback102                    1.1.1.102       Up              Up       default ",
        "MgmtEth0/0/CPU0/0              172.16.101.92   Up              Up       Mgmt-intf",
        "GigabitEthernet0/0/0/0         10.0.0.6        Up              Up       default "
    ]
}

PLAY RECAP *******************************************************************************************
172.16.101.91              : ok=2    changed=1    unreachable=0    failed=0
172.16.101.92              : ok=2    changed=1    unreachable=0    failed=0
```

---

## 1.4 IOS command module
### Lab exercise
- Use the ios_command to execute some exec level commands on IOS devices.
- Create a playbook, p4-ioscmd.yml, with the below contents

```
---
- name: collect ip route summary from all IOS devices
  hosts: IOS
  connection: local

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
- Predict the outcome of this playbook.
- Execute the playbook
- After reviewing the playbook output, try run the playbook in verbose mode: with -v, -vv, or -vvv

```
$ ansible-playbook p4-ioscmd.yml --syntax-check
$ ansible-playbook p4-ioscmd.yml
$ ansible-playbook p4-ioscmd.yml -v
```

### Conclusion
- In this section you used the ios_command module to collect and display command output from an IOS device.
- Review the section and discuss if you have any questions

### Reference

> Reference:
> - this is for future reference only (don't spend time on this now).
> - Pay attention to sections: parameters and return values.
> - http://docs.ansible.com/ansible/latest/modules/ios_command_module.html
> - http://docs.ansible.com/ansible/latest/modules/modules_by_category.html


### Example output

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
### Lab exercise
- Use the iosxr_command to execute some exec level commands on XR devices.
- Create a playbook, p5-xrcmd.yml, with the below contents

```
---
- name: collect ip route summary from all XR devices
  hosts: XR
  connection: local

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
- Predict the outcome of this playbook.
- Execute the playbook

```
$ ansible-playbook p5-xrcmd.yml --syntax-check
$ ansible-playbook p5-xrcmd.yml -v
```

### Conclusion
- In this section you used the xr_command module to collect and display command output from an XR router.
- Review the section and discuss if you have any questions.

### Optional exercise
> - Do this section if you are ahead of schedule, else skip and come back later.
> - This lab will be available for you to work for a few days after Cisco Live. You have the option of doing this later as well.
> - Write a playbook to meet the below requirements:
>   - Collect output of route summary from both IOS and XR routers
>   - Use the modules, ios_command and iosxr_command
>   - Write 2 plays within one playbook.
> - Playbook answer is in the appendix section.

### Example output
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

---

## 1.6 IOS config module
### Lab exercise
- Use ios_config module to configure a loopback interface on an IOS router.
- Create a playbook, p6-iosconfig.yml, with the below contents:

```
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

- Execute the playbook
- Check if loopback101 interface is created by p6-iosconfig.yml playbook

```
$ ansible IOS -m raw -a "show run int loop101"

$ ansible-playbook p6-iosconfig.yml -v

$ ansible IOS -m raw -a "show run int loop101"
```

### Conclusion
-  In this section you used the ios_config module to configure a loopback interface on an IOS router.
- Review the section and discuss if you have any questions

### Example output
```
cisco@ansible-controller:~$ ansible IOS -m raw -a "show run int loop101"
172.16.101.91 | SUCCESS | rc=0 >>

Line has invalid autocommand "show run int loop101"Shared connection to 172.16.101.91 closed.
Connection to 172.16.101.91 closed by remote host.


cisco@ansible-controller:~$ ansible-playbook p6-iosconfig.yml

PLAY [configure loopback1 interface on IOS devices] **********************************************************************************************

TASK [configure loopback101 interface] ***********************************************************************************************************
changed: [172.16.101.91]

PLAY RECAP ***************************************************************************************************************************************
172.16.101.91              : ok=1    changed=1    unreachable=0    failed=0

cisco@ansible-controller:~$ ansible IOS -m raw -a "show run int loop101"
172.16.101.91 | SUCCESS | rc=0 >>

Building configuration...

Current configuration : 98 bytes
!
interface Loopback101
 description test config by p6
 ip address 1.1.1.101 255.255.255.255
end
Shared connection to 172.16.101.91 closed.
Connection to 172.16.101.91 closed by remote host.
```
---

## 1.7 XR config module
### Lab exercise
- Use xr_config module to configure an access-list on a XR router.
- Create a playbook, p7-xrconfig.yml, with the below contents.

```
---
- name: configure ACL test7 on all XR devices
  hosts: XR
  connection: local

  tasks:
    - name: configure acl test7
      iosxr_config:
        parents: ipv4 access-list test7
        lines:
          - 10 permit ipv4 host 1.1.1.1 any
          - 20 permit ipv4 host 2.2.2.2 any
          - 30 permit ipv4 host 3.3.3.3 any
```

- Predict the outcome of executing the above playbook
- Check the ACL before configuring:
- Run the playbook
- Check for the post-playbook config

```
$ ansible XR -m raw -a "sho run ipv4 access-list"

$ ansible-playbook p7-xrconfig.yml -v

$ ansible XR -m raw -a "sho run ipv4 access-list"
```

### Conclusion
- In this section you used the xr_config module to configure an access-list on an XR router.
- Review the section and discuss if you have any questions

### Example output
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
- Ansible uses variables to enable more flexibility in playbooks.
- Variables are used to store information. This information can be used in a playbook by calling the specific variable.

### Lab exercise
- Create custom variables inside a playbook to store an interface name and call the variable to complete the show command execution.
- Create a playbook, p8-vars.yml, with the below contents

```
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

    - name: print stuff, style-1
      debug:
        var: BLAH

    - name: print stuff, style-2
      debug:
        var: BLAH.stdout_lines[0]

    - name: print stuff, style-2
      debug:
        var: BLAH.stdout_lines[1]
```
- Predict the outcome of executing the above playbook
- Run the playbook

```
$ ansible-playbook p8-vars.yml --syntax-check

$ ansible-playbook p8-vars.yml -v
```


### Optional exercise
> - Create a playbook to print messages, "This is <IOS router hostname>" and "This is <XR router hostname>".
> - Use default variable, "inventory_hostname". The messages will look like: "This is R1" and "This is R2"
> - Solution playbook (op8-vars.yml) is given in the Appendix section
>

### Conclusion
- In this section you created variables inside a playbook and recalled the variables to complete a task execution.
- When multiple commands are there, "register" keeps track of each command output output. We used BLAH.stdout_lines[n] to print them out separately.
- Do not distract yourself too much on print formatting, now. Stay focused on playbooks.
- Review the section and discuss if you have any questions


### Example output
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


### Reference
> - This is a simple exercise on variables; recommend to read below pages for more advanced use cases.
> - http://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html
> - https://gist.github.com/andreicristianpetcu/b892338de279af9dac067891579cad7d
>

---

## 1.9 Loops
- Loops are used to perform a task repeatedly with a set of different items.

### Lab exercise
- Utilize Ansible loops to simplify execution of multiple show commands.
- Create a playbook, p9-loops.yml, with the below contents

```
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

```
$ ansible-playbook p9-loops.yml --syntax-check

$ ansible-playbook p9-loops.yml -v
```
### Conclusion
- In this section you created a loop to iterate the execution of multiple show commands.
- Review the section and discuss if you have any questions

### Example output
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

### Reference

> Reference: http://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html


---

## 1.10 Conditionals

- Conditionals are used to decide whether to run a task or not.
- In this section, you will be working on "when" condition.

### Lab exercise
- Collect route summary data by using appropriate command based on the router’s OS
- Create a playbook, p10-conditionals.yml, with the below contents

```
---
- name: get route summary from IOS and XR routers
  hosts: ALL
	connection: local

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

- Predict the outcome of executing the above playbook
- Run the playbook:

```
$ ansible-playbook p10-conditionals.yml --syntax-check

$ ansible-playbook p10-conditionals.yml -v
```

### Conclusion
- In this section you created a conditional statement to use a show command based on the Router OS.
- Review the section and discuss if you have any questions

### Optional exercise
> - Create a playbook, which will:
>   - Detect router OS
>   - If a router has IOS, print message, "'hostname' is a IOS router" and if a router has XR, print, "'hostname' is a XR router"
>   - Playbook need to find the router names dynamically from the invenotry file.
> - Solution playbook, op10-conditionals.yml is included in the Appendix section.

### Example output
```
cisco@ansible-controller:~$ ansible-playbook p10-conditionals.yml --syntax-check

playbook: p10-conditionals.yml
cisco@ansible-controller:~$ ansible-playbook p10-conditionals.yml

PLAY [get route summary from IOS and XR routers] *************************************************************************************************

TASK [collect version info] **********************************************************************************************************************
changed: [R1]
changed: [R2]

TASK [run "show ip route summ" on IOS routers] ***************************************************************************************************
skipping: [R2]
changed: [R1]

TASK [debug] *************************************************************************************************************************************
ok: [R1] => {
    "IOSRTR.stdout_lines": [
        "",
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
}
ok: [R2] => {
    "IOSRTR.stdout_lines": "VARIABLE IS NOT DEFINED!"
}

TASK [run "show route summ" on XR routers] *******************************************************************************************************
skipping: [R1]
changed: [R2]

TASK [debug] *************************************************************************************************************************************
ok: [R1] => {
    "XRRTR.stdout_lines": "VARIABLE IS NOT DEFINED!"
}
ok: [R2] => {
    "XRRTR.stdout_lines": [
        "",
        "",
        "Mon Jun  4 03:09:34.788 UTC",
        "Route Source                     Routes     Backup     Deleted     Memory(bytes)",
        "local                            5          0          0           800          ",
        "connected                        1          4          0           800          ",
        "dagr                             0          0          0           0            ",
        "ospf 1                           1          0          0           160          ",
        "bgp 1                            0          0          0           0            ",
        "Total                            7          4          0           1760         ",
        ""
    ]
}

PLAY RECAP ***************************************************************************************************************************************
R1                         : ok=4    changed=2    unreachable=0    failed=0
R2                         : ok=4    changed=2    unreachable=0    failed=0

cisco@ansible-controller:~$
```


### Reference
> Reference: http://docs.ansible.com/ansible/latest/user_guide/playbooks_conditionals.html#the-when-statement

---

## 1.11 Importing playbooks
- Ansible allows for one playbook to import and execute another playbook using the import_playbook module.
- We will be calling (or importing) using import_playbook module.

### Lab exercise
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
- Predict the outcome of executing the playbook, p11-import.yml
- Run the playbook



```
$ cat p4-ioscmd.yml

$ cat p5-xrcmd.yml

$ ansible-playbook p11-import.yml --syntax-check

$ ansible-playbook p11-import.yml
```

### Conclusion
- In this section you created a playbook to import and execute two other playbooks, rather than recreating the same playbook content again.
- Review the section and discuss if you have any questions.

### Example output

```
cisco@ansible-controller:~$ ansible-playbook p11-import.yml

PLAY [collect ip route summary from all IOS devices] *********************************************************************************************

TASK [execute route summary command] *************************************************************************************************************
ok: [R1]

TASK [print output] ******************************************************************************************************************************
ok: [R1] => {
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

PLAY [collect ip route summary from all XR devices] **********************************************************************************************

TASK [execute route summary command] *************************************************************************************************************
ok: [R2]

TASK [print output] ******************************************************************************************************************************
ok: [R2] => {
    "P5_OUTPUT.stdout_lines": [
        [
            "Route Source                     Routes     Backup     Deleted     Memory(bytes)",
            "local                            5          0          0           800          ",
            "connected                        1          4          0           800          ",
            "dagr                             0          0          0           0            ",
            "ospf 1                           1          0          0           160          ",
            "bgp 1                            0          0          0           0            ",
            "Total                            7          4          0           1760"
        ]
    ]
}

PLAY RECAP ***************************************************************************************************************************************
R1                         : ok=2    changed=0    unreachable=0    failed=0
R2                         : ok=2    changed=0    unreachable=0    failed=0

cisco@ansible-controller:~$
```
---

## 1.12 Ansible Vault
- This section is **optional** and can be skipped. Automation exercises in this lab guide do not depend on Vault.
- Do this session if you are on time. You can do this later if you run out of time.
- Ansible vault has many security features but this section will cover only the basics of encrypting and decrypting a file.

### Objective
- Encrypt the inventory (/etc/hosts) file using Ansible Vault, so router login data is not stored in plain text.

### Lab exercise
- Use Ansible vault to encrypt and decrypt the inventory file.

#### Pre-check

- Pay attention the owner and file privileges (`-rw-r--r--`).
- Make sure the playbook runs without any errors

```
$ ls -l /etc/ansible/hosts

$ cat /etc/ansible/hosts

$ ansible-playbook p4-ioscmd.yml
```
#### Encrypt inventory file and execute a playbook

- Steps summary
  - Encrypt the inventory file using `ansible-vault` command.
    - sudo password: `cisco`
    - New vault password: `cisco123`
  - Read the encrypted file
  - Playbook execution without vault-key will fail.
  - Read the failure error message about inventory file
  - Execute the playbook with vault-key
  - supply sudo and vault passwords as prompted.
  - The playbook should run fine now.

```
$ ansible-vault --help

$ sudo ansible-vault encrypt /etc/ansible/hosts

$ sudo cat /etc/ansible/hosts

$ sudo ansible-vault view /etc/ansible/hosts

$ sudo ansible-playbook p4-ioscmd.yml

$ sudo ansible-playbook p4-ioscmd.yml --ask-vault-pass
```
#### Decrypt and restore

- Steps summary
  - Pay attention the owner and file privileges. Notice that the file permissions are not changed back to the original values.
  - Change the file permissions to the previous settings.
  - Make sure that the file permissions are: `-rw-r--r--`
  - Ensure the playbook runs successfully before proceeding to next section.

```
$ sudo ansible-vault decrypt /etc/ansible/hosts

$ ls -l /etc/ansible/hosts

$ sudo chmod 644 /etc/ansible/hosts

$ ls -l /etc/ansible/hosts

$ cat /etc/ansible/hosts

$ ansible-playbook p4-ioscmd.yml
```
### Conclusion
- Ansible files can be encrypted and decrypted using Vault.
- It is possible to use the encrypted files in playbooks using the option --ask-vault-pass
-Note: When vault encrypted the file it changed the file permissions; but when it decrypted the file it did not restore the old permissions.
- Review the section and discuss if you have any questions.

### Example output
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

# 2 Automating common tasks
- The following exercises will use Ansible to automate certain network operations tasks:
  - Router configuration backup
  - Proactive heath monitoring
  - Method of procedure
  - Generate iBGP config using roles
  - Generating bulk configuration

---
## 2.1 Router config backup
### Objective
- Create a playbook to capture and backup a router’s running config.

### Approach
- We can one play with two tasks.
- Task-1 will collect the config and save it to a variable. (use raw module)
- Task-2 will save the contents of the variable into a file. (use copy module)

### Lab exercise
- Review the contents in the playbook below.
- Task-1 collects and saves running-config in a variable named as RUNCFG
- Task-2 saves the contents of RUNCFG in a file in the home directory.
- Task-2 contains a well known variable (aka default variable) called, inventory_hostname. It means, "current inventory device", the router on which the tasks are run.
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
- Look for config files in `/home/cisco` directory

```
$ ansible-playbook p21-confback.yml --syntax-check

$ ansible-playbook p21-confback.yml

$ ls -l R*.txt
```

### Conclusion
- You applied two modules, raw and copy, to get the router config.
- Note that the file names may be overwritten when you rerun the playbook. And, you need to manually run the playbook.
- The optional exercise at the end of this section addresses the above tow problems and enhance this playbook. Feel free to follow the optional playbook if you want to review the other playbook.
- Review the section and discuss if you have any questions.


### Optional exercise
> - If you are interested, do this after completing all the exercises.
> - Create a playbook to backup routers' config, with the below requirements:
>   - Filename of the config files should include current timestamp to maintain uniqueness in filenames.
>   - Config backup should be taken daily at 03:00 hrs UTC. Use linux cronjob for this task.
> - Execute your playbook and verify if the results meet the requirements.
> - Fyi, a solution playbook file, op21-confback.yml is given in the appendix section for your reference.


### Reference

> - Copy module: http://docs.ansible.com/ansible/latest/modules/copy_module.html
> - inventory_hostname: http://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html

### Example output

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

---
## Device health monitoring

### Objective

- Create a playbook to collect critical data, process, and report issues.

### Approach

- Collect critical data by using iosxr_command module
- Use conditional functions to process the collected data and report any issues.
- Note: The playbook shown here is only applicable for XR devices. It can be modified for any other device by using the appropriate Ansible module.

### Lab exercise

- The first task will use the xr_command module to capture show command outputs from R2-XRv router.
- The second task will use the copy module to write the captured data into a file on the server. The inventory_hostname variable used here is a global variable, which means current device (out of the devices referred in "hosts"), that the tasks are run. Output of inventory_hostname will be R2; the hostname of the router defined in the inventory file.
- The tasks under the debug module use a conditional statement to check the captured data for abnormalities.

```
# PUT PLAYBOOK HERE
```

- Predict the outcome of running this playbook
- Run the playbook

```
$ ansible-playbook p32-xr-health-monitoring.yml --syntax-check

$ ansible-playbook p32-xr-health-monitoring.yml
```

### Conclusion

- Ansible playbooks can be leveraged for Proactive health monitoring to identify issues
- Operations can proactively identify faults by periodically running the playbook in network
- Data collected can be used in-line or offline for analysis


#### Example output

```
# PUT OUTPUT
```

---

## 2.2 Method of Procedure (MOP)

- MOP is a documented step-by-step sequence of tasks executed on network devices to achieve a planned objective. In this case, that objective is provisioning of OSPF.

### Objective
- Configure OSPF on both IOS and XR routers and enable OSPF on loopback0, GigEth2 interface on IOS routers and GigEth0/0/0/0 on XR routers.
- Do not configure if OSPF is already found in the router.

### Approach
- Step-1: Capture pre-config data, using a playbook
- Step-2: Configure OSPF on both the routers, using another playbook
- Step-3: Capture post-config data, using a third playbook
- Step-4: Include the first 3 playbooks in a different playbook

### Lab exercise

#### Step-1: Pre-config data capture
- Let us capture relavent data before configuring ospf on the routers
- Create a playbook, p22-precheck.yml, with the below contents
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
- Predict the outcome of this playbook.
- Run the playbook:
- Ensure that the playbook runs successfully and verify the existence of the postcheck files in the home dir, /home/cisco

```
$ ansible-playbook p22-precheck.yml --syntax-check

$ ansible-playbook p22-precheck.yml

$ ls -l p22-precheck*.txt
```

#### Step-2: Configure OSPF
- Let us make a playbook with two plays
	- play-1 to configure OSPF on IOS routers
	- play-2 to configure ospf on XR routers
- We should not configure OSPF if it is already configured. This is achieved using a module called, "meta". Here, "meta" will abort play (not the entire playbook; just that one play), when OSPF is found in the running config.
- Create a playbook, p22-config.yml, with the below contents:

```
---
- name: configure ospf on IOS routers - play-1
  hosts: IOS
  connection: local

  tasks:
    - name: pre-check for ospf config
      ios_command:
        commands:
          - show run | section ospf

      register: IOS_OSPF

    - meta: end_play
      when: IOS_OSPF.stdout | join('') | search('router ospf')

    - name: configure IOS ospf
      ios_config:
          parents: "router ospf 1"
          lines:
            - "router-id 192.168.0.1"
            - "passive-interface Loopback0"
            - "network 192.168.0.1 0.0.0.0 area 0"
            - "network 10.0.0.4 0.0.0.3 area 0"
          save_when: always

- name: configure ospf on XR routers - play-2
  hosts: XR
  connection: local

  tasks:
    - name: pre-check for ospf config
      iosxr_command:
        commands:
          - show run router ospf

      register: XR_OSPF

    - meta: end_play
      when: XR_OSPF.stdout | join('') | search('router ospf')

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

```
$ ansible IOS -m raw -a "sho run | sec ospf"

$ ansible XR -m raw -a "sho run router ospf"
```

- Important: If there is ospf config, **delete** it for this exercise.
- Run the playbook
- Verify that OSPF route exist on the routers.


```
$ ansible-playbook p22-config.yml --syntax-check

$ ansible-playbook p22-config.yml

$ ansible IOS -m raw -a "show ip ospf neigh"

$ ansible IOS -m raw -a "show ip route ospf"

$ ansible XR -m raw -a "show ospf neigh"

$ ansible XR -m raw -a "show route ospf"
```

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
- Predict the outcome of this playbook.
- Run the playbook:
- Ensure that the playbook runs successfully and verify the existence of the post-check files in the home dir, /home/cisco

```
$ ansible-playbook p22-postcheck.yml --syntax-check

$ ansible-playbook p22-postcheck.yml

$ ls -l p22-postcheck*.txt
```

#### Step-4: Include first 3 steps in a playbook
- Create a parent playbook and call the first 3 steps
- Create a playbook, p22-mop.yml, with the contents below

```
---
- name: pre-config captures
  import_playbook: p22-precheck.yml

- name: config OSPF
  import_playbook: p22-config.yml

- name: post-config captures
  import_playbook: p22-postcheck.yml
```
- Predict the outcome of this playbook
- Run the playbook:

```
$ ansible-playbook p22-mop.yml --syntax-check

$ansible-playbook p22-mop.yml
```

### Conclusion
- Review the section and discuss if you have any questions.
- Typical production MOP requires much more stringent checks. What we covered here is at basic level, for learning purpose.

### Optional exercise

> - Add the below requirements to the above MOP playbook:
>   - Insert a delay of 30 seconds before collecting post-config data
>   - Create a file with the differences between pre-config and post-config data
> - Execute your playbook and verify if the results meet the requirements.
> - Fyi, a solution playbook files (op-22-*.yml) are given in the appendix section for your reference.


### Reference

> copy module: http://docs.ansible.com/ansible/latest/modules/copy_module.html
> meta module: http://docs.ansible.com/ansible/latest/modules/meta_module.html
> pause: http://docs.ansible.com/ansible/latest/modules/pause_module.html
>

### Example output
```
cisco@ansible-controller:~$ ansible-playbook p22-precheck.yml --syntax-check

playbook: p22-precheck.yml
cisco@ansible-controller:~$ ansible-playbook p22-precheck.yml

PLAY [pre-config captures from IOS routers] *************************************************************

TASK [collect precheck IOS output] **********************************************************************
ok: [R1]

TASK [save output to a file] ****************************************************************************
changed: [R1]

PLAY [pre-config captures from XR routers] **************************************************************

TASK [collect precheck XR output] ***********************************************************************
ok: [R2]

TASK [save output to a file] ****************************************************************************
changed: [R2]

PLAY RECAP **********************************************************************************************
R1                         : ok=2    changed=1    unreachable=0    failed=0
R2                         : ok=2    changed=1    unreachable=0    failed=0

cisco@ansible-controller:~$ ls -l p22-precheck*.txt
-rw-rw-r-- 1 cisco cisco 1281 Jun  4 15:34 p22-precheck-ios.txt
-rw-rw-r-- 1 cisco cisco  865 Jun  4 15:34 p22-precheck-xr.txt
cisco@ansible-controller:~$ $ ansible IOS -m raw -a "sho run | sec ospf"
$: command not found
cisco@ansible-controller:~$  ansible IOS -m raw -a "sho run | sec ospf"
R1 | SUCCESS | rc=0 >>
Shared connection to 172.16.101.91 closed.
Connection to 172.16.101.91 closed by remote host.


cisco@ansible-controller:~$ ansible XR -m raw -a "sho run router ospf"
R2 | SUCCESS | rc=0 >>


Mon Jun  4 15:38:36.339 UTC
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


Connection to 172.16.101.92 closed by remote host.
Shared connection to 172.16.101.92 closed.


cisco@ansible-controller:~$ ansible-playbook p22-config.yml --syntax-check

playbook: p22-config.yml
cisco@ansible-controller:~$ ansible-playbook p22-config.yml

PLAY [configure ospf on IOS routers - play-1] ***********************************************************

TASK [pre-check for ospf config] ************************************************************************
ok: [R1]
[DEPRECATION WARNING]: Using tests as filters is deprecated. Instead of using `result|search` instead
use `result is search`. This feature will be removed in version 2.9. Deprecation warnings can be
disabled by setting deprecation_warnings=False in ansible.cfg.

TASK [configure IOS ospf] *******************************************************************************
changed: [R1]

PLAY [configure ospf on XR routers - play-2] ************************************************************

TASK [pre-check for ospf config] ************************************************************************
ok: [R2]

TASK [configure XR ospf] ********************************************************************************
changed: [R2]

PLAY RECAP **********************************************************************************************
R1                         : ok=2    changed=1    unreachable=0    failed=0
R2                         : ok=2    changed=1    unreachable=0    failed=0

cisco@ansible-controller:~$ ansible-playbook p22-postcheck.yml --syntax-check

playbook: p22-postcheck.yml
cisco@ansible-controller:~$ ansible-playbook p22-postcheck.yml

PLAY [post-config captures from IOS routers] ************************************************************

TASK [collect postcheck commands] ***********************************************************************
ok: [R1]

TASK [save output to a file] ****************************************************************************
changed: [R1]

PLAY [post-config captures from XR routers] *************************************************************

TASK [collect postcheck commands] ***********************************************************************
ok: [R2]

TASK [save output to a file] ****************************************************************************
changed: [R2]

PLAY RECAP **********************************************************************************************
R1                         : ok=2    changed=1    unreachable=0    failed=0
R2                         : ok=2    changed=1    unreachable=0    failed=0

cisco@ansible-controller:~$ ls -l p22-postcheck*.txt
-rw-rw-r-- 1 cisco cisco 1281 Jun  4 16:10 p22-postcheck-ios.txt
-rw-rw-r-- 1 cisco cisco  865 Jun  4 16:10 p22-postcheck-xr.txt

cisco@ansible-controller:~$ ansible-playbook p22-mop.yml --syntax-check

playbook: p22-mop.yml
cisco@ansible-controller:~$ ansible-playbook p22-mop.yml

PLAY [pre-config captures from IOS routers] *************************************************************

TASK [collect precheck IOS output] **********************************************************************
ok: [R1]

TASK [save output to a file] ****************************************************************************
changed: [R1]

PLAY [pre-config captures from XR routers] **************************************************************

TASK [collect precheck XR output] ***********************************************************************
ok: [R2]

TASK [save output to a file] ****************************************************************************
changed: [R2]

PLAY [configure ospf on IOS routers - play-1] ***********************************************************

TASK [pre-check for ospf config] ************************************************************************
ok: [R1]
[DEPRECATION WARNING]: Using tests as filters is deprecated. Instead of using `result|search` instead
use `result is search`. This feature will be removed in version 2.9. Deprecation warnings can be
disabled by setting deprecation_warnings=False in ansible.cfg.

PLAY [configure ospf on XR routers - play-2] ************************************************************

TASK [pre-check for ospf config] ************************************************************************
ok: [R2]

PLAY [post-config captures from IOS routers] ************************************************************

TASK [collect postcheck commands] ***********************************************************************
ok: [R1]

TASK [save output to a file] ****************************************************************************
changed: [R1]

PLAY [post-config captures from XR routers] *************************************************************

TASK [collect postcheck commands] ***********************************************************************
ok: [R2]

TASK [save output to a file] ****************************************************************************
changed: [R2]

PLAY RECAP **********************************************************************************************
R1                         : ok=5    changed=2    unreachable=0    failed=0
R2                         : ok=5    changed=2    unreachable=0    failed=0

```

---

## Generate iBGP config using roles and upload to routers

- For operators, network configuration and rollout are critical part of daily operations.
- This exercise will simulate a network config generation via roles and rollout of configs to network elements through automation.

### objectives

- Create a playbook to generate configuration based on role file structures.
- Understand the functioning of roles and its file structures

### Approach

- Show configurations can be generated for routers with different Network Operating Systems (NOS like IOS and XR) using Ansible roles and templates
- Show how Ansible modules can be used to upload the configuration to the routers
- Playbook will be run on “localhost” to generate the needed configurations and then will use a play to upload the config to the routers.

#### Lab Exercise

Step 1.Create two sub-folders by the name “csr-bgp”  and “xr-bgp” using mkdir command

```
cisco@ansible-controller:~$
cisco@ansible-controller:~$ mkdir csr-bgp xr-bgp
cisco@ansible-controller:~$
```

Step 2.Create tasks,templates and vars under both csr-bgp and xr-bgp folder.

```
cisco@ansible-controller:~$
cisco@ansible-controller:~$ mkdir csr-bgp/tasks csr-bgp/vars csr-bgp/templates
cisco@ansible-controller:~$
cisco@ansible-controller:~$ mkdir xr-bgp/tasks xr-bgp/vars xr-bgp/templates
cisco@ansible-controller:~$

```
Step 3. Create a playbook - roles-bgp.yml with the following Contents

```
---
- name: Generate router bgp configuration files using Roles and Jinja2 Templates
  hosts: localhost

  roles:
   - xr-bgp
   - csr-bgp

- name: Task to upload config to CSR
  hosts: IOS
  gather_facts: false
  connection: local
  tasks:
  - name: "Load iBGP configs for CSR1kv router using SRC option using IOS_CONFIG Module"
    ios_config:
      src: "/home/cisco/R1-CSR1K-BGP.txt"

- name: Task to upload config to R2-XRV
  gather_facts: false
  connection: local
  hosts: XR

  tasks:
  - name: "Load iBGP configs for xr router using SRC option of IOSXR_CONFIG Module "
    iosxr_config:
      src: "/home/cisco/R2-XRv-BGP.txt"

```
-  As can be seen above, this playbook has multiple plays. The first play will run “locally” on the host to generate configuration using “roles” options for both CSR1k/IOS and XRv routers.

- Next two plays will be used to upload the IOS BGP configuration to the R1/CSR1K router and XR BGP configuration to the R2/XRv router.

Step 4: Create main.yml fle with below contents.

Note: If done through vi editor, the file is to be created under csr-bgp/tasks folder. If done through ATOM editor, it is a 2 step process - copy to home directory and them move to tasks folder.
```
---
- name: Generate R1 CSR router iBGP config file
  template: src=CSR-BGP.j2 dest=/home/cisco/{{item.hostname}}-BGP.txt
  with_items: "{{router_list}}"
```
- The “main.yml” file in the tasks sub-folder identifies the Jinja2 template that contains “configuration template” for this play book specified using “template” src option.
- Output of the task will be written to the location given in the “dest” parameter.

Note: You will move the file from home directory to csr-bgp/tasks/mail.yml
```
cisco@ansible-controller:~$ mv main.yml csr-bgp/tasks/main.yml
cisco@ansible-controller:~$
```
Step 5: Create a file main.yml with following contents for defining the variables.

Note: If done through vi editor, the file is to be created under csr-bgp/vars folder. If done through ATOM editor, it is a 2 step process - copy to home directory and them move to vars folder.

```
---
router_list:
  -  hostname: R1-CSR1K
     profile: IOS-XE
     RID: 192.168.0.1
     PEER_IP: 192.168.0.2
     LCL_ASN: 1
     RMT_ASN: 1
```
- Vars file contains all values for parameters given in the Jinja2 template.

Note this “main.yml” has a single list “dictionary” which contains mapped key:value pairs to populate the iBGP configurations for the jinja2 template.
Note: Move the file to csr-bgp/vars/main.yml file.

```
cisco@ansible-controller:~$
cisco@ansible-controller:~$ mv main.yml csr-bgp/vars/main.yml
cisco@ansible-controller:~$
```
Step 6: Create the template CSR-BGP.J2 with following contents.

Note: If done through vi editor, the file is to be created under csr-bgp/templates folder. If done through ATOM editor, it is a 2 step process - copy to home directory and them move to temlates folder.

```
router bgp {{item.LCL_ASN}}
 bgp router-id {{item.RID}}
 bgp log-neighbor-changes
 neighbor {{item.PEER_IP}} remote-as {{item.RMT_ASN}}
 neighbor {{item.PEER_IP}} update-source Loopback0
!
```
Note: You will move the file from home directory to csr-bgp/template/CSR-BGP.j2

cisco@ansible-controller:~$
cisco@ansible-controller:~$ mv CSR-BGP.J2 csr-bgp/templates/.
cisco@ansible-controller:~$

Step 7: Repeat the following for XR-BGP device. Create an playbook main.yml with following Contents

Note: If done through vi editor, the file is to be created under xr-bgp/tasks folder. If done through ATOM editor, it is a 2 step process - copy to home directory and them move to tasks folder.

```
---
- name: Generate R2 XRV router iBGP config file
  template: src=XR-BGP.j2 dest=/home/cisco/{{item.hostname}}-BGP.txt
  with_items: "{{router_list}}"
```
 - and move to the xr-bgp/tasks folder

cisco@ansible-controller:~$
cisco@ansible-controller:~$ mv main.yml xr-bgp/tasks/main.yml
cisco@ansible-controller:~$

Step 8: Create an template file - XR-BGP.J2 with following contents and copy to xr-bgp/templates folders

Note: If done through vi editor, the file is to be created under xr-bgp/templates folder. If done through ATOM editor, it is a 2 step process - copy to home directory and them move to templates folder

```
router bgp {{item.LCL_ASN}}
 bgp router-id {{item.RID}}
 address-family ipv4 unicast
 !
 neighbor {{item.PEER_IP}}
  remote-as {{item.RMT_ASN}}
  update-source Loopback0
  address-family ipv4 unicast
  !
 !
!
```

Note: You will move the file from home directory to csr-bgp/template/XR-BGP.j2
```
cisco@ansible-controller:~$
cisco@ansible-controller:~$ mv XR-BGP.J2 xr-bgp/templates/.
cisco@ansible-controller:~$
```
Step 9: Create a main.yml with below contents.

Note: If done through vi editor, the file is to be created under xr-bgp/vars folder. If done through ATOM editor, it is a 2 step process - copy to home directory and them move to vars folder

```
router_list:
  -  hostname: R2-XRv
     profile: IOS-XR
     RID: 192.168.0.2
     PEER_IP: 192.168.0.1
     LCL_ASN: 1
     RMT_ASN: 1
```

- Vars file contains all values for parameters given in the Jinja2 template. Note this “main.yml” has a  single list “dictionary” which contains mapped key:value pairs to populate the iBGP configurations for the jinja2 template.


- move the file main.yml to xr-bgp

```
cisco@ansible-controller:~$
cisco@ansible-controller:~$ mv main.yml xr-bgp/vars/main.yml
cisco@ansible-controller:~$
```

Step 10: Run the tree command and validate that following files structure is created
```
cisco@ansible-controller:~$ tree csr-bgp
csr-bgp
├── tasks
│   └── main.yml
├── templates
│   └── CSR-BGP.J2
└── vars
    └── main.yml

3 directories, 3 files

cisco@ansible-controller:~$ tree xr-bgp
xr-bgp
├── tasks
│   └── main.yml
├── templates
│   └── XR-BGP.J2
└── vars
    └── main.yml
```

Step 11: Execute the playbook - roles-bgp.yml.

```
cisco@ansible-controller:~/roles-templates$ ansible-playbook roles-bgp.yml
```

Step 9: Confirm that playbook runs with no errors and check that there are two files ending with name “\*-BGP.txt” has been created in the home directory

```
cisco@ansible-controller:more cfg/R1-CSR1K-BGP.txt
router bgp 1
 bgp router-id 192.168.0.1
 bgp log-neighbor-changes
 neighbor 192.168.0.2 remote-as 1
 neighbor 192.168.0.2 update-source Loopback0
!

cisco@ansible-controller:more cfg/R2-XRv-BGP.txt
router bgp 1
 bgp router-id 192.168.0.2
 address-family ipv4 unicast
 !
 neighbor 192.168.0.1
  remote-as 1
  update-source Loopback0
  address-family ipv4 unicast
  !
 !
!
```
### Conclusion

- Necessary files and file structure for roles needs to be created.
- Irrespective of roles, the file structure is the same
- Ansible Roles can be utilized to generate config small or big
- Leverage module to load configuration onto to devices

#### Example output:

cisco@ansible-controller:~$ansible-playbook roles-bgp.yml

PLAY [Generate router bgp configuration files using Roles and Jinja2 Templates] ***************************************************************

TASK [xr-bgp : Generate R2 XRV router iBGP config file] ***************************************************************************************
changed: [localhost] => (item={u'profile': u'IOS-XR', u'LCL_ASN': 1, u'RMT_ASN': 1, u'hostname': u'R2-XRv', u'PEER_IP': u'192.168.0.1', u'RID': u'192.168.0.2'})

TASK [csr-bgp : Generate R1 CSR router iBGP config file] **************************************************************************************
changed: [localhost] => (item={u'profile': u'IOS-XE', u'LCL_ASN': 1, u'RMT_ASN': 1, u'hostname': u'R1-CSR1K', u'PEER_IP': u'192.168.0.2', u'RID': u'192.168.0.1'})

PLAY [Task to upload config to CSR] ***********************************************************************************************************

TASK [Load iBGP configs for CSR1kv router using SRC option using IOS_CONFIG Module] ***********************************************************
changed: [172.16.101.98]

PLAY [Task to upload config to R2-XRV] ********************************************************************************************************

TASK [Load iBGP configs for xr router using SRC option of IOSXR_CONFIG Module] ****************************************************************
changed: [172.16.101.99]

PLAY RECAP ************************************************************************************************************************************
172.16.101.98              : ok=1    changed=1    unreachable=0    failed=0
172.16.101.99              : ok=1    changed=1    unreachable=0    failed=0
localhost                  : ok=2    changed=2    unreachable=0    failed=0

cisco@ansible-controller:~$

Streach Exercise: Use Ansible ad-hoc command or create a simple playbook to check if the iBGP configuration has been added and the iBGP session between R1 and R2 is established.

---

# Appendix

## Reference
- Ansible Documentation: http://docs.ansible.com/ansible/latest/index.html
- YAML Version 1.2 Specs: http://www.yaml.org/spec/1.2/spec.html
- Jinjia2 Templating: http://jinja.pocoo.org/docs/dev/templates/
- Ansible installation: https://www.youtube.com/watch?v=NIEVaCBGOUc
- Introduction to YAML: https://www.youtube.com/watch?v=o9pT9cWzbnI
- Ansible - Playbooks for beginners: https://www.youtube.com/watch?v=Z01b9QZG0D0
- Python Configuration generation: - Kirk Byers Blog – Network configuration using Ansbile
- Ansible Up and Running – Lorin Hochstein

---

## Ansible installation
- Ansible control machine is on Linux based systems with Python 2 (versions 2.6 or higher) or Python 3 (versions 3.5 or higher).
- Red Hat, Debian, CentOS, OS X (MAC OS), Ubuntu, BSDs etc. are supported. MS Windows OS is not supported.
- Installation steps are straight forward. Depending on your OS flavor, pick the steps from the installation guide.
- Ansible installation guide: http://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html
- Just for example, installtion steps for CentOS:

```
$ sudo yum update

$ sudo yum install ansible
```

- Installation steps for Ubuntu:

```
$ sudo apt-get update

$ sudo apt-get install software-properties-common

$ sudo apt-add-repository ppa:ansible/ansible

$ sudo apt-get update

$ sudo apt-get install ansible
```

---

## Optional exercise op5-cmd.yml

### Lab exercise
- playbook op5-cmd.xml


```
---
  - name: play-1:get route summary from IOS routers
    hosts: IOS
    connection: local

    tasks:
      - name: execute IOS route summary command
        ios_command:
          commands:
            show ip route summary

        register: IOS_OUTPUT

      - name: print output
        debug:
          var: IOS_OUTPUT.stdout_lines

  - name: play-2:get route summary from XR routers
    hosts: XR
    connection: local

    tasks:
      - name: execute XR route summary command
        iosxr_command:
          commands:
            show route summary

        register: XR_OUTPUT

      - name: print output
        debug:
          var: XR_OUTPUT.stdout_lines
```

### Example output
```
cisco@ansible-controller:~$ ansible-playbook op5-cmd.yml

PLAY [play-1:get route summary from IOS routers] *******************************************************************

TASK [execute IOS route summary command] ***************************************************************************
ok: [R1]

TASK [print output] ************************************************************************************************
ok: [R1] => {
    "IOS_OUTPUT.stdout_lines": [
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

PLAY [play-2:get route summary from XR routers] ********************************************************************

TASK [execute XR route summary command] ****************************************************************************
ok: [R2]

TASK [print output] ************************************************************************************************
ok: [R2] => {
    "XR_OUTPUT.stdout_lines": [
        [
            "Route Source                     Routes     Backup     Deleted     Memory(bytes)",
            "local                            5          0          0           800          ",
            "connected                        1          4          0           800          ",
            "dagr                             0          0          0           0            ",
            "ospf 1                           1          0          0           160          ",
            "bgp 1                            0          0          0           0            ",
            "Total                            7          4          0           1760"
        ]
    ]
}

PLAY RECAP *********************************************************************************************************
R1                         : ok=2    changed=0    unreachable=0    failed=0
R2                         : ok=2    changed=0    unreachable=0    failed=0

```

---
## Optional exercise op8-vars.yml
- playbook, op8-vars.yml

```
---
- name: print hostnames of all devices
  hosts: ALL
  connection: local

  tasks:
    - name: print hostname
      debug:
        msg: "This is {{ inventory_hostname }}"
```

### Example output
```
cisco@ansible-controller:~$ ansible-playbook op8-vars.yml --syntax-check

playbook: op8-vars.yml
cisco@ansible-controller:~$ ansible-playbook op8-vars.yml

PLAY [print hostnames of all devices] ******************************************************************************

TASK [print hostname] **********************************************************************************************
ok: [R1] => {
    "msg": "This is R1"
}
ok: [R2] => {
    "msg": "This is R2"
}

PLAY RECAP *********************************************************************************************************
R1                         : ok=1    changed=0    unreachable=0    failed=0
R2                         : ok=1    changed=0    unreachable=0    failed=0

cisco@ansible-controller:~$
```
---
## Optional exercise op10-conditionals.yml
- playbook, op10-conditionals.yml

```
---
- name: print each host and its OS
  hosts: ALL

  tasks:
    - name: collect OS
      raw: show ver

      register: OS

    - name: print IOS host
      debug:
        msg: "{{ inventory_hostname }} is an IOS Router."
      when: OS.stdout | join('') | search('IOS XE')

    - name: print XR host
      debug:
        msg: "{{ inventory_hostname }} is an XR Router."
      when: OS.stdout | join('') | search('IOS XR')
```

### Example output

```
cisco@ansible-controller:~$ ansible-playbook op10-conditionals.yml --syntax-check

playbook: op10-conditionals.yml
cisco@ansible-controller:~$ ansible-playbook op10-conditionals.yml

PLAY [print each host and its OS] **********************************************************************************

TASK [collect OS] **************************************************************************************************
changed: [R1]
changed: [R2]

TASK [print IOS host] **********************************************************************************************
skipping: [R2]
ok: [R1] => {
    "msg": "R1 is an IOS Router."
}

TASK [print XR host] ***********************************************************************************************
skipping: [R1]
ok: [R2] => {
    "msg": "R2 is an XR Router."
}

PLAY RECAP *********************************************************************************************************
R1                         : ok=2    changed=1    unreachable=0    failed=0
R2                         : ok=2    changed=1    unreachable=0    failed=0

cisco@ansible-controller:~$
```

---
## Optional exercise op21-confback.yml
### Step-1: Playbook, op21-confback.yml

```
---
- name: backup all routers config
  hosts: all

  tasks:
    - name: collect config  from all routers
      connection: ssh
      raw: show run

      register: RUNCFG

    - set_fact: time="{{lookup('pipe','date \"+%Y-%m-%d-%H-%M\"')}}"

    - name: save output to a file
      connection: local
      copy:
        content: "{{ RUNCFG.stdout }}"
        dest: "/home/cisco/{{ inventory_hostname }}_{{ time }}.txt"
```

### Step-2: Cronjob
- Append the below entry in /etc/crontab file
- You need sudo permissions (sudo password=cisco): `$ sudo vi /etc/crontab`

```
#Run Ansible Playbook rtr-cfg-bkup everyday at 3:00 am UTC to backup router configs

0 3 * * * cisco /usr/bin/ansible-playbook /home/cisco/op21-confback.yml

```

### Reference
> set_fact: http://docs.ansible.com/ansible/latest/modules/set_fact_module.html
>
### Example output

```
cisco@ansible-controller:~$ cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user	command
17 *	* * *	root    cd / && run-parts --report /etc/cron.hourly
25 6	* * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6	* * 7	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6	1 * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )

#Run Ansible Playbook rtr-cfg-bkup everyday at 3:00 am UTC to backup router configs

0 3 * * * cisco /usr/bin/ansible-playbook /home/cisco/op21-confback.yml

cisco@ansible-controller:~$ ansible-playbook op21-confback.yml --syntax-check

playbook: op21-confback.yml
cisco@ansible-controller:~$ ansible-playbook op21-confback.yml

PLAY [backup all routers config] ***********************************************************************************

TASK [collect config  from all routers] ****************************************************************************
changed: [R1]
changed: [R2]

TASK [set_fact] ****************************************************************************************************
ok: [R1]
ok: [R2]

TASK [save output to a file] ***************************************************************************************
changed: [R1]
changed: [R2]

PLAY RECAP *********************************************************************************************************
R1                         : ok=3    changed=2    unreachable=0    failed=0
R2                         : ok=3    changed=2    unreachable=0    failed=0


cisco@ansible-controller:~$ ls -ltr R*

-rw-rw-r-- 1 cisco cisco 1973 Jun  5 18:34 R2_2018-06-05-18-34.txt
-rw-rw-r-- 1 cisco cisco 5018 Jun  5 18:34 R1_2018-06-05-18-34.txt
```
---

## Optional exercise op-22 (MOP)
- We have two additional requirements on top of the MOP playbook that we already did.
  - Insert a delay of 30 seconds before collecting post-config data
  - Create a file with the differences between pre-config and post-config data

### Approach
- We can reuse the pre-config captures playbook, p22-preconfig.yml, without any changes.
- p22-config.yml doesn't need any changes as well; we can reuse it.
- We need to add 30 sec. pause in post-config captures playbook, p22-postconfig.yml
- To create diff file, we need a new playbook

### Add delay
- Use "pause" module
- Create a playbook, op22-postconfig.yml

```
---
- name: post-config captures from IOS routers
  hosts: IOS
  connection: local

  tasks:
    - pause: seconds=30

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
        dest="/home/cisco/p22-o-postcheck-ios.txt"

- name: post-config captures from XR routers
  hosts: XR
  connection: local

  tasks:
    - pause: seconds=30

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
        dest="/home/cisco/p22-o-postcheck-xr.txt"

```

### Diff file
- Create playbook, op22-diff.yml, with the contents below

```
---
- name: Compare pre and post files and create a diff file
  hosts: localhost
  connection: local

  tasks:
    - name: diff pre and post files
      command: diff /home/cisco/p22-precheck-ios.txt /home/cisco/p22-o-postcheck-ios.txt

      register: IOS_DIFF

      failed_when: "IOS_DIFF.rc > 1"

    - set_fact: TIMESTAMP="{{lookup('pipe','date \"+%Y-%m-%d-%H-%M\"')}}"

    - name: create diff file with timestamp included in the name
      copy:
        content: "{{ IOS_DIFF.stdout }}"
        dest: "/home/cisco/ios_diff_{{ TIMESTAMP }}.txt"

```

### MOP playbook
- Create MOP playbook, op22-mop.yml, importing all the playbooks

```
---
- name: pre-config captures
  import_playbook: p22-precheck.yml

- name: config OSPF
  import_playbook: p22-config.yml

- name: post-config captures
  import_playbook: op22-postcheck.yml

- name: create diff file from pre and post
  import_playbook: op22-diff.yml
```

### Example output

```
cisco@ansible-controller:~$ ansible-playbook op22-mop.yml

PLAY [pre-config captures from IOS routers] ****************************************************************************************************

TASK [collect precheck IOS output] *************************************************************************************************************
ok: [R1]

TASK [save output to a file] *******************************************************************************************************************
changed: [R1]

PLAY [pre-config captures from XR routers] *****************************************************************************************************

TASK [collect precheck XR output] **************************************************************************************************************
ok: [R2]

TASK [save output to a file] *******************************************************************************************************************
changed: [R2]

PLAY [configure ospf on IOS routers - play-1] **************************************************************************************************

TASK [pre-check for ospf config] ***************************************************************************************************************
ok: [R1]

PLAY [configure ospf on XR routers - play-2] ***************************************************************************************************

TASK [pre-check for ospf config] ***************************************************************************************************************
ok: [R2]

PLAY [post-config captures from IOS routers] ***************************************************************************************************

TASK [pause] ***********************************************************************************************************************************
Pausing for 30 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [R1]

TASK [collect postcheck commands] **************************************************************************************************************
ok: [R1]

TASK [save output to a file] *******************************************************************************************************************
changed: [R1]

PLAY [post-config captures from XR routers] ****************************************************************************************************

TASK [pause] ***********************************************************************************************************************************
Pausing for 30 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [R2]

TASK [collect postcheck commands] **************************************************************************************************************
ok: [R2]

TASK [save output to a file] *******************************************************************************************************************
changed: [R2]

PLAY [Compare pre and post files and create a diff file] ***************************************************************************************

TASK [set_fact] ********************************************************************************************************************************
ok: [localhost]

TASK [diff pre and post IOS files] *************************************************************************************************************
changed: [localhost]

TASK [create IOS diff file with timestamp included in the name] ********************************************************************************
changed: [localhost]

TASK [diff pre and post XR files] **************************************************************************************************************
changed: [localhost]

TASK [create XR diff file with timestamp included in the name] *********************************************************************************
changed: [localhost]

PLAY RECAP *************************************************************************************************************************************
R1                         : ok=6    changed=2    unreachable=0    failed=0
R2                         : ok=6    changed=2    unreachable=0    failed=0
localhost                  : ok=5    changed=4    unreachable=0    failed=0

cisco@ansible-controller:~$ ls -l op22-xr*
-rw-rw-r-- 1 cisco cisco 191 Jun  5 22:07 op22-xr_diff_2018-06-05-22-07.txt
```
---

**<p align="center">End of Lab Guide</p>**

---
