
Hands on Lab Exercise - Roles and Templates
===========================================

Initial Tasks
-------------


1.	Loginto your POD’s Ansible controller after connecting to DMZ VPN.

2.	Create “roles-templates” directory under “/home/cisco/”

3.	Change directory to “/home/cisco/roles-templates/”

4.	Create a sub-directory with name “cfg” using the command “mkdir cfg” under “/home/cisco/roles-templates”

5.	Ensure sub-folder “cfg” is created under  “/home/cisco/roles-templates/”

6.	Change directory to “/home/cisco/roles-templates/”

7.  Create a sub-directory with name “roles” under directory “/home/cisco/roles-templates” using the command “mkdir roles”

8.	Ensure sub-folder “roles” is created under  “/home/cisco/roles-templates/”


Lab Exercise #1: Roles & Templates
==================================

**Aim:** Teach how various components of Ansible Roles & Jinja2 Templates work together with a simple playbook that uses Ansible roles and templates

**Outcome** of the playbook is to generate the below global cisco configuration for IOS running router using roles and jinja2 template.

```

username alpha secret beta
ntp server 9.9.9.9
logging host 9.9.9.10

```

Playbook will be run on “localhost” and will not interact with router as this playbook is to generate configuration offline.

Lab exercise #3 will demonstrate how the generated configuration can be uploaded to router using Ansible play/task.

Name of the playbook: **roles-basic-v1.yml**

Location of this playbook in the controller: **/home/cisco/roles-templates**


1.On the ansible-controller, change directory to “/home/cisco/roles-templates/roles” and create a folder by the name “basic-v1” using mkdir command

Ensure “basic-v1” directory is created under “/home/cisco/roles-templates/roles” and change directory to “basic-v1” directory.

```
cisco@ansible-controller:~/roles-templates/roles$ pwd
/home/cisco/roles-templates/roles
cisco@ansible-controller:~/roles-templates/roles$ mkdir basic-v1
cisco@ansible-controller:~/roles-templates/roles$

```

2.”pwd” command should show that you are in the below directory.

```
cisco@ansible-controller:~/roles-templates/roles/basic-v1$ pwd
/home/cisco/roles-templates/roles/basic-v1
cisco@ansible-controller:~/roles-templates/roles/basic-v1$

```

3.As part of roles and templates some directory structure + files are to be created as was covered in the lecture.

Copy/paste the below command to create them (after making sure you are in directory “/home/cisco/roles-templates/roles/basic-v1”)

```

mkdir -p tasks templates vars; touch tasks/main.yml vars/main.yml templates/BASIC-v1.j2

```

4.After this run tree command to ensure you see the below directory structure.

```

cisco@ansible-controller:~/roles-templates/roles/basic-v1$ pwd
/home/cisco/roles-templates/roles/basic-v1
cisco@ansible-controller:~/roles-templates/roles/basic-v1$ tree
.
├── tasks
│   └── main.yml
├── templates
│   └── BASIC-v1.j2
└── vars
    └── main.yml

3 directories, 3 files

```

5.Go to “/home/cisco/roles-templates” and create a playbook using a filename“roles-basic-v1.yml”.

Use vi or your favorite editor to create the file. Please use the content given in the below box as a content for the playbook.


Content for “roles-basic-v1.yml” file.

```
---
- name: Demonstrate Basic Use case of Roles, Jinja2 Templating for configuration generation
  hosts: localhost

  roles:
   - basic-v1

```


This playbook is run “locally” on the host and it just uses “roles” options and a “list” data structure with one element with the name “basic-v1”.

The list element “basic-v1” referenced here maps to the directory that was created in step #1 of this exercise.

6.Go to directory “/home/cisco/roles-templates/roles/basic-v1” and add contents to the three files as given below:

File #1: Content to be added under “tasks” sub-folder

Ensure you are in “/home/cisco/roles-templates/roles/basic-v1” directory

Change directory to “tasks” sub-folder

This folder will have a file with name “main.yml”.

