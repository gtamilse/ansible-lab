
## Proactive heath monitoring

- Proactive health monitoring helps to identify, and resolve issues before they cause problems in network.

### Objective

- Create a playbook to collect critical data, process, and report issues.

### Approach

- In this exercise, you will collect critical data by using iosxr_command modules.
- Leveraging conditions function, process the collected data, and report for any issues.

- Note: The playbook shown here is only applicable for XR devices. It can be modified for any other device by using the appropriate Ansible module.

### Lab Exercise

Step 1: Create a playbook xr_healthvalidation.yml with following contents:

- Tasks under "Health Monitoring Commands" data will do the data capture by leverging the iosxr_command module
- Tasks under copy module, stores the collected data into {{inventory_hostname }}_health_check.txt"
- Tasks under debug module, checks the data captured against abnormalities using conditionals.

```
---
- name: XR Router Health Monitoring
  hosts: XR
  gather_facts: false
  connection: local

  tasks:
    - name: Health Monitoring Commands
      iosxr_command:
        commands:
          - show platform
          - show redundancy
          - show install active sum
          - show context
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
          content="\n\n ===show platform=== \n\n {{ iosxr_mon.stdout[0] }} \n\n ===show redundancy=== \n\n {{ iosxr_mon.stdout[1] }} \n\n ===show install active sum=== \n\n {{ iosxr_mon.stdout[2] }} \n\n ===show context=== \n\n {{ iosxr_mon.stdout[3] }} \n\n ===show proc cpu=== \n\n {{ iosxr_mon.stdout[4] }} \n\n ===show memory summary=== \n\n {{ iosxr_mon.stdout[5] }} \n\n ===show ipv4 vrf all int bri=== \n\n {{ iosxr_mon.stdout[6] }} \n\n ===show route sum=== {{ iosxr_mon.stdout[7] }} \n\n ===show ospf nei=== \n\n {{ iosxr_mon.stdout[8] }} \n\n ===show mpls ldp neighbor=== \n\n {{ iosxr_mon.stdout[9] }} \n\n ===show bgp sum=== {{iosxr_mon.stdout[10] }}"
           dest="./{{ inventory_hostname }}_health_check.txt"

    - debug:
        msg: " {{ inventory_hostname }} show_platform indicates card is down"
      when: iosxr_mon.stdout[0] | join('') | search('Down')

    - debug:
        msg: " {{ inventory_hostname }} show_redundancy indicates card is not present"
      when: iosxr_mon.stdout[1] | join('') | search('NSR not ready since Standby is not Present')

#    - debug:
#        msg: " {{ inventory_hostname }} active packages list: {{iosxr_data[2] }}"
#      when: iosxr_mon.stdout[2] | join('') | search('Active Packages')

    - debug:
        msg: " {{ inventory_hostname }} Process Crashed: {{iosxr_mon.stdout[3] }}"
      when: iosxr_mon.stdout[3] | join('') | search('Crash')

    - debug:
         msg: "{{ inventory_hostname }} CPU Utilization {{ iosxr_mon.stdout[4] }}"

    - debug:
        msg: " {{ inventory_hostname }} Memory Available: {{ iosxr_mon.stdout[5] }}"

    - debug:
        msg: " {{ inventory_hostname }} Interface is Down"
      when: iosxr_mon.stdout[6] | join('') | search('Down')

    - debug:
        msg: " {{ inventory_hostname }} Route Summary: {{iosxr_mon.stdout[7]}}"
```

Step 5: Execute the playbook and validate the exercise

cisco@ansible-controller:~$ ansible-playbook xr_healthvalidation.yml

### Conclusion

- Ansible playbooks can be leveraged for Proactive health monitoring to identify issues
- Operations can proactively identify faults by periodically running the playbook in network
-  Data collected can be used in-line or offline for analysis


#### Example output

