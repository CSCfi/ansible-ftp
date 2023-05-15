Ansible Playbook for Openstack + vsftpd
=========

Run this playbook to create an instance on your Openstack space, then install and configure vsftpd.

Requirements
------------
You must have the openstack.cloud collection installed: https://docs.ansible.com/ansible/latest/collections/openstack/cloud/index.html
Type this command:
```sh
ansible-galaxy collection install openstack.cloud
```
and the latest version of openstacksdk of Python: https://pypi.org/project/openstacksdk/  
```sh
pip install openstacksdk
```

You must source your project before launching this playbook:  
```sh
source project_xxxxxxx
```

Also, there will be a need to ssh to the newly created instance during the playbook. Add your key with this command:  
```sh
ssh-add path/to/your/key
```

Example Playbook
--------------

Launch the playbook like this:
```sh
ansible-playbook main.yaml
```
It will prompt for:
- The name of the instance that you want to create. By default **ftp-server**
- The image that you want to use. By default **Ubuntu-22.04** (Use this command to list the different images: `openstack image list`)
- The flavor that you want to use. By default **standard.small** (Use this command to list the different flavors: `openstack flavor list`)
- The key that you will use to SSH to your instance.
- The network that you will use (Use this command to list the different networks: `openstack network list`)
- The name of the FTP user. By default **ftpuser**
- The password for the FTP user

At the end of the playbook, an output message will be displayed with the name of your instance, the public IP address of your instance and the name of the FTP user:  
`The instance ftp-server has been created with public IP address: 123.45.67.89. The user is ftpuser`

Role Variables
----------------

After the var_prompts, a set_fact will be triggered to store the FTP user, the password and the name of the instance that will be created.

```yaml
  - hosts: localhost
    var_prompts:
    ...
    tasks:
    - set_fact:
      ftpuser: "{{ ftp_user }}"
      passwd: "{{ ftp_user_password }}"
      instance: "{{ instance_name }}"
    ...
```
Those three variables will be used for later plays. They will be recalled like this:
```yaml
- hosts: created_instance
  vars:
     ftp_user: "{{ hostvars['localhost']['ftpuser'] }}"
     pass_user: "{{ hostvars['localhost']['passwd'] }}"
     output_instance: "{{ hostvars['localhost']['instance'] }}"
```

To-Do
----------------
Add tags to only run specific tasks.
