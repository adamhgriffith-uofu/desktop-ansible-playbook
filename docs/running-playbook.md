
# Running the Ansible Playbook

The Ansible playbook, an SSH configuration file, and a `hosts.yml` inventory file are used for provisioning work desktops.

## Requirements

Use the installation instructions found in [Local Ansible Playbook Development with Vagrant](development.md) to install:
* Ansible
* Miniconda3

## Create `secrets.yml`

Follow the instructions found in [Local Ansible Playbook Development with Vagrant](development.md) to create this file.

## Create `hosts.yml`

Copy `./inventory/hosts.yml.tmpl` to the following place in this project `./inventory/hosts.yml` and replace the placeholder key-value pairs with values appropriate for the work desktop and user.

For more information on Ansible inventories see:
* [How to build your inventory](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html)
* [ansible.builtin.yaml – Uses a specific YAML file as an inventory source.](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/yaml_inventory.html)

## Run the Playbook

Activate the Conda environment, change directories, and verify Ansible can see the host(s):

```shell
[your@localmachine]$ conda activate desktop-ansible
(desktop-ansible) [your@localmachine]$ ansible all -m ping -i ./inventory/hosts.yml
desktop| SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

Test permission escalation on the host(s):

```shell
(desktop-ansible) [your@localmachine]$ ansible all -a "/bin/ls -al /root" -i ./inventory/hosts.yml --become --become-user root
desktop| CHANGED | rc=0 >>
total 40
dr-xr-x---.  3 root root  170 Feb 28 22:27 .
dr-xr-xr-x. 17 root root  224 Jul  3  2017 ..
-rw-------.  1 root root 6913 Jul  3  2017 file.ext
...
```

**Note:** If you receive an error during these steps, double-check the contents of your `hosts.yml` file, paying special attention to the Ansible variables.

Finally, verbosely run the Ansible playbook itself:

```shell
(desktop-ansible) [your@localmachine]$ ansible-playbook -v -i ./inventory/hosts.yml --extra-vars "@./secrets.yml" ./playbook.yml
...
PLAY [all] ******************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************
...
...
```

## Teardown

Follow the instructions found towards the bottom in [Local Ansible Playbook Development with Vagrant](development.md) tear down Conda.
