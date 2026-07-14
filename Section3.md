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

01 - Hand-On
---
```
ansible@ubuntu-c:~$ ping centos1
cat ansible.cfg
cat hosts
rm -rf /home/ansible/.ssh/known_hosts

ansible all -m ping
ANSIBLE_HOST_KEY_CHECKING=False
```
---
Varible
```
## To Ignore host key check 
ANSIBLE_HOST_KEY_CHECKING=False
```

```
Prerequestites : You need to delete related setting in known_hosts file 
ANSIBLE_HOST_KEY_CHECKING=False ansible all -m ping
```
OUTPUT
```
centos1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.9"
    },
    "changed": false,
    "ping": "pong"
}
```
---

02 - Hands On
---
```
cp -r 01/* 02/

# added ansible.cfg ( commmand ကနေဆိုအမြဲမှတ်မိနေနိုင်မှာမဟုတ်တဲ့အတွက် ေကြာင့် ansible config ဖိုင်ထဲမှာ HOST KEY CHECKINGနဲ့ပတ်သက်တဲ့ အကြောင်းကိုထပ်ထည့်မယ်)
host_key_checking = False 

rm -rf /home/ansible/.ssh/known_hosts
ansible all -m ping

### **got success with no user interaction within our lab**
```
---
03 - Hands On
---
```
# mixture centos & ubuntu
mkdir 03
```

ansible.cfg
```
[defaults]
inventory = hosts
host_key_checking = False
```

hosts
```
[centos]
centos1
centos2
centos3

[ubuntu]
ubuntu1
ubuntu2
ubuntu3

```

### Try ping all group
---
```
ansible all -m ping
```
OUTPUT
```
centos3 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: Warning: Permanently added 'centos3,172.23.0.3' (ECDSA) to the list of known hosts.\r\nansible@centos3: Permission denied (publickey,gssapi-keyex,gssapi-with-mic,password).",
    "unreachable": true
}
centos2 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: Warning: Permanently added 'centos2,172.23.0.4' (ECDSA) to the list of known hosts.\r\nansible@centos2: Permission denied (publickey,gssapi-keyex,gssapi-with-mic,password).",
    "unreachable": true
}
ubuntu1 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: Warning: Permanently added 'ubuntu1,172.23.0.6' (ECDSA) to the list of known hosts.\r\nansible@ubuntu1: Permission denied (publickey,password).",
    "unreachable": true
}
ubuntu2 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: Warning: Permanently added 'ubuntu2,172.23.0.5' (ECDSA) to the list of known hosts.\r\nansible@ubuntu2: Permission denied (publickey,password).",
    "unreachable": true
}
ubuntu3 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: Warning: Permanently added 'ubuntu3,172.23.0.8' (ECDSA) to the list of known hosts.\r\nansible@ubuntu3: Permission denied (publickey,password).",
    "unreachable": true
}
centos1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.9"
    },
    "changed": false,
    "ping": "pong"
}
```
---

**Centos ကလွဲပြီး ကျန်တာတွေမှာ ssh-key added မလုပ်ရသေးလို့ Error တက်တာပါ**  
**အရင်ကလုပ်ခဲ့သလို loopနဲံပတ်ပြီးထည့်လိုက်လည်း ရပါတယ်**
```
sudo apt update 
sudo apt install sshpass ( sshpass ကရှိပြိးသားဆို အပေါ် အဆင့်၂ဆင့်ကလုပ်စရာမလိုပါ)
echo password > password.txt

for user in ansbile root
do 
 for os in ubuntu centos 
  do 
    for instance in 1 2 3
     do
       sshpass -f password.txt ssh-copy-id -o StrictHostKeyChecking=no ${user}@${os}${instance}
     done
  done

ansible all -m ping
```
OUTPUT   
```
ubuntu2 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.10"
    },
    "changed": false,
    "ping": "pong"
}
ubuntu1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.10"
    },
    "changed": false,
    "ping": "pong"
}
centos3 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.9"
    },
    "changed": false,
    "ping": "pong"
}
....... # All Success
```