```

cisco@ansible-controller:~/roles-templates/roles/basic-v1/tasks$ pwd
/home/cisco/roles-templates/roles/basic-v1/tasks
cisco@ansible-controller:~/roles-templates/roles/basic-v1/tasks$ ls
main.yml

```

Copy the content given in the below box as content for file “main.yml” and write/save.
cisco@ansible-controller:~/roles-templates/roles/basic-v1/tasks$ vi main.yml

Ensure the saved file has the below content by checking with more/cat command.

Content for file “main.yml” in the sub-folder “tasks".

```
---
- name: Use a Jinja2 template with paramaters to generate configurations using values provided by a vars file
  template: src=BASIC-v1.j2 dest=/home/cisco/roles-templates/cfg/BASIC-CFG-v1.txt

```

“main.yml” file in the tasks sub-folder identifies the Jinja2 template that contains “configuration template” for this play book specified using “template” src option.

Output of the task will be written to the location given in the “dest” parameter.

File #2: Content to be added under “templates” sub-folder

Ensure you are in “/home/cisco/roles-templates/roles/basic-v1” directory

Change directory to “templates” sub-folder

This folder will have a file with name “BASIC-v1.j2”.


```

cisco@ansible-controller:~/roles-templates/roles/basic-v1/templates$ pwd
/home/cisco/roles-templates/roles/basic-v1/templates
cisco@ansible-controller:~/roles-templates/roles/basic-v1/templates$ ls
BASIC-v1.j2

Copy the content given in the below box as content for file “BASIC-v1.j2” and write/save.
cisco@ansible-controller:~/roles-templates/roles/basic-v1/templates$ vi BASIC-v1.j2

```

Ensure the saved file has the below content by checking with more/cat command

Content for file “BASIC-v1.j2” in the sub-folder templates

```
username {{ user_name }} secret {{ password }}
ntp server {{ ntp_server }}
logging host {{ syslog_server }}

```

Template file shows the parametrized configuration file. Parameters are shown inside double curly braces.

File #3: Content to be added under “vars” sub-folder

Ensure you are in “/home/cisco/roles-templates/roles/basic-v1” directory

Change directory to “vars” sub-folder

This folder will have a file with name “main.yml”.

```

cisco@ansible-controller:~/roles-templates/roles/basic-v1/vars$ pwd
/home/cisco/roles-templates/roles/basic-v1/vars
cisco@ansible-controller:~/roles-templates/roles/basic-v1/vars$ ls
main.yml

```

Copy the content given in the below box as content for file “main.yml” and write/save.
cisco@ansible-controller:~/roles-templates/roles/basic-v1/vars$ vi main.yml

Ensure the saved file has the below content by checking with more/cat command

Content for file “main.yml in the sub-folder "vars".

```
---
user_name: alpha
password: beta
ntp_server: 9.9.9.9
syslog_server: 9.9.9.10

```

Vars file contains all values for parameters given in the Jinja2 template

7.Check the content of the below folder and make sure there are no file(s) in this folder. After step #8 is executed, a file with the name “BASIC-CFG-v1.txt” will be created in this directory

ls -ltr ~/roles-templates/cfg

8.Run the playbook using the below command (running in verbose mode with -vvv is optional) from directory “/home/cisco/roles-templates”

```
cisco@ansible-controller:~/roles-templates$ ansible-playbook roles-basic-v1.yml --syntax-check
cisco@ansible-controller:~/roles-templates$ ansible-playbook roles-basic-v1.yml -v

```

9.Confirm that playbook runs with no errors and check a file with name “BASIC-CFG-v1.txt” has been created in the directory ~/roles-templates/cfg.

10.Confirm that the generated configuration file has correctly substituted the values for the configuration template using the content of "vars" main.yml file.

```
cisco@ansible-controller:~/roles-templates$ ls -ltr ~/roles-templates/cfg
total 4
-rw-rw-r-- 1 cisco cisco 69 Apr 27 02:19 BASIC-CFG-v1.txt

cisco@ansible-controller:~/roles-templates$ more ~/roles-templates/cfg/BASIC-CFG-v1.txt
username alpha secret beta
ntp server 9.9.9.9
logging host 9.9.9.10

```

