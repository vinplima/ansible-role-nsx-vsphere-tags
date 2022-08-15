NSX-vSphere Tags Ansible Role
=========

This role creates categories and tags in vCenter. For each category/tag with configured VMs, the respective scope/tag will be configured in NSX-T. In that case, if a Category/Tag is created in vCenter, but not added to any VM, the respective Scope/Tag will not be created in NSX-T. 

At this present version, this role creates tags in vCenter and creates the same tags in NSX-T when there is at least one VM with the tag associated. However, tag removal from vCenter and adding tag into VMs are not in scope of this role.

#### Table of Contents
- [NSX-vSphere Tags Ansible Role](#nsx-vsphere-tags-ansible-role)
      - [Table of Contents](#table-of-contents)
  - [Requirements](#requirements)
  - [Role Variables](#role-variables)
    - [basic variables:](#basic-variables)
    - [NSX-T variables:](#nsx-t-variables)
    - [vCenter variables:](#vcenter-variables)
    - [Tags variable:](#tags-variable)
  - [Example Playbook](#example-playbook)
  - [Versions](#versions)
  - [License](#license)
  - [Author Information](#author-information)

Requirements
------------

This role uses [Community VMware](https://docs.ansible.com/ansible/latest/collections/community/vmware/index.html) and [VMware Ansible for NSX-T](https://github.com/vmware/ansible-for-nsxt) Collections. It is also necessary to install vSphere Automation SDK and pyvmomi Python modules. 

The NSX-T Ansible Collection and vSphere Automation SDK are installed directly from Github, as shown below:

~~~
ansible-galaxy collection install git+https://github.com/vmware/ansible-for-nsxt

pip install git+https://github.com/vmware/vsphere-automation-sdk-python.git
~~~

This role has been tested with the following versions of the mentioned Ansible Collections:

- community.vmware == 1.18.0
- vmware.ansible_for_nsxt == 3.2.0

Role Variables
--------------

### basic variables:
- vcenter_hostname: vCenter hostname (type: String, default: vcenter.local)
- nsx_hostname: NSX-T hostname (type: String, default: nsx.local)
- vcenter_username: vCenter user name (type: String, default: administrator@vsphere.local)
- vcenter_password: vCenter user password (type: String, default: VMware1!)
- nsx_username: NSX-T user password (type: String, default: admin)
- nsx_password: NSX-T user password (type: String, default: VMware1!VMware1!)
- validate_certs: if certificates must be validated during connection (type: Boolean, default: false)

### NSX-T variables:
- remove_other_tags: if tags not declared for the VM in vSphere must be removed in NSX-T (type: Boolean, default: false)
- nsx_ignore_errors: if must ignore errors during Tag creation in NSX-T (type: Boolean, default: false)

### vCenter variables:
- vm_root_folder: folder in which VMs will be checked for tags (type: String, default: /Datacenter/vm)
- category_state: state of the category ([present, absent], default: present)
- tag_state: state of the tag ([present, absent], default: present)

### Tags variable:
- nsx_vsphere_tags: hash of scopes/tags to be created. Default:

~~~
nsx_vsphere_tags:
  scope: 
    - name: nsx-vsphere-demo-scope
      description: Tags created in vSphere and in NSX-T
      tags:
        - name: demo-tag
          description: Demo tag
~~~


Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

~~~
- name: Create tag demo in vCenter vcenter.local and in NSX-T nsx.local if there is any VM with this tag in vCenter
  hosts: vmwareinfra
  connection: local

  roles:
    - roles/nsx-vsphere-tags
  
  vars:
    nsx_vsphere_tags:
      scope: 
        - name: demo
          description: Demo scope
          tags:
            - name: demo
              description: Demo tag
~~~

Versions
-------

* v1.0.0: Creates tags in vCenter and sync tags with associated VMs to NSX-T
* v1.1.0: Added variable nsx_ignore_errors, which makes possible to execute the playbook even though the VM is not found in NSX-T

License
-------

GPLv3

Author Information
------------------

Vinicius Porto Lima - viniciusmaximus@gmail.com

Github - [https://github.com/vinplima](https://github.com/vinplima)