```

# group နဲ့ ping ချင်ရင် 
ansible centos -m ping
ansible ubuntu -m ping

ansible '*' -m ping

ansible all -m ping -o
ansible centos --list-hosts
ansible ubuntu --list-hosts
ansible all --list-hosts
ansible centos1 --list-hosts
ansible centos1 -m ping -o

# use regular expression
ansible ~.*3 --list-hosts
```
---

04 - Hands On
---

hosts file
```
[centos]
centos1 ansible_user=root
centos2 ansible_user=root
centos3 ansible_user=root

[ubuntu]
ubuntu1
ubuntu2
ubuntu3
```

```
ansible all -m ping -o
id
ansible all -m command -a 'id' -o
```
---
05 - Hands On
---
Host 
```
# connect to our hosts as more users and escalate to a super user

[centos]
centos1 ansible_user=root
centos2 ansible_user=root
centos3 ansible_user=root

[ubuntu]
ubuntu1 ansible_become=true ansible_become_pass=password
ubuntu2 ansible_become=true ansible_become_pass=password
ubuntu3 ansible_become=true ansible_become_pass=password
```

```
# ubuntu → ansible → root
# centos1 → root 

ansible all -m ping -o
ansible all -a 'id' -o

```

```
# connect to cenos1 as root
ssh root@centos1 -p 22

# update sshd so that it listens on port 2222 only
/utils/update_sshd_ports.sh 2222

# return back to ubuntu-c
exit
```

```
ansible all -m ping -o
```
OUTPUT
```
centos1 | UNREACHABLE!: Failed to connect to the host via ssh: ssh: connect to host centos1 port 22: Connection refused
ubuntu3 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3.10"},"changed": false,"ping": "pong"}
centos3 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3.9"},"changed": false,"ping": "pong"}
ubuntu2 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3.10"},"changed": false,"ping": "pong"}
ubuntu1 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3.10"},"changed": false,"ping": "pong"}
centos2 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3.9"},"changed": false,"ping": "pong"}
```

06 - Hands On
---
```
# Update Host File -> Centos1 Port Added
centos1 ansible_user=root ansible_port=2222

# Ping command again
ansible all -m ping -o

# OUTPUT SUCCESS
```
---
07 - Hands On 
---
```
# 06 Another version
centos1:2222 ansible_user=root
ansible all -m ping -o

# OUTPUT SUCCESS
```
---
08 - Hands On 
---
```
# Added Control Group
# Hosts File
[control]
ubuntu-c ansible_connection=local

ansible all -m ping -o
```

OUTPUT
```
ubuntu-c | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3.10"},"changed": false,"ping": "pong"}
ubuntu1 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3.10"},"changed": false,"ping": "pong"}
centos3 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3.9"},"changed": false,"ping": "pong"}
centos1 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3.9"},"changed": false,"ping": "pong"}
centos2 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3.9"},"changed": false,"ping": "pong"}
ubuntu2 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3.10"},"changed": false,"ping": "pong"}
ubuntu3 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3.10"},"changed": false,"ping": "pong"}
```
---

09 - Hands On 
---
```
# Using ranges 
# Hosts file
[control]
ubuntu-c ansible_connection=local

[centos]
centos1 ansible_user=root ansible_port=2222
centos[2:3] ansible_user=root

[ubuntu]
ubuntu[1:3] ansible_become=true ansible_become_pass=password

# Confirm Host
ansible all --list-hosts 

# PING Conmand again
ansible all -m ping -o
```