*******This concludes lab exercise #1*********



Lab Exercise #2: Roles & Templates – With Loop
==============================================

**Goal of this exercise:** To demonstrate “loop” functionality in Ansible Roles & Jinja2 Templates by creating multiple interfaces.

**Outcome** of the playbook is to generate three loopback interfaces (as shown below) using “for” loop function in Jinja2 template and leverage “vars” to substitute parameters in the configuration template.

```

interface loopback 11
 description Loopback 11 interface
 ip address 11.11.11.11 255.255.255.255
!
interface loopback 12
 description Loopback 12 interface
 ip address 12.12.12.12 255.255.255.255
!
interface loopback 13
 description Loopback 13 interface
 ip address 13.13.13.13 255.255.255.255
!

```

Playbook will be run on “localhost” and will not interact with router as this playbook is to generate configuration offline.

Lab exercise #3 will demonstrate how the generated configuration can be uploaded to router using Ansible play/task.

Name of the playbook: add-lb-intfs.yml

Location of this playbook in the controller: /home/cisco/roles-templates

1.Go to/change directory to “/home/cisco/roles-templates/roles” and create a folder by the name “loop” using mkdir command

Ensure “loop” directory is created under “/home/cisco/roles-templates/roles” and change directory to “loop” directory.

```

cisco@ansible-controller:~/roles-templates$ cd /home/cisco/roles-templates/roles/
cisco@ansible-controller:~/roles-templates/roles$ mkdir loop
cisco@ansible-controller:~/roles-templates/roles$ ls
basic-v1  loop
cisco@ansible-controller:~/roles-templates/roles$ cd loop/

2.”pwd” command should show that you are in the below directory.

cisco@ansible-controller:~/roles-templates/roles/loop$ pwd
/home/cisco/roles-templates/roles/loop

```

3.As part of roles and templates some directory structure + files are to be created as was covered in the lecture.

Copy/paste the below command to create them (after making sure you are in directory “/home/cisco/roles-templates/roles/loop”)

```

mkdir -p tasks templates vars; touch tasks/main.yml vars/main.yml templates/LB-IF.j2

```

4.After this run tree command to ensure you see the below directory structure.

```

cisco@ansible-controller:~/roles-templates/roles/loop$ pwd
/home/cisco/roles-templates/roles/loop
cisco@ansible-controller:~/roles-templates/roles/loop$ tree
.
├── tasks
│   └── main.yml
├── templates
│   └── LB-IF.j2
└── vars
    └── main.yml

3 directories, 3 files

```

5.Go to “/home/cisco/roles-templates” and create a playbook with  filename“add-lb-intfs.yml”.

Use vi or your favorite editor to create the file. Please use the content given in the below box as a content for the playbook.

```

cisco@ansible-controller:~/roles-templates$ pwd
/home/cisco/roles-templates
cisco@ansible-controller:~/roles-templates$ vi add-lb-intfs.yml

```

Content for “add-lb-intfs.yml” file.

```

---
- name: Generate Multiple LB interface configurations
  hosts: localhost

  roles:
   - loop

```

 This playbook is run “locally” on the host and it just uses “roles” options and a “list” data structure with one element with the name “loop” is used.

The list element “loop” referenced here maps to the directory that was created in step #1 of this exercise.

6.Go to directory “/home/cisco/roles-templates/roles/loop” and add contents to the three files as given below:

File #1: Content to be added under “tasks” sub-folder

Ensure you are in “/home/cisco/roles-templates/roles/loop” directory

Change directory to “tasks” sub-folder

This folder will have a file with name “main.yml”.

```

cisco@ansible-controller:~/roles-templates/roles/loop/tasks$ pwd
/home/cisco/roles-templates/roles/loop/tasks
cisco@ansible-controller:~/roles-templates/roles/loop/tasks$ ls
main.yml

```

Copy the content given in the below box as content for file “main.yml” and write/save.
cisco@ansible-controller:~/roles-templates/roles/loop/tasks$ vi main.yml

Ensure the saved file has the below content by checking with more/cat command