```
cisco@ansible-controller:~$ ansible-playbook xr_healthvalidation.yml

PLAY [XR Router Health Monitoring] ***********************************************************************************************************************************************************************

TASK [Collection Monitoring Commands] ********************************************************************************************************************************************************************
ok: [172.16.101.99]

TASK [save output to a file] *****************************************************************************************************************************************************************************
changed: [172.16.101.99]

TASK [debug] *********************************************************************************************************************************************************************************************
skipping: [172.16.101.99]

TASK [debug] *********************************************************************************************************************************************************************************************
ok: [172.16.101.99] => {
    "msg": " 172.16.101.99 show_redundancy indicates card is not present"
}

TASK [debug] *********************************************************************************************************************************************************************************************
skipping: [172.16.101.99]

TASK [debug] *********************************************************************************************************************************************************************************************
ok: [172.16.101.99] => {
    "msg": "172.16.101.99 CPU Utilization CPU utilization for one minute: 1%; five minutes: 1%; fifteen minutes: 1%\n \nPID    1Min    5Min    15Min Process"
}

TASK [debug] *********************************************************************************************************************************************************************************************
ok: [172.16.101.99] => {
    "msg": " 172.16.101.99 Memory Available: node:      node0_0_CPU0\n\fPhysical Memory: 3071M total (1382M available)\n Application Memory : 2868M (1382M available)"
}

TASK [debug] *********************************************************************************************************************************************************************************************
skipping: [172.16.101.99]

TASK [debug] *********************************************************************************************************************************************************************************************
ok: [172.16.101.99] => {
    "msg": " 172.16.101.99 Route Summary: Route Source                     Routes     Backup     Deleted     Memory(bytes)\nconnected                        1          1          0           320          \nlocal                            2          0          0           320          \ndagr                             0          0          0           0            \nbgp 1                            0          0          0           0            \nTotal                            3          1          0           640"
}

PLAY RECAP ***********************************************************************************************************************************************************************************************
172.16.101.99              : ok=6    changed=1    unreachable=0    failed=0

cisco@ansible-controller:~$
```

## Generate iBGP config using roles and upload to routers

- For operators, network configuration change and rollout occurs periodically.


### objectives

- Understand the functioning of roles and its file structures
- Create a playbook to generate and rollout BGP configuration to the devices.

### Approach

- Generate BGP configurations for different Network Operating Systems (IOS and XR) using Ansible roles and templates
- Using Ansible modules upload the configuration to the devices
- Note: Playbook will be run on “localhost” to generate the required configurations, and then upload the config to the routers.

#### Lab Exercise

Step 1.Create two sub-folders by the name “csr-bgp”  and “xr-bgp” using mkdir command

```
cisco@ansible-controller:~$
cisco@ansible-controller:~$ mkdir csr-bgp xr-bgp
cisco@ansible-controller:~$
```

Step 2.Create tasks,templates and vars folders, under both csr-bgp and xr-bgp folder.

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
Step 6: Create the template CSR-BGP.j2 with following contents.

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
cisco@ansible-controller:~$ mv CSR-BGP.j2 csr-bgp/templates/.
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

Step 8: Create an template file - XR-BGP.j2 with following contents and copy to xr-bgp/templates folders

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
cisco@ansible-controller:~$ mv XR-BGP.j2 xr-bgp/templates/.
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

- Roles expect certain folders like tasks/vars and templates.
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

Optional Exercise: Use Ansible ad-hoc command or create a simple playbook to check if the iBGP configuration has been added and the iBGP session between R1 and R2 is established.

## Bulk config generation

- By Leveraging Ansible Roles and templates, Users can build bulk (many) configuration for deployment @ scale.

### Objective

- Build configuration for 2 different cisco network operating systems - IOS and XR.

### Approach

- Initialize the roles directory and file structure by using ansible-galaxy cli
- Build the playbook, template and variables for bulk config generation

### Lab Exercise

Step 1: Create a new role for config generation leveraging utility "ansible-galaxy"

- Note: Ansible-Galaxy is open source that allows building Roles file structure

```
cisco@ansible-controller:~$ ansible-galaxy init config-gen
- config-gen was created successfully
```
Step 2: Review the tree structure that has been created by galaxy. For this lab, we will be using templates/vars and task folder for buld config generation.
```
cisco@ansible-controller:~$ tree config-gen/
config-gen/
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
├── tests
│   ├── inventory
│   └── test.yml
└── vars
    └── main.yml
		8 directories, 8 files
		cisco@ansible-controller:~$
```
step 3: Create a playbook main.yml with the following contents.

Note: If done through vi editor, the file main.yml is to be created under config-gen/tasks folder. If done through ATOM editor, it is a 2 step process - copy to home directory and them move to tasks folder

```
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
     dest=/home/cisco/project1/{{item.hostname}}.txt
  with_items:
     - "{{ ios_hostnames }}"
...
# tasks file for config-gen

```
- copy from home directory and move under config-gen/tasks folder

```
cisco@ansible-controller:~$
cisco@ansible-controller:~$ mv main.yml config-gen/tasks/main.yml
cisco@ansible-controller:~$
```
Step 4: Create the platform specific configuration and save them under the config-gen/templates folder.

- 4a. Create the following file "xr-config-template.j2" template
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
- 4b. Create the following "ios-config-template.j2" file with following content

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
 ip address {{item.Mgmt_ip}} {{item.Mgmt_mask}}
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
Step 4c: Move the templates *.j2 from the home directory to core-gen/templates directory
```
cisco@ansible-controller:~$ mv *.j2 config-gen/templates/
```

