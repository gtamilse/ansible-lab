## 3.2 Device health monitoring

### Objective

- Create a playbook to collect critical data, process, and report issues.

### Approach

-	In this exercise, you will create a playbook to collect XR commands and monitor the router's health status.
- You will leverage conditional functions to process the collected data and report any issues.

### Lab exercise

- Review the contents in the playbook below.
- Task-1 collects and saves command outputs in a variable named as iosxr_mon
- Task-2 saves the contents of iosxr_mon in a file "R2_health_check.txt" in the home directory.
- Task-2 contains a well known variable (aka default variable) called, inventory_hostname. It means, "current inventory device", the router on which the tasks are run.
- Task-3 Does a conditionals check on collected contents, and prints the output
- Create the playbook with file name, p32-xr-health-monitoring.yml, with the contents below.


```
---
- name: XR Router Health Monitoring
  hosts: XR
  gather_facts: false
  connection: local

  tasks:
    - name: Device Health Monitoring
      iosxr_command:
        commands:
          - show platform
          - show redundancy
          - show proc cpu | ex "0%      0%       0%"
          - show memory sum location all | in "node|Pyhsical|available"
          - show ipv4 vrf all int bri
          - show route sum
          - show ospf neighbor
          - show mpls ldp neighbor | in "Id|Up"
          - show bgp all all sum | in "Address|^[0-9]+"

      register: iosxr_mon

    - name: save output to a file
      copy:
          content="\n\n ===show platform=== \n\n {{ iosxr_mon.stdout[0] }} \n\n ===show redundancy=== \n\n {{ iosxr_mon.stdout[1] }} \n\n ===show proc cpu=== \n\n {{ iosxr_mon.stdout[2] }} \n\n ===show memory summary=== \n\n {{ iosxr_mon.stdout[3] }} \n\n ===show ipv4 vrf all int bri=== \n\n {{ iosxr_mon.stdout[4] }} \n\n ===show route sum=== \n\n {{ iosxr_mon.stdout[5] }} \n\n ===show ospf nei=== \n\n {{ iosxr_mon.stdout[6] }} \n\n ===show mpls ldp neighbor=== \n\n {{ iosxr_mon.stdout[7] }} \n\n ===show bgp sum=== \n\n {{iosxr_mon.stdout[8] }}"
           dest="./{{ inventory_hostname }}_health_check.txt"

    - debug:
        msg: " {{ inventory_hostname }} show_platform indicates card is down"
      when: iosxr_mon.stdout[0] | join('') | search('Down')

    - debug:
        msg: " {{ inventory_hostname }} show_redundancy indicates card is not present"
      when: iosxr_mon.stdout[1] | join('') | search('NSR not ready since Standby is not Present')

    - debug:
         msg: "{{ inventory_hostname }} CPU Utilization {{ iosxr_mon.stdout[2] }}"

    - debug:
        msg: " {{ inventory_hostname }} Memory Available: {{ iosxr_mon.stdout[3] }}"

    - debug:
        msg: " {{ inventory_hostname }} Interface is Down"
      when: iosxr_mon.stdout[4] | join('') | search('Down')

    - debug:
        msg: " {{ inventory_hostname }} Route Summary: {{iosxr_mon.stdout[5]}}"

    - debug:
        msg: " {{ inventory_hostname }} OSPF Summary: {{iosxr_mon.stdout[6]}}"
      when: iosxr_mon.stdout[6] | join('') | search('FULL')

    - debug:
        msg: " {{ inventory_hostname }} MPLS LDP Summary: {{iosxr_mon.stdout[7]}}"

    - debug:
        msg: " {{ inventory_hostname }} BGP Sessions Down: {{ iosxr_mon.stdout[8] }} "
      when: iosxr_mon.stdout[8] | join('') | search('Active')

```

- Predict the outcome of running this playbook
- Run the playbook
- look for the fike "R2_health_check.txt" for collected captures

```
$ ansible-playbook p32-xr-health-monitoring.yml --syntax-check

$ ansible-playbook p32-xr-health-monitoring.yml

$ ls -al R2_health_check.txt
```


### Conclusion
- You used iosxr_mon, copy modules and "when" conditionals for health monitoring.
- Ansible playbooks can be leveraged for Device health monitoring to identify issues.
- Operations can proactively identify faults by periodically running the playbook in network.
- Data collected can be used in-line or offline for analysis


#### Example output