Content for file “main.yml” in the sub-folder “tasks".

```
---
- name: Create Multiple Loopback intf configurations using Parametrizied Template and values provided by a vars file
  template: src=LB-IF.j2 dest=/home/cisco/roles-templates/cfg/LB-IF-CFG.txt
  with_items: "{{interface_list}}"
```

The “main.yml” file in the tasks sub-folder identifies the Jinja2 template that contains “configuration template” for this play book specified using “template” src option.
Output of the task will be written to the location given in the “dest” parameter.

File #2: Content to be added under “templates” sub-folder

Ensure you are in “/home/cisco/roles-templates/roles/loop” directory

Change directory to “templates” sub-folder

This folder will have a file with name “LB-IF.j2”.

```
cisco@ansible-controller:~/roles-templates/roles/loop/templates$ pwd
/home/cisco/roles-templates/roles/loop/templates
cisco@ansible-controller:~/roles-templates/roles/loop/templates$ ls
LB-IF.j2

```

Copy the content given in the below box as content for file “LB-IF.j2” and write/save.
cisco@ansible-controller:~/roles-templates/roles/loop/templates$ vi LB-IF.j2

Ensure the saved file has the below content by checking with more/cat command

Content for file “LB-IF.j2” in the sub-folder templates

```
{% for lb_list in interface_list %}
interface loopback {{lb_list.LB_IF_ID}}
 description {{lb_list.DESC}}
 ip address {{lb_list.IP_ADD}} {{lb_list.MASK}}
!
{% endfor %}

```

Template file shows the  parametrized configuration file. Parameters are shown inside double curly braces

File #3: Content to be added under “vars” sub-folder

Ensure you are in “/home/cisco/roles-templates/roles/loop” directory

Change directory to “vars” sub-folder

This folder will have a file with name “main.yml”.

```

cisco@ansible-controller:~/roles-templates/roles/loop/vars$ pwd
/home/cisco/roles-templates/roles/loop/vars
cisco@ansible-controller:~/roles-templates/roles/loop/vars$ ls
main.yml

```

Copy the content given in the below box as content for file “main.yml” and write/save.
cisco@ansible-controller:~/roles-templates/roles/loop/vars$ vi main.yml

Ensure the saved file has the below content by checking with more/cat command

Content for file “main.yml in the sub-folder "vars".

```
---
# vars file for Loopback interfaces
interface_list:
  - {LB_IF_ID: 11, IP_ADD: 11.11.11.11, MASK: 255.255.255.255, DESC: Loopback 11 interface}
  - {LB_IF_ID: 12, IP_ADD: 12.12.12.12, MASK: 255.255.255.255, DESC: Loopback 12 interface}
  - {LB_IF_ID: 13, IP_ADD: 13.13.13.13, MASK: 255.255.255.255, DESC: Loopback 13 interface}

```

Vars file contains all values for parameters given in the Jinja2 template. Note this “main.yml” has a list of three dictionaries with each dictionary containing mapped key:value pairs to populate the three loopback interface configurations.

7.Check the content of the below folder and make sure there are no file(s) in this folder. After step #8 is executed, a file with the name “LB-IF-CFG.txt” will be created in this directory

```
cisco@ansible-controller:~/roles-templates/roles/loop/vars$ ls -ltr ~/roles-templates/cfg
total 4
-rw-rw-r-- 1 cisco cisco 69 Apr 27 02:19 BASIC-CFG-v1.txt

```

8.Run the playbook using the below command (running in verbose mode with -vvv is optional) from directory “/home/cisco/roles-emplates”

```
cisco@ansible-controller:~/roles-templates$ pwd
/home/cisco/roles-templates

cisco@ansible-controller:~/roles-templates$ ls
add-lb-intfs.yml  cfg  roles  roles-basic-v1.yml

cisco@ansible-controller:~/roles-templates$ ansible-playbook add-lb-intfs.yml --syntax-check

cisco@ansible-controller:~/roles-templates$ ansible-playbook add-lb-intfs.yml -v

```
9.Confirm that playbook runs with no errors and check to a file with name “LB-IF-CFG.txt” has been created in the directory ~/roles-templates/cfg