step 5: Define the variables needed to generate the template in the main.yml file. Each host will need to contain values for all the variables highlighted in the template file.

```
---
xr_hostnames:
   - { hostname: xr-router-rtr1, timezone: EST, timezone_dst: EDT, timezone_offset: -5, loopback0_ip: 192.168.0.1, loopback0_mask: 255.255.255.255, mgnt_ip: 172.16.101.99, mgnt_mask: 255.255.255.0, gig0000_ip: 10.0.0.5, gig0000_mask: 255.255.255.0, gig0001_ip: 10.1.0.5 , gig0001_mask: 255.255.255.0,}

xr_interfaces:
  - GigabitEthernet0/0/0/0
  - GigabitEthernet0/0/0/1


ios_hostnames:
   - { hostname: ios-router-rtr1, timezone: EST, timezone_dst: EDT, timezone_offset: -5, loopback0_ip: 192.168.0.2, loopback0_mask: 255.255.255.255, mgnt_ip: 172.16.101.98, mgnt_mask: 255.255.255.0, gigaethernet2_ip: 10.0.0.6, gigaethernet2_mask: 255.255.255.0, gigaethernet3_ip: 10.1.0.6 , gigaethernet3_mask: 255.255.255.0, ospf_network1: 192.168.0.2, ospf_network_mask1: 0.0.0.0, ospf_network2: 10.0.0.0, ospf_network_mask2: 0.0.0.255, ospf_network3: 10.1.0.0, ospf_network_mask3: 0.0.0.255, areaid: 0 }

ios_interfaces:
  - GigabitEthernet0/0/0/0
  - GigabitEthernet0/0/0/1

...
# vars file for config-gen
```

Step 6: Move the file main.yml from the home directory to core-gen/vars directory

```
cisco@ansible-controller:~$ mv main.yml config-gen/tasks/main.yml
```

Step 7: Create a playbook config-gen.yml to invoke the role of Bulk config generation

```
---
  - name: Playbook to generate configuration based on role "config-gen"
    hosts: localhost

    roles:
       - config-gen
```       

Step 8: Execute the playbook config-gen.yml. You will see the config files are generated in target location.

```
cisco@ansible-controller:~$ ansible-playbook config-gen.yml

```
Step 9: Validate the files are created
```
cisco@ansible-controller:~$ ls -ltr *BGP.txt
-rw-r--r-- 1 cisco cisco 168 Jun  5 02:40 R2-XRv-BGP.txt
-rw-r--r-- 1 cisco cisco 148 Jun  5 02:41 R1-CSR1K-BGP.txt
cisco@ansible-controller:~$
```

#### Exercise captures:

```

PLAY [Playbook to generate configuration based on role "config-gen"] **************************************************************************

TASK [config-gen : Generate the configuration for xr-routers] *********************************************************************************
changed: [localhost] => (item={u'timezone_dst': u'EDT', u'gig0000_mask': u'255.255.255.0', u'mgmt_ip': u'172.16.101.99', u'timezone_offset': -5, u'hostname': u'xr-router-rtr1', u'loopback0_ip': u'192.168.0.1', u'mgmt_mask': u'255.255.255.0', u'timezone': u'EST', u'gig0000_ip': u'10.0.0.5', u'gig0001_ip': u'10.1.0.5', u'gig0001_mask': u'255.255.255.0', u'loopback0_mask': u'255.255.255.255'})

TASK [config-gen : Generate the configuration for iosxe-router3] ******************************************************************************
changed: [localhost] => (item={u'timezone_dst': u'EDT', u'areaid': 0, u'ospf_network3': u'10.1.0.0', u'mgmt_ip': u'172.16.101.98', u'gigaethernet2_mask': u'255.255.255.0', u'gigaethernet3_mask': u'255.255.255.0', u'ospf_network1': u'192.168.0.2', u'timezone_offset': -5, u'hostname': u'ios-router-rtr1', u'ospf_network_mask1': u'0.0.0.0', u'ospf_network_mask2': u'0.0.0.255', u'loopback0_ip': u'192.168.0.2', u'gigaethernet3_ip': u'10.1.0.6', u'mgmt_mask': u'255.255.255.0', u'timezone': u'EST', u'ospf_network_mask3': u'0.0.0.255', u'gigaethernet2_ip': u'10.0.0.6', u'ospf_network2': u'10.0.0.0', u'loopback0_mask': u'255.255.255.255'})

PLAY RECAP ************************************************************************************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0

```
### Conclusion

- You can utilize the concept of roles - predetermined order of directories and files to automate generating bulk tasks.

Optional Exercise:

1. Generate config for more than one roles for XR and IOS device.
2. Generate a playbook to push a config to the device node.
