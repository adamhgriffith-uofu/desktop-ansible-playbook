
# Local Ansible Playbook Development with Vagrant

A local Oracle VirtualBox VM will provide a solid developer experience as well as the ability to test the Ansible playbook used for work desktop configuration.

## Requirements

### Install Ansible

This project uses Miniconda3 (or Conda) to create a Python interpreter with Ansible and other necessary dependencies.

1. Navigate to the [Miniconda3 downloads page](https://docs.conda.io/en/latest/miniconda.html) to download and install Conda on your system.
2. Execute the following to create the `desktop-ansible` Conda environment on your system:

   ```shell
   conda env create -f ./environment.yml
   ```

3. Activate the Conda environment and check that Ansible is properly installed:

   ```shell
   [your@localmachine]$ conda activate desktop-ansible
   (desktop-ansible) [your@localmachine]$ ansible --version
     ansible [core 2.12.1]
     config file = None
     configured module search path = ['/home/you/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
     ansible python module location = /home/you/miniconda3/envs/chpc-ansible/lib/python3.10/site-packages/ansible
     ansible collection location = /home/you/.ansible/collections:/usr/share/ansible/collections
     executable location = /home/you/miniconda3/envs/chpc-ansible/bin/ansible
     python version = 3.10.2 | packaged by conda-forge | (main, Jan 14 2022, 08:02:09) [GCC 9.4.0]
     jinja version = 3.0.3
     libyaml = True
   ```

4. Use the following resources as references for any Ansible-related questions:
    * [Ansible Quickstart Guide](https://docs.ansible.com/ansible/2.9/user_guide/quickstart.html)
    * [Ansible Concepts](https://docs.ansible.com/ansible/2.9/user_guide/basic_concepts.html)
    * [Intro to playbooks](https://docs.ansible.com/ansible/latest/user_guide/playbooks_intro.html)
    * [Ansible: Module Index](https://docs.ansible.com/ansible/2.9/modules/modules_by_category.html)

### Install Oracle VirtualBox

Oracle VirtualBox is the virtualization provider-of-choice for this project. Download and install VirtualBox using the instructions provided on the [VirtualBox downloads page](https://www.virtualbox.org/wiki/Downloads).

### Install Vagrant

[Hashicorp Vagrant](https://www.vagrantup.com/) is a tool that "leverages a declarative configuration file which describes all your software requirements, packages, operating system configuration, users, and more". The configuration for this project is described in the `./Vagrantfile`.

Download and install Vagrant using the instructions provided on the [Vagrant downloads page](https://www.vagrantup.com/downloads). Once Vagrant is installed execute the following commands on your system to install plugins necessary for this project:

* VirtualBox Guest Additions Plugin:

  ```shell
  vagrant plugin install vagrant-vbguest
  ```

* Vagrant Disk-size Plugin:

  ```shell
  vagrant plugin install vagrant-disksize
  ```

Optionally install the Vagrant `bash` autocompletion (recommended for Linux users):

```shell
vagrant autocomplete install --bash
```

## Create `secrets.yml`

Copy `./secrets.yml.tmpl` to the following place in this project: `./secrets.yml` and replace the placeholder key-value pairs with values appropriate for the work desktop and user.

## Run the Playbook

Activate the Conda environment, create the virtual machine, and run the Ansible playbook:

```shell
[your@localmachine]$ conda activate desktop-ansible
(desktop-ansible) [your@localmachine]$ vagrant up
...
...
==> default: Running provisioner: ansible...
    default: Running ansible-playbook...
...
No config file found; using defaults

PLAY [all] *********************************************************************
...
PLAY RECAP *********************************************************************
default                    : ok=43   changed=8    unreachable=0    failed=0    skipped=6    rescued=0    ignored=0
```

* Develop and test the Ansible playbook source on a currently running VM by executing `vagrant provision` one or more times.
* Remember to also lint any local changes made to the Ansible playbook source (see [Ansible Linting](#ansible-linting))
* Vagrant creates its own Ansible inventory file and makes use of `ansible/group_vars/all.yml` (see [Ansible and Vagrant](https://www.vagrantup.com/docs/provisioning/ansible_intro) for more information).

## Ansible Linting

Any changes made to the Ansible playbook source should be linted before making its way beyond local development.
* Linting for this project is configured using the `./.ansible-lint` file.
* Run by executing `ansible-lint -p` in the `./` directory.

For example, a fatal violation will occur when a variable name does not adhere to variable naming standards:

```shell
(desktop-ansible) [your@localmachine]$ ansible-lint -p
WARNING  Listing 1 violation(s) that are fatal
tasks/slate.yml:2: var-naming Task registers a variable that violates variable naming standards
You can skip specific rules or tags by adding them to your configuration file:
# .ansible-lint
warn_list:  # or 'skip_list' to silence them completely
  - experimental  # all rules tagged as experimental

Finished with 0 failure(s), 1 warning(s) on 25 files.
```

See [Ansible Lint Documentation: Usage](https://ansible-lint.readthedocs.io/en/latest/usage.html) for additional information on this subject.

## Teardown

The [Vagrant CLI](https://www.vagrantup.com/docs/cli) is documented at great length but here are some of the highlights:

| Command           | Description                                                                                                                                                                                                                                                                           |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `vagrant halt`    | This command shuts down the running machine Vagrant is managing and allowing `vagrant up` to start the machine again.                                                                                                                                                                 |
| `vagrant destroy` | This command stops the running machine Vagrant is managing and destroys all resources that were created during the machine creation process. After running this command, your computer should be left at a clean state, as if you never created the guest machine in the first place. |

Finally, deactivate the Conda environment:

```shell
(desktop-ansible) [your@localmachine]$ conda deactivate
[your@localmachine]$
```