10.Confirm that the generated configuration file has correctly substituted the values for the configuration template using the content of "vars" main.yml file.

```
cisco@ansible-controller:~/roles-templates$ ls -ltr ~/roles-templates/cfg
total 8
-rw-rw-r-- 1 cisco cisco  69 Apr 27 02:19 BASIC-CFG-v1.txt
-rw-rw-r-- 1 cisco cisco 298 Apr 27 02:48 LB-IF-CFG.txt

cisco@ansible-controller:~/roles-templates$ more ~/roles-templates/cfg/LB-IF-CFG.txt
interface loopback 11
 description Loopback 11 interface
 ip address 11.11.11.11 255.255.255.255
!
interface loopback 12
 description Loopback 12 interface
 ip address 12.12.12.12 255.255.255.255
!
interface loopback 13
 description Loopback 13 interface
 ip address 13.13.13.13 255.255.255.255
!
```

*******This concludes lab exercise #2*********


Lab Exercise #3: Roles & Templates – Generate iBGP config using roles and upload to routers
===========================================================================================

**Goal of this Lab**: To show how configurations can be generated for routers with different Network Operating Systems (NOS like IOS and XR) using Ansible roles and templates and also show how Ansible modules can be used to upload the configuration to the routers.

**Pre-Req:** This lab exercise presumes that OSPF is configured between R1 and R2 in the individual’s POD.

**Outcome** of the playbook is to generate iBGP configuration for IOS running CSR1Kv router (R1) and for XRv running (R2) router and upload  the generated configurations from Ansible controller to the routers.

Playbook will be run on “localhost” to generate the needed configurations and then will use a play to upload the config to the routers.

Name of the playbook: **roles-bgp.yml**

Location of this playbook in the controller: **/home/cisco/roles-templates**

1.Go to/change directory to “/home/cisco/roles-templates/roles” and create two sub-folders by the name “csr-bgp”  and “xr-bgp” using mkdir command

```

cisco@ansible-controller:~/roles-templates/roles$ pwd
/home/cisco/roles-templates/roles

cisco@ansible-controller:~/roles-templates/roles$ mkdir csr-bgp
cisco@ansible-controller:~/roles-templates/roles$ mkdir xr-bgp

```

2.Change directory to “/home/cisco/roles-templates/roles/csr-bgp” directory.

```

cisco@ansible-controller:~/roles-templates/roles$ cd csr-bgp/
cisco@ansible-controller:~/roles-templates/roles/csr-bgp$

```

3.Use the “pwd” command should show that you are in the below directory.

```

cisco@ansible-controller:~/roles-templates/roles/csr-bgp$ pwd
/home/cisco/roles-templates/roles/csr-bgp

```

4.As part of roles and templates some directory structure + files are to be created as was covered in the lecture part of this training

Copy/paste the below command to create them (after making sure you are in directory “/home/cisco/roles-templates/roles/csr-bgp”)

```

mkdir -p tasks templates vars; touch tasks/main.yml vars/main.yml templates/R1-CSR1K-BGP.j2

```

Check and confirm three directories names tasks, vars and templates are created under ""/home/cisco/roles-templates/roles/csr-bgp". Alternatively "tree" command can be executed from "csr-bgp" sub-directory to confirm both the needed directory structure & files are created (tasks and vars sub-folder should have "main.yml and templates sub-directory should have a file with name R1-CSR1L-BGP.j2")

```

cisco@ansible-controller:~/roles-templates/roles/csr-bgp$ pwd
/home/cisco/roles-templates/roles/csr-bgp

cisco@ansible-controller:~/roles-templates/roles/csr-bgp$ ls
tasks  templates  vars

```

5.Change directory to “/home/cisco/roles-templates/roles xr-bgp” directory

```

cisco@ansible-controller:~/roles-templates/roles/csr-bgp$ cd /home/cisco/roles-templates/roles/xr-bgp/

```

6.Use the “pwd” command should show that you are in the "xr-bgp" sub-directory under "home/cisco/roles-templates/roles/"