10 - Hands On
```
# groupvars 
# centos မှာဆိုရင် ansible_user = root ဆိုပြီးတော့ Duplicate ဖြစ်နေတာတွေရှိတယ် အဲ့ဒါတွေကို groupvars သုံးပြီး mitigateလုပ်လို့ရတယ်

# Hosts File
[control]
ubuntu-c ansible_connection=local

[centos]
centos1 ansible_port=2222
centos[2:3]

[centos:vars]
ansible_user=root 

[ubuntu]
ubuntu[1:3] 

[ubuntu:vars]
ansible_become=true
ansible_become_pass=password

# each CentOS host will receive the CentOS group vars
# each ubuntu hosts will recevie the Ubuntu group vars 
```
---
11 - Hands On
---
```
# Host file added "children declaration"
[linux:children]
centos
ubuntu

# If we ping the Linux group, it will automatically inherit the CentOS and Ubutnu group

ansible linux -m ping -o

```
---

12 - Hands On
---
```
# Added Hosts File 
[all:vars]
ansible_port=1234


# PING
ansible all -m ping -o 
```
OUTPUT

- precedene အရဆိုရင် သူ့ Group မှာသတ်မှတ်ထားတဲ ့ansible_port က priority ပိုမြင့်တဲ့အတွက် centos1 မှာက ansible_port=2222လို့သတ်မှတ်ထားတာမို့ အဲ့ဒီဟာအတွက် က success ဖြစ်မယ် 
- ကျန်တာက Fail မယ် 
- ubuntu-c က local connection ကို သုံးတာမို့ successဖြစ်တယ်
```
centos2 | UNREACHABLE!: Failed to connect to the host via ssh: ssh: connect to host centos2 port 1234: Connection refused
centos3 | UNREACHABLE!: Failed to connect to the host via ssh: ssh: connect to host centos3 port 1234: Connection refused
ubuntu1 | UNREACHABLE!: Failed to connect to the host via ssh: ssh: connect to host ubuntu1 port 1234: Connection refused
ubuntu3 | UNREACHABLE!: Failed to connect to the host via ssh: ssh: connect to host ubuntu3 port 1234: Connection refused
ubuntu2 | UNREACHABLE!: Failed to connect to the host via ssh: ssh: connect to host ubuntu2 port 1234: Connection refused
ubuntu-c | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3.10"},"changed": false,"ping": "pong"}
centos1 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python3.9"},"changed": false,"ping": "pong"}
```
---

13 - Hands On
---
```
# Added Host File
[linux:vars]
ansible_port=1234
```
---
14 - Hands On
---
Host File ကို Yaml လို့ရေးတာ (INI မဟုတ်ပဲ​)
```
# ansible.cfg 
[defaults]
inventory = hosts.yaml
host_key_checking = False

```
Host file 
```
---
control:
  hosts:
    ubuntu-c:
      ansible_connection: local
centos:
  hosts:
    centos1:
      ansible_port: 2222
    centos2:
    centos3:
  vars:
    ansible_user: root
ubuntu:
  hosts:
    ubuntu1:
    ubuntu2:
    ubuntu3:
  vars:
    ansible_become: true
    ansible_become_pass: password
linux:
  children:
    centos:
    ubuntu:
...
```
---
15 - Hands On
---
- Yaml နဲ့မဟုတ် တော့ပဲ JSON နဲ့ 
```
# yaml ကနေ json ကိုထုတ်မယ်
python3 -c 'import sys, yaml, json; json.dump(yaml.load(sys.stdin, Loader=yaml.FullLoader), sys.stdout, indent=4)' < hosts.yaml > hosts.json
```
---

16 - Hands On
---
```
ansible all -i hosts.yaml --list-hosts
ansible all -i hosts.json --list-hosts
ansible all -i hosts --list-hosts

ansible linux -m ping -e 'ansible_port=22' -o
```
ansible -help | more
-e ( existing inventory ရဲ့ vars ကို override လုပ်လို့ရတယ်)

---

## 11. Ansible Modules 

* Ansible Modules 
* The setup module 
* The file module
* Color notation used during Ansible execution 
* Idempotence 
* The copy module 
* The command module
* Ansible-doc