```
cisco@ansible-controller:~$ ansible-playbook p32-xr-health-monitoring.yml --syntax-check

playbook: p32-xr-health-monitoring.yml

cisco@ansible-controller:~$ ansible-playbook p32-xr-health-monitoring.yml

PLAY [XR Router Health Monitoring] ******************************************************************************************************************************************************************************************************************************************************************************************

TASK [Router Health Monitoring Commands] ************************************************************************************************************************************************************************************************************************************************************************************
ok: [172.16.101.99]

TASK [save output to a file] ************************************************************************************************************************************************************************************************************************************************************************************************
changed: [172.16.101.99]

TASK [debug] ****************************************************************************************************************************************************************************************************************************************************************************************************************
skipping: [172.16.101.99]

TASK [debug] ****************************************************************************************************************************************************************************************************************************************************************************************************************
ok: [172.16.101.99] => {
    "msg": " 172.16.101.99 show_redundancy indicates card is not present"
}

TASK [debug] ****************************************************************************************************************************************************************************************************************************************************************************************************************
ok: [172.16.101.99] => {
    "msg": "172.16.101.99 CPU Utilization CPU utilization for one minute: 0%; five minutes: 0%; fifteen minutes: 0%\n \nPID    1Min    5Min    15Min Process"
}

TASK [debug] ****************************************************************************************************************************************************************************************************************************************************************************************************************
ok: [172.16.101.99] => {
    "msg": " 172.16.101.99 Memory Available: node:      node0_0_CPU0\n\fPhysical Memory: 3071M total (1381M available)\n Application Memory : 2868M (1381M available)"
}

TASK [debug] ****************************************************************************************************************************************************************************************************************************************************************************************************************
skipping: [172.16.101.99]

TASK [debug] ****************************************************************************************************************************************************************************************************************************************************************************************************************
ok: [172.16.101.99] => {
    "msg": " 172.16.101.99 Route Summary: Route Source                     Routes     Backup     Deleted     Memory(bytes)\nconnected                        1          1          0           320          \nlocal                            2          0          0           320          \ndagr                             0          0          0           0            \nbgp 1                            0          0          0           0            \nTotal                            3          1          0           640"
}

TASK [debug] ****************************************************************************************************************************************************************************************************************************************************************************************************************
skipping: [172.16.101.99]

TASK [debug] ****************************************************************************************************************************************************************************************************************************************************************************************************************
ok: [172.16.101.99] => {
    "msg": " 172.16.101.99 MPLS LDP Summary: "
}

TASK [debug] ****************************************************************************************************************************************************************************************************************************************************************************************************************
skipping: [172.16.101.99]

PLAY RECAP ******************************************************************************************************************************************************************************************************************************************************************************************************************
172.16.101.99              : ok=7    changed=1    unreachable=0    failed=0

cisco@ansible-controller:~$
```

## Generate iBGP config using roles and upload to routers

### Objectives

- Create a playbook to generate iBGP configuration using roles and upload to devices

### Approach

- In this exercise, you will utilize roles function, to generate iBGP configuration for IOS and IOSXR device
- You will create the necessary role folder/sub-folder structure (tasks/vars/templates) manually, and load the necessary files (tasks/mail.yml, vars/main.yml, templates/xxx-BGP.j2)
- Task-1 You will invoke role module for csr-bgp and xr-bgp. Roles automatically load certain files variable files, templates and tasks based on known file structure.
- Task-2&3 You will load the generated config into target device nodes.

#### Lab Exercise

##### Step-1: Create two folders by the name “csr-bgp”  and “xr-bgp” for 2 roles with mkdir command

```
$ mkdir csr-bgp xr-bgp
```

- Create the following sub-folders under two roles directory. Tasks,templates and vars folders, under both csr-bgp and xr-bgp folder.

```
$ mkdir csr-bgp/tasks csr-bgp/vars csr-bgp/templates
$ mkdir xr-bgp/tasks xr-bgp/vars xr-bgp/templates
```

##### Step-2: Review the content of playbook below

  - Task-1 You will invoke role module for csr-bgp and xr-bgp.

  - Task-2 leverage the ios_config, and iosxr_config module to load the configuration onto target nodes.

- Create a playbook p32-roles-bgp.yml, with the following contents

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
##### Step-3: Review the contents of playbook below

  - In this playbook, target configuration is generated and saved as txt file based on source template file in jinja2 format.
  - Necessary variable parameters are invoked from the variable files.
  - Create main.yml file with below contents.
    - Move the main.yml file from home directory to csr-bgp/tasks/main.yml in ansible controller. This optional step is required for ATOM Editor users.
    - For vi users, you can create the main.yml file directly under csr-bgp/tasks folder.

```
---
- name: Generate R1 CSR router iBGP config file
  template: src=CSR-BGP.j2 dest=/home/cisco/{{item.hostname}}-BGP.txt
  with_items: "{{router_list}}"
```
Note: The following step is for ATOM users. You will move the main.yml file from /home/cisco directory to csr-bgp/tasks/main.yml in ansible controller.

```
$ mv main.yml csr-bgp/tasks/main.yml
```
##### Step-4: Create a file main.yml with following contents.  

  - You are creating an variable file with following contents.
  - this “main.yml” has a single list “dictionary” which contains mapped key:value pairs to populate the iBGP configurations for the jinja2 template.
    - Move the main.yml file from home directory to csr-bgp/vars/main.yml on ansible controller. This optional step is required for ATOM Editor users.
    - For vi users, you can create the main.yml file directly under csr-bgp/vars folder.

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
Note: The following step is for ATOM users. You will move the main.yml file from /home/cisco directory to csr-bgp/vars/main.yml in ansible controller.

