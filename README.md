Cloud Gigas IaC
===============

Role for creating and modifiying VMs on the Gigas Cloud using Infraestructure as Code methodology.
Uses Gigas API 1.0 (Madrid)

Requirements
------------

An API account for each datacenter on Gigas.

Role Variables
--------------

Variables that can be placed either in defaults/main.yml or vars/main.yml.

Some of then should me placed within *group_vars* or *host_var* like **location_id** and **network_id**.
Consider vaulting sensitive ones.

|Varriable|Description| Used by task|
|--|--|--|
|**api_user**|Gigas API User (for desired datacenter)|auth.yml|
|**api_password**|Gigas API User password (for desired datacenter)|auth.yml|
|**location_id**|API Locationn ID|all tasks|
|**root_password**|Initial root user password|create_vm.yml|
|**distro**|Distro family for template listing|
|**network_id**|Network VLAN ID (for adding new IP addresses)|ip-add.yml, create-vm.yml|
|**ip_priv_network**| Network address (for adding/modifiying new IP address)|ip-add.yml|
|**ip_priv_broadcast**| Network broadcast address (for adding/modifying new IP address)|ip-add.yml|

Role IaC variables structure
----------------------------

The Virtual Machines are defined in a YAML dictionary that should be referenced in the playbook with *vars_files*. 
An example location could be *vars/external_vars.yml*
The default dictionary variable is named **vms**. An example structure below:
```
vms:
  vm1:
    hostname: AWESOMEVM01
    label: labelwithnospaces
    cpu: 2
    ram: 2048
    swap: 1
    swap_type: 7
    disk: 50
    disk_type: 7
    #disk2: 50
    #disk2_type: 7
    template_id: 989
    ip_priv_net_id: 597
    ip_priv_address: 172.30.41.10
    ip_priv_netmask: 24
    ip_priv_gateway: 172.30.41.1
  vm2:
    hostname: AWESOMEVM02
    label: labelwithnospaces
    cpu: 2
    ram: 2048
    swap: 1
    swap_type: 7
    disk: 50
    disk_type: 7
    #disk2: 50
    #disk2_type: 7
    template_id: 989
    ip_priv_net_id: 597
    ip_priv_address: 172.30.41.11
    ip_priv_netmask: 24
    ip_priv_gateway: 172.30.41.1
```

|Variable|Description| Notes|
|--|--|--|
|hostname|Hostname|Up to 25 characters|
|label|user-friendly VM description|Can't contain spaces|
|cpu|number of CPUs assigned to the VM||
|ram|amount of RAM assigned to the VM| In Megabytes|
|swap| Set swap space in Gigabytes||
|swap_type|Type of disk (SATA, SAS, SSD) for swap space|For API 1.0 values are: SAS 6, SATA 7, SSD 9|
|disk|Set the disk space in Gigabytes for this VM for the main disk||
|disk_type|Type of disk (SATA, SAS, SSD) for swap space|For API 1.0 values are: SAS 6, SATA 7, SSD 9|
|disk2| Size of a secondary disk for the virtual machine||
|disk2_type|Type of disk (SATA, SAS, SSD) for swap space|For API 1.0 values are: SAS 6, SATA 7, SSD 9||
|template_id|Template ID to build the vm|[Listing templates](http://api.docs.gigas.com/images.html#list-templates)|
|ip_priv_net_id| VLAN ID (for adding new IP addresses)|[List networks](http://api.docs.gigas.com/network.html#list-networks)|
|ip_priv_address|VM private IP address||
|ip_priv_netmask|VM private IP netmask prefix||
|ip_priv_gateway|VM private IP gateway||

Each of the **vms**'s dictionary element (like) **vm1**, **vm2**, **vmN**, etc, can have a different name. Using a descriptive name like the hostname can be more convenient.

Dependencies
------------

None for the moment.


Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: localhost
      gather_facts: no
      vars_files:
      - vars/external_vars.yml
      
      roles:
         - cloud-gigas-iac

Bugs
----

* Due to Gigas cloud's issues is not possible to create a VM with 3 SATA disks (primary disk, swap, secondary disk). A workaround is to just create primary disk and a swap. After creation then add the third SATA disk. 

To-Do
-----

* Destroy VMs when related information is removed from the IaC dictionary
* Modify VMs parameters when IaC dictionary is changed

License
-------

GNU GPLv3

Author Information
------------------

Luis Garnica Guilarte (luisgarnica42@gmail.com, luis.garnicaguilarte@soprasteria.com)
