## Config Generation using roles and jinja2 templates

- In this section, you will create a hierarchical template which consists of one common template that can get called by other templates, creating a nested configuration.
- Exercise D:
   - Create a hierarchical template using nested templates.
   - Generate configurations for two different roles

### Step 1: Create a new role called pe-p (??? pe-p or ler-lsr?)

```
cisco@Ansible-Controller:~/project1$ ansible-galaxy init ler-lsr
pe-p was created successfully
```
### Step 2: Create a playbook xr-hier
-  that will use the pe-p role.
- This file is hosted on the parent directory of the pe-p role folder.

```
cisco@Ansible-Controller:~/project1$ vi xr-hier.yml

---
  - name: Create a config for ler-lsr config from template XR
    hosts: localhost

    roles:
       - ler-lsr
...
```

### Step 3: Edit the main.yml file

- under the ler-lsr/tasks folder with information regarding:
  - the source location of the template, the location of the output destination files, and the hostname variables.

```
cisco@Ansible-Controller:~/project1$ vi ler-lsr/tasks/main.yml

---
- name: Generate the configuration for LER/PE Router1
  template:
    src=ler_config_template.j2
    dest=/home/cisco/project1/{{item.hostname}}.txt
  with_items:
    - "{{ ler_routers }}"

- name: Generate the configuration for LSR/P Router2
  template:
    src=lsr_config_template.j2
    dest=/home/cisco/project1/{{item.hostname}}.txt
  with_items:
    - "{{ lsr_routers }}"
...
```

### Step 4: Create the three different templates

-  listed below and save them under the /ler-lsr/templates/ folder.
  - ler_lsr_config_template.j2 - File contains the common configuration used for LSR/LER.
  - ler_config_template.j2 - File contains configuration specific to LER/PE Role.
  - lsr_config_template.j2 - File contains configuration specific to LSR/P Role.

> Note - We use a combination of { extend/block } function to pass arguments and configuration across the templates. The following are the common configuration used across ler-lsr device.

```
cisco@Ansible-Controller:~/project1$ vi ler-lsr/templates/ler_lsr_config_template.j2

hostname {{item.hostname}}
service timestamps log datetime msec
service timestamps debug datetime msec
telnet vrf default ipv4 server max-servers 10
telnet vrf Mgmt-intf ipv4 server max-servers 10
domain name virl.info
domain lookup disable
cdp
vrf Mgmt-intf
 address-family ipv4 unicast
 !
 address-family ipv6 unicast
 !
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
interface Loopback0
 description Loopback
 ipv4 address {{item.loopback0_ip}} {{item.loopback0_mask}}
!
interface Loopback1
 ipv4 address {{item.loopback1_ip}} {{item.loopback1_mask}}

!
interface Loopback2
 ipv4 address {{item.loopback2_ip}} {{item.loopback2_mask}}
!
{% block interface_ip %}
{% endblock%}
!
interface MgmtEth0/0/CPU0/0
 description OOB Management
 cdp
 ! Configured on launch
 vrf Mgmt-intf
 ipv4 address {{item.mgmt_ip}} {{item.mgmt_mask}}
!
route-policy bgp_in
  pass
end-policy
!
route-policy bgp_out
  pass
end-policy
!
{% block isis %}
{% endblock %}
!
{% block bgp %}
{% endblock %}
!
!
mpls oam
!

{% block rsvp %}
{% endblock %}
!
{% block mpls_traffic_eng %}
{% endblock %}
 !
 bandwidth-accounting
  application
   enable
   interval 180
  !
  sampling-interval 60
  adjustment-factor 100
  flooding threshold up 10 down 10
 !
!
!
ssh server v2
ssh server vrf Mgmt-intf
end
```

- The following are the Ler specific configuration:

```
cisco@as-rtp-cisco-st31:~/playbooks/roles/htemp$ vi ler-lsr/templates/ler_config_template.j2

{% extends "ler_lsr_config_template.j2" %}
{% block interface_ip %}
!
interface tunnel-te1
 bandwidth 100
 ipv4 unnumbered Loopback0
 shutdown
 autoroute announce
  exclude-traffic segment-routing
 !
 destination 192.168.0.5
 path-option 1 dynamic
!
interface tunnel-te1000
 bandwidth 100
 ipv4 unnumbered Loopback0
 shutdown
 autoroute announce
  exclude-traffic segment-routing
 !
 destination 192.168.0.6
 path-option 1 dynamic
 !

interface GigabitEthernet0/0/0/0
 description to LER-01
 cdp
 ipv4 address {{item.gig0000_ip}} {{item.gig0000_mask}}
!
interface GigabitEthernet0/0/0/1
 description to LSR-02
 cdp
 ipv4 address {{item.gig0001_ip}} {{item.gig0001_mask}}
!
interface GigabitEthernet0/0/0/2
 description to LSR-03
 cdp
 ipv4 address {{item.gig0002_ip}} {{item.gig0002_mask}}
!
{% endblock %}

{% block isis %}
!
router isis {{item.isisno}}
 net 49.1921.6800.0001.00
 segment-routing global-block 400000 464000
 address-family ipv4 unicast
  metric-style wide
  mpls traffic-eng level-2-only
  mpls traffic-eng router-id Loopback0
  segment-routing mpls
 !
 interface Loopback0
  passive
  circuit-type level-2-only
  address-family ipv4 unicast
  !
 !
 interface Loopback1
  passive
  circuit-type level-2-only
  address-family ipv4 unicast
   prefix-sid index 11
 !
 {% for interface in interface_list_ler %}
 !
 interface {{interface}}
  circuit-type level-2-only
  point-to-point
  address-family ipv4 unicast
   metric 10
  !
  address-family ipv6 unicast
   metric 10
  !
 {% endfor %}
 {% endblock %}

 {% block bgp %}
!
router bgp {{item.bgpas}}
 bgp router-id {{item.loopback0_ip}}
 address-family ipv4 unicast
  network {{item.loopback0_ip}}
 !

 ! iBGP
 ! iBGP peers
 neighbor {{item.peerip}}
  remote-as {{item.peeras}}
  description iBGP peer BB03
  update-source Loopback0
  address-family ipv4 unicast
  !
!
{% endblock %}
!

{% block rsvp %}
!
rsvp
{% for interface in interface_list_ler %}
 interface {{interface}}
  bandwidth percentage 100
 !
{% endfor %}
{% endblock %}
!
{% block mpls_traffic_eng %}
mpls traffic-eng
{% for interface in interface_list_ler %}
!
interface {{interface}}
!
{% endfor %}
{% endblock %}
 !
```

- The following are LSR specific device configuration:

```
cisco@Ansible-Controller:~/project1$ vi ler-lsr/templates/lsr_config_template.j2

{% extends "ler_lsr_config_template.j2" %}
{% block interface_ip %}
!
interface GigabitEthernet0/0/0/0
 description to LER01
 cdp
 ipv4 address {{item.gig0000_ip}} {{item.gig0000_mask}}
!
interface GigabitEthernet0/0/0/1
 description to LSR03
 cdp
 ipv4 address {{item.gig0001_ip}} {{item.gig0001_mask}}
!
{% endblock%}

{% block isis %}
!
router isis 123
 net 49.1921.6800.0006.00
 segment-routing global-block 400000 464000
 address-family ipv4 unicast
  metric-style wide
  mpls traffic-eng level-2-only
  mpls traffic-eng router-id Loopback0
  segment-routing mpls
  !
 interface Loopback0
  passive
  circuit-type level-2-only
  address-family ipv4 unicast
  !
 interface Loopback1
  passive
  circuit-type level-2-only
  address-family ipv4 unicast
   prefix-sid index 66
  !
 !
 {% for interface in interface_list_lsr %}
 !
 interface {{interface}}
  circuit-type level-2-only
  point-to-point
  address-family ipv4 unicast
   metric 10
  !
  address-family ipv6 unicast
   metric 10
  !
 {% endfor %}
  {% endblock %}
!
{% block rsvp %}
rsvp
 {% for interface in interface_list_lsr %}
 interface {{interface}}
  bandwidth percentage 100
 !
{% endfor %}
{% endblock %}
!
{% block mpls_traffic_eng %}
mpls traffic-eng
{% for interface in interface_list_lsr %}
!
interface {{interface}}
!
{% endfor %}
{% endblock %}
!
```

- Verify the templates have been created and are under core-config/templates

```
cisco@Ansible-Controller:~/project1$ ls -ltr ler-lsr/templates/
total 12
-rw-rw-r-- 1 cisco cisco 2161 Jan 21 01:57 ler_config_template.j2
-rw-rw-r-- 1 cisco cisco 1456 Jan 21 02:31 ler_lsr_config_template.j2
-rw-rw-r-- 1 cisco cisco 1323 Jan 21 02:31 lsr_config_template.j2
```

Step 5: Define the variables

- needed to generate the template in the /pe-p/vars/main.yml file.
- Each host will need to contain values for all the variables highlighted in the template file.