```
$ mv main.yml csr-bgp/vars/main.yml
```
##### Step-5: Create the template CSR-BGP.j2 with following contents.
- In this step, you are building configuration template in jinja2 templates
- You will use variables in templates, so they can be assigned statically or dynamically to generate configuration of target nodes.
  - Move the CSR-BGP.j2 file from home directory to csr-bgp/templates/CSR-BGP.j2 on ansible controller. This optional step is required for ATOM Editor users.
  - For vi users, you can create the CSR-BGP.j2 file directly under csr-bgp/templates folder.

```
router bgp {{item.LCL_ASN}}
 bgp router-id {{item.RID}}
 bgp log-neighbor-changes
 neighbor {{item.PEER_IP}} remote-as {{item.RMT_ASN}}
 neighbor {{item.PEER_IP}} update-source Loopback0
!
```
Note: The following step is for ATOM users. You will move the CSR-BGP.j2 file from /home/cisco directory to csr-bgp/templates/CSR-BGP.j2 in ansible controller.
```
$ mv CSR-BGP.j2 csr-bgp/templates/CSR-BGP.j2
```

##### Step-6: Repeat step-3 to step-5 for the role type XR-BGP device.
  - In this playbook, target configuration is generated and saved as txt file based on source template file in jinja2 format.
  - Necessary variable parameters are invoked from the variable files.
  - Create main.yml file with below contents.
    - Move the main.yml file from home directory to xr-bgp/tasks/main.yml in ansible controller. This optional step is required for ATOM Editor users.
    - For vi users, you can create the main.yml file directly under xr-bgp/tasks folder.

```
---
- name: Generate R2 XRV router iBGP config file
  template: src=XR-BGP.j2 dest=/home/cisco/{{item.hostname}}-BGP.txt
  with_items: "{{router_list}}"
```
Note: The following step is for ATOM users. You will move the main.yml file from /home/cisco directory to xr-bgp/tasks/main.yml in ansible controller.

```
$ mv main.yml xr-bgp/tasks/main.yml
```
##### Step-7: Create a file main.yml with following contents.  

  - You are creating an variable file with following contents.
  - this “main.yml” has a single list “dictionary” which contains mapped key:value pairs to populate the iBGP configurations for the jinja2 template.
    - Move the main.yml file from home directory to xr-bgp/vars/main.yml on ansible controller. This optional step is required for ATOM Editor users.
    - For vi users, you can create the main.yml file directly under xr-bgp/vars folder.

```
router_list:
  -  hostname: R2-XRv
     profile: IOS-XR
     RID: 192.168.0.2
     PEER_IP: 192.168.0.1
     LCL_ASN: 1
     RMT_ASN: 1
```
Note: The following step is for ATOM users. You will move the main.yml file from /home/cisco directory to xr-bgp/tasks/main.yml in ansible controller.

```
$ mv main.yml xr-bgp/vars/main.yml
```
##### Step-8: Create the template XR-BGP.j2 with following contents.
- In this step, you are building configuration template in jinja2 templates
- You will use variables in templates, so they can be assigned statically or dynamically to generate configuration of target nodes.
  - Move the XR-BGP.j2 file from home directory to xr-bgp/templates/XR-BGP.j2 on ansible controller. This optional step is required for ATOM Editor users.
  - For vi users, you can create the XR-BGP.j2 file directly under xr-bgp/templates folder.

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
Note: The following step is for ATOM users. You will move the XR-BGP.j2 file from /home/cisco directory to xr-bgp/templates/XR-BGP.j2 in ansible controller.
```
$ mv XR-BGP.j2 xr-bgp/templates/XR-BGP.j2
```

##### Step-9: Run the tree command and validate that following files structure is created
```
cisco@ansible-controller:~$ tree csr-bgp
csr-bgp
├── tasks
│   └── main.yml
├── templates
│   └── CSR-BGP.j2
└── vars
    └── main.yml

3 directories, 3 files

cisco@ansible-controller:~$ tree xr-bgp
xr-bgp
├── tasks
│   └── main.yml
├── templates
│   └── XR-BGP.j2
└── vars
    └── main.yml
```

##### Step-10: Execute the playbook - p34-roles-bgp.yml.

- Task-1 Generates R2-XRv config by loading files in xr-bgp folder. Invokes tasks, templates and vars file and save's the target file as R2-XRv-BGP.txt
- Task-2 Generates R1-IOS config by loading files in cxr-bgp folder. Invokes tasks, templates and vars file and save's the target file as R1-CSR1K-BGP.txt
- Task-3 loads the config to R1-CSR device
- Task-4 loads the config to R2-XRv device

