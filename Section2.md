# Setup of the Lab Env and Resources

## 4. Installing Docker

### Linux Installation
```
--- Reference Official Site ----
chmod +X $DOCKER_CONFIG/cli-plugins/docker-compose    
## docker-compose golabally avaiable
sudo cp $DOCKER_CONFIG/cli-plugins/docker-compose /usr/local/bin
docker compose version 
## add user to docker group 
### user can use docker as expected without any restrictions
sudo usermod -aG docker $USER
su - $USER 
docker run -it --rm ubuntu bash
root@hostname:. #cat /etc/os-release 

```

## 5. Installing the Ansible Lab

[Github](https://github.com/spurin/diveintoansible-lab)

## 6. SSH Connectivity between host

- **Agentless Architecture**
- For connectivity to take place between Ansible and our targets a trusted relationship is required for automated **passwordless connectivity**

### SSH Connectivity Overview 

Ansbile ↔︎ Ubunt1 (Target Server)
1. ubuntu-c(client) requests an SSH session with ubuntu1(server)
2. SSH Protocol version are exchanged, if they are compatible they agree, otherwise, no connection
3. Key Exchange, information is shared with the cryptographic primitives that each side supports 
4. Both client and server, negotitae session key, using Diffie-Hellman algorithm create a symmetric key. At this point, if the server is not know to the client, you will see the request to add the key fingerprint to the clients know hosts file
5. New Keys between client & server, future communication uses these keys
6. Encrypted session established, ready for authenication 

### Hands-On 
```
localhost:1000
choose Ansible Terminal
ansible 
Password 

ssh ubuntu1 
exit 

# confirm known hosts file 
ansible@ubuntu-c:~$ ls 
ls -a 
cd .ssh
cat known_hosts >>> confirm two line 

ssh-keygen -H -F ubuntu1 >>> output first line
ping ubuntu1
ssh-keygen -H -F 172.23.0.3 >>> output second line

ansible
ssh-keygen
ssh-copy-id ansible@ubuntu1
ssh-copy-id ansible@ubuntu2

sudo apt update 
sudo apt install sshpass
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



sudo = super user do 
StrictHostKeyChecking=no = automatically accpet fingerprint

## check can reach all of our hosts
ansible -i,ubuntu1,ubuntu2,ubuntn3,centos1,centos2,centos3 all -m ping 

-i = inventory အကယ်က inventory file ကို ညွှန်းရတာ အခုကကျတော့ command ကနေ parameter pass တဲ့ပုံစံ

## eg.
ansible -i inventory [Target Server]
ansible -i inventory all -m "shell" -a "touch devopsclass"

-m = ansible module 

```

[Diagram](https://app.diagrams.net/?splash=0#G1KjRF56hUhLSi932xa8_llQ0nlQpL51_O#%7B%22pageId%22%3A%22N64jpYCgCpYWlQi05clx%22%7D)

## 7. Settting up the Course Repository


[Github diveintoansible](https://github.com/spurin/diveintoansible)

## Section 1 & 2 Quiz

Who Invented Ansible ?         
Answer: Michael DeHann

What year, was Ansible created in ?       
Answer: 2012

When, was Ansible Acquired , by Red Hat, Inc.        
Answer: 2016. ❌ 2015 ✅

Ansible is a single tool, True or False        
Answer: True ❌     
False ✅   
Ansible is a toolset, comprising of many tools, modules and is also an extendisble framework

Name some of the core components of Ansible          
Answer: ansible inventory, ansible module , ansible executable  ❌　   
✅      
Modules   
ansible executable   
ansible-playbook executable  
Inventories... many more 

What are the types of target, we can use with Ansible?   
Answer : ?     
✅  
Hosts   
Network Switches     
Containers    
Storage Arrays     
... many more  

When SSH is used, during the secured channel configuration, what algorithm is used to cread a symmetric key
Answer : Delffie Algorithm  ❌　     
Diffie-Hellman ✅   
an algorithm from 1970s that was publised by Whitfield Diffie and Martin Hellman

What command is used, to generage a public and private ssh keypair     
Answer: ssh-keygen ✅   

In what file, should a public key be added to on a remote users home directory, to configure truested access:   
Answer : authorized_keys ✅   

In which directory, within a users home directory, would you find a generated public and private ssh key, a known_hosts file and the authorized_keys file     
Answer: .ssh ✅   

What is the name of convenient ssh tool, we can use for copying our public key , to a target users authorized_keys file   
Answer: ssh-copy-id ✅   

What is the SSH option, for automaticallly accpetion, unknown Host key Fingerprints    
Answer: SSH..... ❌　  
StrictHostKeyChecking=no ✅   

On the command, "ansible -i,ubuntu1 -m ping" what does the -i and -m option represent    
Answer: -i = inventory , -m = module   
-i is used for the inventory   
-m is used for the module   