```

cisco@ansible-controller:~/roles-templates/roles/xr-bgp$ pwd
/home/cisco/roles-templates/roles/xr-bgp

```

7.As part of roles and templates some directory structure + files are to be created as was covered in the lecture part of this training

Copy/paste the below command to create them (after making sure you are in directory “/home/cisco/roles-templates/roles/xr-bgp”)

```

mkdir -p tasks templates vars; touch tasks/main.yml vars/main.yml templates/R2-XRv-BGP.j2

cisco@ansible-controller:~/roles-templates/roles/xr-bgp$ ls
tasks  templates  vars

```

8.After this run tree command to ensure you see the below directory structure.


Go to “/home/cisco/roles-templates/roles/csr-bgp” directory and execute tree command to check if the below directory structure exists.

Go to “/home/cisco/roles-templates/roles/xr-bgp” and execute tree command to check if the below directory structure exists.

```
cisco@ansible-controller:~/roles-templates/roles/xr-bgp$ pwd
/home/cisco/roles-templates/roles/xr-bgp
cisco@ansible-controller:~/roles-templates/roles/xr-bgp$ tree
.
├── tasks
│   └── main.yml
├── templates
│   └── R2-XRv-BGP.j2
└── vars
    └── main.yml

3 directories, 3 files

```

9.Go to “/home/cisco/roles-templates” and create a playbook using a filename “roles-bgp.yml”.

Use vi or your favorite editor to create the file. Please use the content given in the below box as a content for the playbook.

Content for “roles-bgp.yml” file.

```

---
- name: Generate router bgp configuration files using Roles and Jinja2 Templates
  hosts: localhost

  roles:
   - xr-bgp
   - csr-bgp

- name: Task to upload config to R1-CSR
  hosts: IOS
  gather_facts: false
  connection: local
  tasks:
  - name: "Load iBGP configs for CSR1kv router using SRC option using IOS_CONFIG Module"
    ios_config:
      src: "/home/cisco/roles-templates/cfg/R1-CSR1K-BGP.txt"

- name: Task to upload config to R2-XRV
  gather_facts: false
  connection: local
  hosts: XR


  tasks:
  - name: "Load iBGP configs for xr router using SRC option of IOSXR_CONFIG Module "
    iosxr_config:
      src: "/home/cisco/roles-templates/cfg/R2-XRv-BGP.txt"

```

As can be seen above, this playbook has multiple plays. The first play will run “locally” on the host to generate configuration using “roles” options for both CSR1k/IOS and XRv routers.

 Next two plays will be used to upload the IOS BGP configuration to the R1/CSR1K router and XR BGP configuration to the R2/XRv router.

10.Go to directory “/home/cisco/roles-templates/roles/csr-bgp” and add contents to the three files as given below:

File #1: Content to be added under “tasks” sub-folder

Ensure you are in “/home/cisco/roles-templates/roles/csr-bgp” directory

Change directory to “tasks” sub-folder

This folder will have a file with name “main.yml”.

```

cisco@ansible-controller:~/roles-templates/roles/csr-bgp/tasks$ pwd
/home/cisco/roles-templates/roles/csr-bgp/tasks
cisco@ansible-controller:~/roles-templates/roles/csr-bgp/tasks$ ls
main.yml

```

Copy the content given in the below box as content for file “main.yml” and write/save.

```

cisco@ansible-controller:~/roles-templates/roles/csr-bgp/tasks$ vi main.yml

```

Ensure the saved file has the below content by checking with more/cat command

Content for file “main.yml” in the sub-folder “tasks".

```
---
- name: Generate R1 CSR router iBGP config file
  template: src={{item.hostname}}-BGP.j2 dest=/home/cisco/roles-templates/cfg/{{item.hostname}}-BGP.txt
  with_items: "{{router_list}}"

```

The “main.yml” file in the tasks sub-folder identifies the Jinja2 template that contains “configuration template” for this play book specified using “template” src option.
Output of the task will be written to the location given in the “dest” parameter.

File #2: Content to be added under “templates” sub-folder