```
$ ansible-playbook p34-roles-bgp.yml --syntax-check
$ ansible-playbook p34-roles-bgp.yml
```
##### Step-11: Validate the following configuration file has been created
```
$ cat R1-CSR1K-BGP.txt
$ cat R2-XRv-BGP.txt
```
##### Step-12: You will use the ansible ad-hoc command to validate both IOS and XR router has the loaded configuration.
```
$ ansible IOS -m raw -a "show run | inc bgp|neig"
$ ansible XR -m raw -a " show run router bgp"
```

### Conclusion

- In this exercise, you used roles to develop two different configurations for two different device type
- In the same playbook, the generated config was loaded into the target devices.

#### Example output:

```
cisco@ansible-controller:~$ mkdir csr-bgp xr-bgp
cisco@ansible-controller:~$ mkdir csr-bgp/tasks csr-bgp/vars csr-bgp/templates
cisco@ansible-controller:~$ mkdir xr-bgp/tasks xr-bgp/vars xr-bgp/templates

cisco@ansible-controller:~$ansible-playbook p34-roles-bgp.yml

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
```

Optional Exercise: Use Ansible to create a simple playbook to check if the iBGP configuration has been added and the iBGP session between R1 and R2 is established.

## Bulk config generation

- By Leveraging Ansible Roles and templates, You can build bulk (many) configuration for deployment @ scale.

### Objective

- Create a playbook to generate configuration for multiple devices for  different cisco network operating systems - IOS and XR.

### Approach

-- You will use the open-source ansible-galaxy cli to generate the necessary role folder/file structure
- you will leverage roles function, to generate device configs for IOS and IOSXR devices
- You will use the var files, to provide the necessary parameters for different device configurations.

### Lab Exercise

##### Step-1: Create a new role for config generation leveraging open-source cli "ansible-galaxy"

```
$ ansible-galaxy init config-gen
```
- Review the tree structure that has been created by galaxy. For this lab, you will be using templates/vars and task folder for build config.
```
$ tree config-gen/
```

##### Step-2: Create the playbook p34-config-gen.yml with following contents

  - In this playbook, there is a single task where it calls to execute the role config-gen
```
---
  - name: Playbook to generate configuration based on role "config-gen"
    hosts: localhost

    roles:
       - config-gen
```

##### Step-3: Review the contents of playbook below

- In Task1, you will build configuration based on source "xr-config-tempalte"
- In Task1, the variable under xr_homenames, provides necessary variable parameter for bulk device configuration.
- In Task2, you will build configuration based on source "ios-config-tempalte"
- In Task1, the variable under ios_homenames, provides necessary variable parameter for bulk device configuration.
- Create main.yml file with below contents.
  - Move the main.yml file from home directory to config-gen/tasks/main.yml in ansible controller. This optional step is required for ATOM Editor users.
  - For vi users, you can create the main.yml file directly under config-gen/tasks folder.
---
- name: Generate the configuration for xr-routers
  template:
      src=xr-config-template.j2
      dest=/home/cisco/{{item.hostname}}.txt
  with_items:
      - "{{ xr_hostnames }}"

- name: Generate the configuration for iosxe-router3
  template:
      src=ios-config-template.j2
      dest=/home/cisco/{{item.hostname}}.txt
  with_items:
      - "{{ ios_hostnames }}"
...

Note: The following step is for ATOM users. You will move the main.yml file from /home/cisco directory to config-gen/tasks/main.yml in ansible controller.

```
$ mv main.yml config-gen/tasks/main.yml
```
##### Step-4: Create a file main.yml with following contents.  

  - You are creating an variable file with following contents.
  - this “main.yml” has a 8 list “dictionary” which contains mapped key:value pairs to populate the configurations for the jinja2 template.
    - Vars file, provides the necessary parameters for 3 different XR device types and 3 different IOSXE device types
    - There are 2 other list of ditcionaries that provide the necessary interface type
    - List is ordered and it goes from top to Down
    - Dictionay is defined by key value pair and they can be unordered.