Setup Module
---
* This module automatically executed when using playboooks to gather useful information as varibales, about the remote target. 
* can also be executed by the ansible command to find out the variables available to host 
* Ansible provides many 'facts' about a target automatically 
* This module is also supported for windows targrs 
* In Ansible 2.10, this has been moved to ansbile base and is classed as a 'buildin' plugin

```
ansible centos1 -m setup
ansible centos1 -m setup | more
```
---

File Module
---
Used for file, symlinks and directory manipulation

* Sets attributes of files, symlinks and directories, or ,removes files ,symlinks and directories
* Many other modules support the same options as the 'file' moduel, including [copy], [template], and [assemble]
* For Windows targes, use the [win_file] module instead
* In Ansible 2.10, this has been moved to ansible-base and is classed as a 'bulitin' plugin. It can be referenced via the same 'file' or 'ansible.builtin.file'
* Documentation - reference offical website 
```
# file create on our remote 
ansible all -m file -a 'path=/tmp/test state=touch'
-a = argument

ls -altrh /tmp/test
ssh centos2 ls -altrh /tmp/test
```
---

Ansilbe Colors
---
Signifies Success or Failure, with or without changes 

* Red = Failure
* Yellow = Success, with Changes
* Green = Success, no Changes
---

Unix Permissions
---
User / Group / Other
RWX /421
```
User Group Other
RWX   RWX   RWX 

Permission 600 = 

RW-  ---   ---
```

```
ansible all -m file -a 'path=/tmp/test state=file mode=600'
ls -altrh /tmp/test
```
---

Idempotency
---
An operation is idempotent, if the result of performing it once, is exactly the same as the result of performing it repeadedly without any intervening actions.

```
chmod 644 /tmp/test
ansible all -m file -a 'path=/tmp/test state=file mode=600'
```
---

Copy Module
---
* The 'copy' module copies a file from the local or remote target, to a location on the remote target. Use the [fetch] module, to copy files from a remote target, to a local target
* If you need variable interpolation in the copied files, use the [template] module. 
* For Windows targets, use the [win_copy] modules instead
* In Ansible 2.10, this has been moved to ansible-base and is classed as a 'builtin' plugin. It can be referenced via the name 'copy' or 'ansible.builtin.copy'

```
touch /tmp/x
ansible all -m copy -a 'src=/tmp/x dest=/tmp/x'
ansible all -m copy -a 'remote_src=yes src=/tmp/x dest=/tmp/y'
```
---

Command Module
---
Used for execcuting remote commands 

* The 'command' module, takes the command name folloewd by a list of space-delimited arguments. 
* The given command will be executed on all selected nodes
* It is not processed through the shell, so, variables like $HOME and operations like <,>,|,; and &, will not work. Use the [shell] module if you need these features.
* For windows targes, use the [win_command] module instead
* In Ansible 2.10, this has been moved to ansible-base and is classed as a 'builtin' plugin. It can be referenced via the name 'command' or 'ansible.builtin.command'

```
ansible all -a 'hostname' -o
ansible all -a 'touch /tmp/test_command_module creates=/tmp/test_command_module'

ansible all -a 'rm /tmp/test_command_module removes=/tmp/test_command_module'
```
---

Fetch module
---
Look at Official Site

```
ansible all -m file -a 'path=/tmp/test_modules.txt state=touch mode=600'

ansible all -m fetch -a 'src=/tmp/test_modules.txt dest=/tmp/' -o
ansible all -m fetch -a 'flat=yes src=/tmp/test_modules.txt dest=/tmp/tetched'
```
---

ansible-doc
---
Manual Pages for Ansible
* Convenient and greate alternative, to the online docs
* Try - 
  > ansible-doc file   
  > ansible-doc fetch
* Also provides you with the source code location, for modules, allowing you to understand, how a moudle works behind the scenes

```
ansible-doc file 
ansible-doc fetch
```
---