Ensure you are in “/home/cisco/roles-templates/roles/csr-bgp” directory

Change directory to “templates” sub-folder

This folder will have a file with name “R1-CSR1K-BGP.j2”.

```

cisco@ansible-controller:~/roles-templates/roles/csr-bgp/templates$ pwd
/home/cisco/roles-templates/roles/csr-bgp/templates
cisco@ansible-controller:~/roles-templates/roles/csr-bgp/templates$ ls
R1-CSR1K-BGP.j2

```

Copy the content given in the below box as content for file “R1-CSR1K-BGP.j2” and write/save.
cisco@ansible-controller:~/roles-templates/roles/csr-bgp/templates$ vi R1-CSR1K-BGP.j2

Ensure the saved file has the below content by checking with more/cat command

Content for file “R1-CSR1K-BGP.j2” in the sub-folder templates

```
router bgp {{item.LCL_ASN}}
 bgp router-id {{item.RID}}
 bgp log-neighbor-changes
 neighbor {{item.PEER_IP}} remote-as {{item.RMT_ASN}}
 neighbor {{item.PEER_IP}} update-source Loopback0
!

```

Template file shows the parametrized configuration file. Parameters are shown inside double curly braces.

File #3: Content to be added under “vars” sub-folder

Ensure you are in “/home/cisco/roles-templates/roles/csr-bgp” directory

Change directory to “vars” sub-folder

This folder will have a file with name “main.yml”.

```
cisco@ansible-controller:~/roles-templates/roles/csr-bgp/vars$ pwd
/home/cisco/roles-templates/roles/csr-bgp/vars
cisco@ansible-controller:~/roles-templates/roles/csr-bgp/vars$ ls
main.yml

```

Copy the content given in the below box as content for file “main.yml” and write/save.
cisco@ansible-controller:~/roles-templates/roles/csr-bgp/vars$ vi main.yml

Ensure the saved file has the below content by checking with more/cat command

Content for file “main.yml in the sub-folder "vars".

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

Vars file contains all values for parameters given in the Jinja2 template. Note this “main.yml” has a single list “dictionary” which contains mapped key:value pairs to populate the iBGP configurations for the jinja2 template.

11.Go to directory “/home/cisco/roles-templates/roles/xr-bgp” and add contents to the three files as given below:

File #1: Content to be added under “tasks” sub-folder

Ensure you are in “/home/cisco/roles-templates/roles/xr-bgp” directory

Change directory to “tasks” sub-folder

This folder will have a file with name “main.yml”.

```

cisco@ansible-controller:~/roles-templates/roles/xr-bgp/tasks$ pwd
/home/cisco/roles-templates/roles/xr-bgp/tasks
cisco@ansible-controller:~/roles-templates/roles/xr-bgp/tasks$ ls
main.yml

```

Copy the content given in the below box as content for file “main.yml” and write/save.

```

cisco@ansible-controller:~/roles-templates/roles/xr-bgp/tasks$ vi main.yml

```

Ensure the saved file has the below content by checking with more/cat command

Content for file “main.yml” in the sub-folder “tasks".

```
---
- name: Generate R2 XRV router iBGP config file
  template: src={{item.hostname}}-BGP.j2 dest=/home/cisco/roles-templates/cfg/{{item.hostname}}-BGP.txt
  with_items: "{{router_list}}"

```

The “main.yml” file in the tasks sub-folder identifies the Jinja2 template that contains “configuration template” for this play book specified using “template” src option.
Output of the task will be written to the location given in the “dest” parameter.

File #2: Content to be added under “templates” sub-folder

Ensure you are in “/home/cisco/roles-templates/roles/xr-bgp” directory

Change directory to “templates” sub-folder

This folder will have a file with name “R2-XRv-BGP.j2”.

```

cisco@ansible-controller:~/roles-templates/roles/xr-bgp/templates$ pwd
/home/cisco/roles-templates/roles/xr-bgp/templates
cisco@ansible-controller:~/roles-templates/roles/xr-bgp/templates$ ls
R2-XRv-BGP.j2

```
Copy the content given in the below box as content for file “R2-XRv-BGP.j2” and write/save.
cisco@ansible-controller:~/roles-templates/roles/xr-bgp/templates$ vi R2-XRv-BGP.j2