```
---
xr_hostnames:
   - { hostname: xr-router-rtr1, timezone: EST, timezone_dst: EDT, timezone_offset: -5, loopback0_ip: 192.168.0.1, loopback0_mask: 255.255.255.255, mgnt_ip: 172.16.101.99, mgnt_mask: 255.255.255.0, gig0000_ip: 10.0.0.5, gig0000_mask: 255.255.255.0, gig0001_ip: 10.1.0.5 , gig0001_mask: 255.255.255.0,}

   - { hostname: xr-router-rtr2, timezone: CST, timezone_dst: CDT, timezone_offset: -5, loopback0_ip: 192.168.0.3, loopback0_mask: 255.255.255.255, mgnt_ip: 172.16.3.99, mgnt_mask: 255.255.255.0, gig0000_ip: 10.2.0.5, gig0000_mask: 255.255.255.0, gig0001_ip: 10.3.0.5 , gig0001_mask: 255.255.255.0,}

   - { hostname: xr-router-rtr3, timezone: PST, timezone_dst: PDT, timezone_offset: -5, loopback0_ip: 192.168.0.5, loopback0_mask: 255.255.255.255, mgnt_ip: 172.16.5.99, mgnt_mask: 255.255.255.0, gig0000_ip: 10.4.0.5, gig0000_mask: 255.255.255.0, gig0001_ip: 10.5.0.5 , gig0001_mask: 255.255.255.0,}

xr_interfaces:
  - GigabitEthernet0/0/0/0
  - GigabitEthernet0/0/0/1

ios_hostnames:
   - { hostname: ios-router-rtr1, timezone: EST, timezone_dst: EDT, timezone_offset: -5, loopback0_ip: 192.168.0.2, loopback0_mask: 255.255.255.255, mgnt_ip: 172.16.101.98, mgnt_mask: 255.255.255.0, gigaethernet2_ip: 10.0.0.6, gigaethernet2_mask: 255.255.255.0, gigaethernet3_ip: 10.1.0.6 , gigaethernet3_mask: 255.255.255.0, ospf_network1: 192.168.0.2, ospf_network_mask1: 0.0.0.0, ospf_network2: 10.0.0.0, ospf_network_mask2: 0.0.0.255, ospf_network3: 10.1.0.0, ospf_network_mask3: 0.0.0.255, areaid: 0 }

   - { hostname: ios-router-rtr2, timezone: CST, timezone_dst: CDT, timezone_offset: -5, loopback0_ip: 192.168.0.4, loopback0_mask: 255.255.255.255, mgnt_ip: 172.16.4.98, mgnt_mask: 255.255.255.0, gigaethernet2_ip: 10.4.0.6, gigaethernet2_mask: 255.255.255.0, gigaethernet3_ip: 10.5.0.6 , gigaethernet3_mask: 255.255.255.0, ospf_network1: 192.168.0.2, ospf_network_mask1: 0.0.0.0, ospf_network2: 10.4.0.0, ospf_network_mask2: 0.0.0.255, ospf_network3: 10.5.0.0, ospf_network_mask3: 0.0.0.255, areaid: 0 }

   - { hostname: ios-router-rtr3, timezone: CST, timezone_dst: PDT, timezone_offset: -5, loopback0_ip: 192.168.0.6, loopback0_mask: 255.255.255.255, mgnt_ip: 172.16.6.98, mgnt_mask: 255.255.255.0, gigaethernet2_ip: 10.6.0.6, gigaethernet2_mask: 255.255.255.0, gigaethernet3_ip: 10.7.0.6 , gigaethernet3_mask: 255.255.255.0, ospf_network1: 192.168.0.2, ospf_network_mask1: 0.0.0.0, ospf_network2: 10.6.0.0, ospf_network_mask2: 0.0.0.255, ospf_network3: 10.7.0.0, ospf_network_mask3: 0.0.0.255, areaid: 0 }

ios_interfaces:
  - GigabitEthernet0/0/0/0
  - GigabitEthernet0/0/0/1
```
Note: The following step is for ATOM users. You will move the main.yml file from /home/cisco directory to core-gen/vars/main.yml in ansible controller.

```
$ mv main.yml csr-bgp/vars/main.yml
```
##### Step-5: Review the contents of template below
- Create the template "xr-config-template.j2" with following contents.
- Within the template, the variables are used where ever they can be replaced with specific device type configurations
  - Move the "xr-config-template.j2" file from home directory to config-gen/templates/"xr-config-template.j2" in ansible controller. This optional step is required for ATOM Editor users.
  - For vi users, you can create the "xr-config-template.j2" file directly under config-gen/templates folder.

  ```
  hostname {{item.hostname}}
  service timestamps log datetime msec
  service timestamps debug datetime msec
  clock timezone {{item.timezone}} {{item.timezone_offset}}
  clock summer-time {{item.timezone_dst}} recurring
  telnet vrf default ipv4 server max-servers 10
  telnet vrf Mgmt-intf ipv4 server max-servers 10
  domain lookup disable
  vrf Mgmt-intf
   address-family ipv4 unicast
   !
   address-family ipv6 unicast
   !
  !
  domain name virl.info
  ssh server v2
  ssh server vrf Mgmt-intf
  !
  line template vty
  timestamp
  exec-timeout 720 0
  !
  line console
  exec-timeout 0 0
  !
  line default
  exec-timeout 720 0
  !
  vty-pool default 0 50
  control-plane
   management-plane
    inband
     interface all
      allow all
     !
    !
   !
  !
  !
  cdp
  !
  !
  interface Loopback0
    description Loopback
    ipv4 address {{item.loopback0_ip}} {{item.loopback0_mask}}
  !
  interface GigabitEthernet0/0/0/0
    description to R1-CSR1kv
    ipv4 address {{item.gig0000_ip}} {{item.gig0000_mask}}
    cdp
    no shutdown
  !
  interface GigabitEthernet0/0/0/1
    description to R3-NXOS
    ipv4 address {{item.gig0001_ip}} {{item.gig0001_mask}}
    cdp
    no shutdown
  !
  interface mgmteth0/0/CPU0/0
    description OOB Management
    ! Configured on launch
    vrf Mgmt-intf
   ipv4 address 172.16.101.99 255.255.255.0
    cdp
    no shutdown
  !
  !
  router ospf 16509
    log adjacency changes
    router-id {{item.loopback0_ip}}
    address-family ipv4
    area 0
      !
      interface Loopback0
        passive enable
      !
  {% for interface in xr_interfaces %}
  interface {{interface}}
  cost 1
  !
  {% endfor %}
    !
  !
  !
  ```

  - Create the template "ios-config-template.j2" with following contents.
  - Within the template, the variables are used where ever they can be replaced with specific device type configurations
    - Move the "ios-config-template.j2" file from home directory to config-gen/templates/"ios-config-template.j2" in ansible controller. This optional step is required for ATOM Editor users.
    - For vi users, you can create the "ios-config-template.j2" file directly under config-gen/templates folder.