```
cisco@Ansible-Controller:~/project1$ vi ler-lsr/vars/main.yml

---
ler_routers:
    - { hostname: ler1_xr_cl2018, loopback0_ip: 192.168.0.1, loopback0_mask: 255.255.255.255, loopback1_ip: 192.168.1.1, loopback1_mask: 255.255.255.255, loopback2_ip: 192.168.2.1, loopback2_mask: 255.255.255.255, mgmt_ip: 172.16.1.1, mgmt_mask: 255.255.255.0, gig0000_ip: 10.1.0.1, gig0000_mask: 255.255.255.0, gig0001_ip: 10.1.1.1, gig0001_mask: 255.255.255.0, gig0002_ip: 10.1.2.1, gig0002_mask: 255.255.255.0, isisno: 12345, bgpas: 65123, peerip: 192.168.0.123, peeras: 65321 }

interface_list_ler:
    - GigabitEthernet0/0/0/0
    - GigabitEthernet0/0/0/1
    - GigabitEthernet0/0/0/2

lsr_routers:
    - { hostname: lsr1_xr_cl2018, loopback0_ip: 192.168.0.2, loopback0_mask: 255.255.255.255, loopback1_ip: 192.168.1.2, loopback1_mask: 255.255.255.255, loopback2_ip: 192.168.2.2, loopback2_mask: 255.255.255.255, mgmt_ip: 172.16.2.1, mgmt_mask: 255.255.255.0, gig0000_ip: 10.1.0.2, gig0000_mask: 255.255.255.0, gig0001_ip: 10.2.1.1, gig0001_mask: 255.255.255.0, gig0002_ip: 10.2.2.1, gig0002_mask: 255.255.255.0, isisno: 12345 }

interface_list_lsr:
    - GigabitEthernet0/0/0/0
    - GigabitEthernet0/0/0/1
...
# vars file for ler-lsr
```

- In the above exercise, you have two different variables defined for two roles LSR and LER but both are for a single router type.
- For expanding into larger scale, you can add more dictionaries under LSR or LER router for each device type.

### Step 6: Execute the Playbook xr-hier.yml

```
cisco@Ansible-Controller:~/project1$ ansible-playbook xr-hier.yml

PLAY [Create a config for router1 from template XR] *****************************************************************************************************************************************************

TASK [Gathering Facts] **********************************************************************************************************************************************************************************
ok: [localhost]

TASK [pe-p : Generate the configuration for LER/PE Router1] *********************************************************************************************************************************************
changed: [localhost] => (item={u'gig0002_mask': u'255.255.255.0', u'peerip': u'192.168.0.123', u'gig0000_mask': u'255.255.255.0', u'isisno': 12345, u'loopback1_ip': u'192.168.1.1', u'mgmt_ip': u'172.16.1.1', u'loopback1_mask': u'255.255.255.255', u'loopback2_mask': u'255.255.255.255', u'peeras': 65321, u'hostname': u'ler1_xr_cl2018', u'gig0002_ip': u'10.1.2.1', u'loopback0_ip': u'192.168.0.1', u'loopback2_ip': u'192.168.2.1', u'loopback0_mask': u'255.255.255.255', u'mgmt_mask': u'255.255.255.0', u'gig0001_mask': u'255.255.255.0', u'gig0000_ip': u'10.1.0.1', u'gig0001_ip': u'10.1.1.1', u'bgpas': 65123})

TASK [pe-p : Generate the configuration for LSR/P Router2] **********************************************************************************************************************************************
changed: [localhost] => (item={u'gig0002_mask': u'255.255.255.0', u'gig0000_mask': u'255.255.255.0', u'isisno': 12345, u'loopback1_ip': u'192.168.1.2', u'mgmt_ip': u'172.16.2.1', u'loopback1_mask': u'255.255.255.255', u'loopback2_mask': u'255.255.255.255', u'hostname': u'lsr1_xr_cl2018', u'gig0002_ip': u'10.2.2.1', u'loopback0_ip': u'192.168.0.2', u'loopback2_ip': u'192.168.2.2', u'mgmt_mask': u'255.255.255.0', u'gig0001_mask': u'255.255.255.0', u'gig0000_ip': u'10.1.0.2', u'gig0001_ip': u'10.2.1.1', u'loopback0_mask': u'255.255.255.255'})

PLAY RECAP **********************************************************************************************************************************************************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

- You will observe that two different configurations for LSR/LER device types have been generated.

### Step 7: Validate the router configurations are generated

- in the correct folder and explore the generated files.

```
cisco@Ansible-Controller:~/project1$ ls -al l?r*.txt
-rw-rw-r-- 1 cisco cisco 3440 Apr 26 02:25 ler1_xr_cl2018.txt
-rw-rw-r-- 1 cisco cisco 2439 Apr 26 02:25 lsr1_xr_cl2018.txt
```

## Key Takeaways

- The objectives of this session are to introduce you to roles.
- Roles allow you to break a complex playbook into smaller files.
- Utilizing roles and Jinja J2 templates allows for a simple and quick way to generate configurations for large scale networks.
- The modularity of the hierarchical template prevents changes across templates for common configurations. As a result, a change in the base config will only need to be done once and it will be propagated to any other template which calls the base template. 
