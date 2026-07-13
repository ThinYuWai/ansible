# Ansible Architecture and Design

Ansible Configuration   
> Configuration Options and Precedence

Ansible Inventories   
> Host & Group Variables, Group Ranges Privileged Connectivity  
> apply host and group variables

Ansible Modules    
> Common Modules, Interactivity with the CLI 

## 9. Ansible Configuration

- ansible-base is a core package that provides the ansible binaries and supporting files to execute ansible
- The ansible package provides individual modules and plugins that were supported in Ansible 2.0, the installation of ansbile automatically installs ansible base as a dependency.

### Configuration Files Precedences 
---
1. ANSIBLE_CONFIG ( Environment variable, with a filename target )
2. ./ansible.cfg (An ansible.cfg fiel, in the current directory)
3. ~/.ansible.cfg ( A hidden file, called .ansible.cfg, in the users home directory)
4. /etc/ansible/ansible.cfg ( Ansible ကို install လိုက်တဲ့ချိန်မှာ ဆောက်ပေးလိုက်တာ Typically provieded, through packaged or system installations of Ansible)

---
```
ansible --version
pip freeze | grep ansible
```
OUTPUT
```
ansible [core 2.17.4]
  config file = None
  configured module search path = ['/home/ansible/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python3.10/dist-packages/ansible
  ansible collection location = /home/ansible/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.10.12 (main, Jul 29 2024, 16:56:48) [GCC 11.4.0] (/usr/bin/python3)
  jinja version = 3.1.4
  libyaml = True
```
---
```
/etc/ansible/ansible.cfg
su - 
mkdir /etc/ansible
touch /etc/ansible/ansible.cfg
```
OUTPUT
```
ansible [core 2.17.4]
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/ansible/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python3.10/dist-packages/ansible
  ansible collection location = /home/ansible/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.10.12 (main, Jul 29 2024, 16:56:48) [GCC 11.4.0] (/usr/bin/python3)
  jinja version = 3.1.4
  libyaml = True
```
---
```
cd ~
pwd
touch .ansible.cfg
ansible --version
```
OUTPUT
```
ansible [core 2.17.4]
  config file = /home/ansible/.ansible.cfg
  configured module search path = ['/home/ansible/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python3.10/dist-packages/ansible
  ansible collection location = /home/ansible/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.10.12 (main, Jul 29 2024, 16:56:48) [GCC 11.4.0] (/usr/bin/python3)
  jinja version = 3.1.4
  libyaml = True
```
---

```
mkdir testdir
cd testdir
ansible --version

touch ansible.cfg
ansible --version
```
OUTPUT
```
ansible [core 2.17.4]
  config file = /home/ansible/testdir/ansible.cfg
  configured module search path = ['/home/ansible/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python3.10/dist-packages/ansible
  ansible collection location = /home/ansible/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.10.12 (main, Jul 29 2024, 16:56:48) [GCC 11.4.0] (/usr/bin/python3)
  jinja version = 3.1.4
  libyaml = True
```
---
```
touch my_example_ansible.cfg
export ANSIBLE_CONFIG=/home/ansible/my_example_ansible.cfg

ansible --version
```
OUTPUT
```
ansible [core 2.17.4]
  config file = /home/ansible/my_example_ansible.cfg
  configured module search path = ['/home/ansible/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python3.10/dist-packages/ansible
  ansible collection location = /home/ansible/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.10.12 (main, Jul 29 2024, 16:56:48) [GCC 11.4.0] (/usr/bin/python3)
  jinja version = 3.1.4
  libyaml = True
```
---
CLEAN UP 
```
rm -r testdir
unset ANSIBLE_CONFIG
sudo rm /etc/ansible/ansible.cfg
rm ~/.ansible.cfg
```
OUTPUT After cleanup 
```
ansible [core 2.17.4]
  config file = None
  configured module search path = ['/home/ansible/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python3.10/dist-packages/ansible
  ansible collection location = /home/ansible/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.10.12 (main, Jul 29 2024, 16:56:48) [GCC 11.4.0] (/usr/bin/python3)
  jinja version = 3.1.4
  libyaml = True
```
---
## 10. Ansible Inventories 

* Ansible Inventories
* Provide Ansible Connectivity to our centos hosts via root
* Provide Ansible Connectivity to our ubuntu hosts via sudo 
* Inventory host variables (host vars)
* Simplification of Inventory with ranges 
* Inventory group variables (groupvars)
* Inventory children groups 