```
  clock timezone {{item.timezone}} {{item.timezone_offset}}
  clock summer-time {{item.timezone_dst}} recurring

  service timestamps debug datetime msec
  service timestamps log datetime msec
  platform qfp utilization monitor load 80
  no platform punt-keepalive disable-kernel-core
  platform console serial
  !
  hostname {{item.hostname}}
  !
  boot-start-marker
  boot-end-marker
  !
  !
  vrf definition Mgmt-intf
   !
   address-family ipv4
   exit-address-family
   !
   address-family ipv6
   exit-address-family
  !
  enable secret 4 tnhtc92DXBhelxjYk8LWJrPV36S2i4ntXrpb4RFmfqY
  enable password cisco
  !
  no aaa new-model
  !
  !
  !
  !
  !
  !
  !
  !

  no ip domain lookup
  ip domain name virl.info
  !
  !
  !
  ipv6 unicast-routing
  !
  !
  !
  !
  !
  !
  !
  subscriber templating
  !
  !
  !
  !
  !
  !
  !
  multilink bundle-name authenticated
  !
  !
  !
  !
  !
  crypto pki trustpoint TP-self-signed-35466579
   enrollment selfsigned
   subject-name cn=IOS-Self-Signed-Certificate-35466579
   revocation-check none
   rsakeypair TP-self-signed-35466579
  !
  !
  crypto pki certificate chain TP-self-signed-35466579
   certificate self-signed 01
    3082032C 30820214 A0030201 02020101 300D0609 2A864886 F70D0101 05050030
    2F312D30 2B060355 04031324 494F532D 53656C66 2D536967 6E65642D 43657274
    69666963 6174652D 33353436 36353739 301E170D 31383033 32383136 33343532
    5A170D32 30303130 31303030 3030305A 302F312D 302B0603 55040313 24494F53
    2D53656C 662D5369 676E6564 2D436572 74696669 63617465 2D333534 36363537
    39308201 22300D06 092A8648 86F70D01 01010500 0382010F 00308201 0A028201
    0100C122 3C95D116 714EE581 53539DCE 33BBE636 20BCAB70 B12ECDE8 832DB71C
    F223B066 E3779F87 0BF81EE6 CE6E60EE F471B22F 5ECE57FD 50C7D706 17F3F62D
    4573882F B9B6351F ECDC6192 167D768A DC8B4613 8A2AEB70 1906E49D 0A2734A8
    64C0C7A3 4B6951D2 573AFF96 5682BE7D 305F4351 A5E6A667 DB787283 724AF55F
    3A049F98 57A1C34F CC9B9C24 3056B3DF 11A04AB4 3F051C0D 14D5AACE B7B0D991
    611FE0D6 6B2CC9D2 3F410224 52701D25 135C7BF2 FEDC0BCD F9BD7C10 4B437143
    E38A10E8 F5423F0E BB71A593 AFDBC814 D6DD4ED6 0709FCC5 33F480F0 6389C2AF
    F0C36163 54164A20 541AAA30 EAFDFD2B 35361640 82331C9B F0D97302 B1429508
    87DB0203 010001A3 53305130 0F060355 1D130101 FF040530 030101FF 301F0603
    551D2304 18301680 14B0C21C 0050185E 5D0751E6 6A90DD48 D9157E6B 0E301D06
    03551D0E 04160414 B0C21C00 50185E5D 0751E66A 90DD48D9 157E6B0E 300D0609
    2A864886 F70D0101 05050003 82010100 4E89908F 13A8518B 33D0DC0E 71548510
    7E3285F7 71E4B8A4 2E25FA83 3FD571F5 17D190EA DFC4F076 AF1C3494 17DC54B4
    93A61630 C2D321BE F3D1B9D1 72AA7BE9 D5755FD5 C2330B82 F9DA1B4B 590BBA8A
    0A36758E 22061021 86D03C8B D5877680 954F22E6 3A4F807E 79CA5DB5 F63ECF74
    CA45C80D A8052A3A 48CD69B5 027D66D5 08020FA6 94FCE404 07D12573 590C0D60
    5999C40B FECA7B2D A11FC2B8 21D7A110 E4814E8E 2ED74D9D B22A66DF B9BF8932
    424A5807 AF9A59B5 FB6A7FCE B73E25B8 F937695D 9E15768D 614AA387 0B26B6FA
    C54DF6E2 34E5E803 1123AB24 9CC8F3CF FDBB6B7E CC3FF86C B83C858A 34646F0B
    0C79ED3D 814ACA2F 3F565B5C BB84FCAA
    	quit
  !
  !
  !
  !
  !
  !
  !
  !
  license udi pid CSR1000V sn 9N7CZX65NJ3
  license accept end user agreement
  license boot level ax
  diagnostic bootup level minimal
  !
  spanning-tree extend system-id
  !
  !
  username cisco privilege 15 secret 5 $1$F6GC$L/.gqoiPm0AcItLajjXXJ/
  !
  redundancy
  !
  !
  cdp run
  !

  !
  interface Loopback0
   description Loopback
   ip address {{item.loopback0_ip}} {{item.loopback0_mask}}
  !
  interface GigabitEthernet1
   description OOB Management
   vrf forwarding Mgmt-intf
   ip address {{item.mgnt_ip}} {{item.mgnt_mask}}
   negotiation auto
   cdp enable
   no mop enabled
   no mop sysid
  !
  interface GigabitEthernet2
   description to R2-XRv
   ip address {{item.gigaethernet2_ip}} {{item.gigaethernet2_mask}}
   ip ospf cost 1
   negotiation auto
   cdp enable
   no mop enabled
   no mop sysid
  !
  !
  router ospf 16509
    network {{item.ospf_network1}} {{item.ospf_network_mask1}} area {{item.areaid}}
    log-adjacency-changes
    passive-interface Loopback0
    network {{item.ospf_network2}} {{item.ospf_network_mask1}} area {{item.areaid}}
    network {{item.ospf_network3}} {{item.ospf_network_mask1}} area {{item.areaid}}
  !
  !


  threat-visibility
  !
  virtual-service csr_mgmt
  !
  ip forward-protocol nd
  ip http server
  ip http authentication local
  ip http secure-server
  !
  ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr
  ip ssh server algorithm authentication password
  ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr
  !
  !
  control-plane
  !
  !
  line con 0
   password cisco
   stopbits 1
  line vty 0 4
   exec-timeout 720 0
   password cisco
   login local
   transport input telnet ssh
  !
  end
  ```