Ensure the saved file has the below content by checking with more/cat command

Content for file “R2-XRv-BGP.j2” in the sub-folder "templates".

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

Template file shows the parametrized configuration file. Parameters are shown inside double curly braces.

File #3: Content to be added under “vars” sub-folder

Ensure you are in “/home/cisco/roles-templates/roles/xr-bgp” directory

Change directory to “vars” sub-folder

This folder will have a file with name “main.yml”.

```

cisco@ansible-controller:~/roles-templates/roles/xr-bgp/vars$ pwd
/home/cisco/roles-templates/roles/xr-bgp/vars
cisco@ansible-controller:~/roles-templates/roles/xr-bgp/vars$ ls
main.yml

```

Copy the content given in the below box as content for file “main.yml” and write/save.

```
cisco@ansible-controller:~/roles-templates/roles/xr-bgp/vars$ vi main.yml

```

Ensure the saved file has the below content by checking with more/cat command

Content for file “main.yml in the sub-folder "vars".

```

router_list:
  -  hostname: R2-XRv
     profile: IOS-XR
     RID: 192.168.0.2
     PEER_IP: 192.168.0.1
     LCL_ASN: 1
     RMT_ASN: 1

```

Vars file contains all values for parameters given in the Jinja2 template. Note this “main.yml” has a  single list “dictionary” which contains mapped key:value pairs to populate the iBGP configurations for the jinja2 template.

12.Check the content of the below folder and make sure there is no file ending with the tag “BGP.txt”. After step #13 is executed, two files with the name ending with “BGP.txt” one each for R1 and R2 will be created in this directory.

```

cisco@ansible-controller:~/roles-templates/roles/xr-bgp/vars$ ls -ltr ~/roles-templates/cfg
total 8
-rw-rw-r-- 1 cisco cisco  69 Apr 27 02:19 BASIC-CFG-v1.txt
-rw-rw-r-- 1 cisco cisco 298 Apr 27 02:48 LB-IF-CFG.txt

```

Use Ansible ad-hoc command or create a playbook "using raw module or command module" to check on R1/CSR1K, R2/XRv and confirm that there is no BGP configuration on both the routers.

Note: Please ask for assistance if anyone needs any help with this task

13.Run the playbok using the below command (running in verbose mode with -vvv is optional) from directory “/home/cisco/roles-templates”

```

cisco@ansible-controller:~/roles-templates$ cd cfg/
cisco@ansible-controller:~/roles-templates/cfg$ ls -ltr
total 16
-rw-rw-r-- 1 cisco cisco  69 Apr 27 02:19 BASIC-CFG-v1.txt
-rw-rw-r-- 1 cisco cisco 298 Apr 27 02:48 LB-IF-CFG.txt
-rw-rw-r-- 1 cisco cisco 174 Apr 27 03:41 R2-XRv-BGP.txt
-rw-rw-r-- 1 cisco cisco 149 Apr 27 03:41 R1-CSR1K-BGP.txt

cisco@ansible-controller:~/roles-templates$ ansible-playbook roles-bgp.yml --syntax-check

cisco@ansible-controller:~/roles-templates$ ansible-playbook roles-bgp.yml -v

```

14.Confirm that playbook runs with no errors and check that there are two files ending with name “\*-BGP.txt” has been created in the directory ~/roles-templates/cfg

```

cisco@ansible-controller:~/roles-templates$ more cfg/R1-CSR1K-BGP.txt
router bgp 1
 bgp router-id 192.168.0.1
 bgp log-neighbor-changes
 neighbor 192.168.0.2 remote-as 1
 neighbor 192.168.0.2 update-source Loopback0
!

cisco@ansible-controller:~/roles-templates$ more cfg/R2-XRv-BGP.txt
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

Use Ansible ad-hoc command or create a simple playbook to check if the iBGP configuration has been added and the iBGP session between R1 and R2 is established.

*******This concludes lab exercise #3*********