- Move the templates *.j2 from the home directory to core-gen/templates directory

Note: The following step is for ATOM users. You will move the *.j2 file from /home/cisco directory to core-gen/templates/*.j2 in ansible controller.

  ```
$ mv *.j2 config-gen/templates/.
  ```
##### Step-6: Execute the playbook p34-config-gen.yml.

```
$ ansible-playbook p35-config.gen.yml --syntax-check

$ ansible-playbook p35-config.gen.yml

```
##### Step-7: Validate the files are created
```
$ ls -ltr *router*.txt
```

#### Exercise captures:

```
cisco@ansible-controller:~$ ansible-galaxy init config-gen
- config-gen was created successfully
cisco@ansible-controller:~$

cisco@ansible-controller:~$ tree config-gen
config-gen
├── defaults
│   └── main.yml
├── files
├── handlers
│   └── main.yml
├── meta
│   └── main.yml
├── README.md
├── tasks
│   └── main.yml
├── templates
│   ├── ios-config-template.j2
│   └── xr-config-template.j2
├── tests
│   ├── inventory
│   └── test.yml
└── vars
    └── main.yml

cisco@ansible-controller:~$ ansible-playbook p35-config.gen.yml --syntax-check

playbook: p35-config.gen.yml
cisco@ansible-controller:~$


cisco@ansible-controller:~$ ansible-playbook p35-config.gen.yml

PLAY [Playbook to generate configuration based on role "config-gen"] *************************************************************************************************************************************

TASK [config-gen : Generate the configuration for xr-routers] ********************************************************************************************************************************************
changed: [localhost] => (item={u'timezone_dst': u'EDT', u'gig0000_mask': u'255.255.255.0', u'mgnt_ip': u'172.16.101.99', u'timezone_offset': -5, u'hostname': u'xr-router-rtr1', u'loopback0_ip': u'192.168.0.1', u'mgnt_mask': u'255.255.255.0', u'timezone': u'EST', u'gig0001_mask': u'255.255.255.0', u'gig0000_ip': u'10.0.0.5', u'gig0001_ip': u'10.1.0.5', u'loopback0_mask': u'255.255.255.255'})
changed: [localhost] => (item={u'timezone_dst': u'CDT', u'gig0000_mask': u'255.255.255.0', u'mgnt_ip': u'172.16.3.99', u'timezone_offset': -5, u'hostname': u'xr-router-rtr2', u'loopback0_ip': u'192.168.0.3', u'mgnt_mask': u'255.255.255.0', u'timezone': u'CST', u'gig0001_mask': u'255.255.255.0', u'gig0000_ip': u'10.2.0.5', u'gig0001_ip': u'10.3.0.5', u'loopback0_mask': u'255.255.255.255'})
changed: [localhost] => (item={u'timezone_dst': u'PDT', u'gig0000_mask': u'255.255.255.0', u'mgnt_ip': u'172.16.5.99', u'timezone_offset': -5, u'hostname': u'xr-router-rtr3', u'loopback0_ip': u'192.168.0.5', u'mgnt_mask': u'255.255.255.0', u'timezone': u'PST', u'gig0001_mask': u'255.255.255.0', u'gig0000_ip': u'10.4.0.5', u'gig0001_ip': u'10.5.0.5', u'loopback0_mask': u'255.255.255.255'})

TASK [config-gen : Generate the configuration for iosxe-router3] *****************************************************************************************************************************************
changed: [localhost] => (item={u'timezone_dst': u'EDT', u'areaid': 0, u'ospf_network3': u'10.1.0.0', u'mgnt_ip': u'172.16.101.98', u'gigaethernet2_mask': u'255.255.255.0', u'gigaethernet3_mask': u'255.255.255.0', u'ospf_network1': u'192.168.0.2', u'timezone_offset': -5, u'hostname': u'ios-router-rtr1', u'ospf_network_mask1': u'0.0.0.0', u'ospf_network_mask2': u'0.0.0.255', u'loopback0_ip': u'192.168.0.2', u'mgnt_mask': u'255.255.255.0', u'gigaethernet3_ip': u'10.1.0.6', u'timezone': u'EST', u'ospf_network_mask3': u'0.0.0.255', u'gigaethernet2_ip': u'10.0.0.6', u'ospf_network2': u'10.0.0.0', u'loopback0_mask': u'255.255.255.255'})
changed: [localhost] => (item={u'timezone_dst': u'CDT', u'areaid': 0, u'ospf_network3': u'10.5.0.0', u'mgnt_ip': u'172.16.4.98', u'gigaethernet2_mask': u'255.255.255.0', u'gigaethernet3_mask': u'255.255.255.0', u'ospf_network1': u'192.168.0.2', u'timezone_offset': -5, u'hostname': u'ios-router-rtr2', u'ospf_network_mask1': u'0.0.0.0', u'ospf_network_mask2': u'0.0.0.255', u'loopback0_ip': u'192.168.0.4', u'mgnt_mask': u'255.255.255.0', u'gigaethernet3_ip': u'10.5.0.6', u'timezone': u'CST', u'ospf_network_mask3': u'0.0.0.255', u'gigaethernet2_ip': u'10.4.0.6', u'ospf_network2': u'10.4.0.0', u'loopback0_mask': u'255.255.255.255'})
changed: [localhost] => (item={u'timezone_dst': u'PDT', u'areaid': 0, u'ospf_network3': u'10.7.0.0', u'mgnt_ip': u'172.16.6.98', u'gigaethernet2_mask': u'255.255.255.0', u'gigaethernet3_mask': u'255.255.255.0', u'ospf_network1': u'192.168.0.2', u'timezone_offset': -5, u'hostname': u'ios-router-rtr3', u'ospf_network_mask1': u'0.0.0.0', u'ospf_network_mask2': u'0.0.0.255', u'loopback0_ip': u'192.168.0.6', u'mgnt_mask': u'255.255.255.0', u'gigaethernet3_ip': u'10.7.0.6', u'timezone': u'CST', u'ospf_network_mask3': u'0.0.0.255', u'gigaethernet2_ip': u'10.6.0.6', u'ospf_network2': u'10.6.0.0', u'loopback0_mask': u'255.255.255.255'})

PLAY RECAP ***********************************************************************************************************************************************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0

cisco@ansible-controller:~$

cisco@ansible-controller:~$ ls -ltr *router*.txt
-rw-rw-r-- 1 cisco cisco 1382 Jun  7 15:24 xr-router-rtr1.txt
-rw-rw-r-- 1 cisco cisco 1382 Jun  7 15:24 xr-router-rtr2.txt
-rw-rw-r-- 1 cisco cisco 1382 Jun  7 15:24 xr-router-rtr3.txt
-rw-rw-r-- 1 cisco cisco 4252 Jun  7 15:24 ios-router-rtr1.txt
-rw-rw-r-- 1 cisco cisco 4250 Jun  7 15:24 ios-router-rtr2.txt
-rw-rw-r-- 1 cisco cisco 4250 Jun  7 15:24 ios-router-rtr3.txt

```
### Conclusion

- You can utilize the concept of roles - predetermined order of directories and files to automate generating bulk configuration tasks.
