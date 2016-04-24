#### pshandansible
#####creating environment
```
Vagrant.configure(1) do |config|
    config.vm.define "host1" do |host1|
        host1.vm.box="ubuntu/trusty64"
        host1.vm.hostname="host1"
        host1.vm.network "private_network",ip: "192.168.11.11"
    end
    config.vm.define "host2" do |host2|
        host1.vm.box="ubuntu/trusty64"
        host1.vm.hostname="host2"
        host1.vm.network "private_network",ip: "192.168.11.22"
    end
    config.vm.define "host3" do |host3|
        host1.vm.box="nrel/CentOS-6.5"
        host1.vm.hostname="host3"
        host1.vm.network "private_network",ip: "192.168.11.33"
        host1.vm.network "forwarded_port",guest:80,host:8080
    end
end
```
then
```
vagrant up
vboxmanage list runningvms
```
connect:
```
vagrant ssh host1
```
#####install ansible
on yum, build source code:
```
sudo yum install python-setuptools
sudo easy_install pip
sudo yum install python-devel
sudo pip install ansible
```
######Testing lab (using naive way)
```
ansible 192.168.11.11 -i inventory -u vagrant -m ping -k    (-k interactive prompt)
```
by default, no command = command command
```
ansible 192.168.11.11 -i inventory -u vagrant -a command "/sbin/root"
ansible 192.168.11.11 -i inventory -u vagrant -a "/sbin/root"
```
difference between command and shell:  
command is running an executable inside of python, shell can use env variable
#####inventory
######configuration
ansible.cfg
```
[defaults]
host_key_checking=False
```
```
vim ~/.ssh/known_hosts
```
######working with python3-based system
set inventory file
```
ansible_python_interpreter=/usr/bin/python2.7
```
#####module
######funda
check docs
```
ansible-doc -l
ansible-doc -s name  //get playbook snippet
ansible-doc name //get specific module usage
```
######using module docs
g1:g2 //g1 or g2   g1:&g2 //intersection  
g1:&g2:!g3  // hosts defined in g1 and g2 but not in g3
```
ansible group1:group2 -i inventory -m yum -a "name=httpd state=present" --sudo
```
######setup module
```
ansible gp -i inventory -m setup -a "filter=ansible_eth*"   //eth0,eth1,eth2
```
#####playbook
######logic
```
tasks:
- shell: /usr/bin/whoami
  register: username
- file: path=/home/myfile owner={{username}}
- debug: var=username
vars_prompt:
- name: a
  propmt:
```
using when
```
tasks:
- command: ls /path    //may not exist!
  register: res
  ignore_errors: yes
- debug: msg="fail"
  when: res|failed            // three status: success, failed, skipped
```


#####role 
######role basic
```
root/role1
    /role2
      /site.yml
      /defaults
        /main.yml
      /files
      /vars
      /meta
      /templates
      /handlers
      /tasks
```
######creating roles.
example. in tasks folder, we create 3 files:
```
main.yml
syslog.yml
snmp.yml
```
main.yml
```
---
- include: snmp.yml
- include: syslog.yml
```
syslog.yml
```

```
snpm.yml
```
---
- name: install net-snmp
  yum: name=net-snmp state=present
- name: deploy config
  template: src=snmpd.conf.js dest=/etc/snpm/snpmd.conf
  notify: start
```
inventory:
```
user1 ansible_ssh_host= ansible_ssh_user= ansible_ssh_pass= username=
user2 ansible_ssh_host= ansible_ssh_user= ansible_ssh_pass=
[a]
user1
[b]
user2
[c:children]
a
b
[c:vars]
ansible_ssh_user= 
ansible_ssh_pass=
```
site.yml (note the colon notation)
```
hosts:a:b
```
######ansible-galaxy
```
ansible-galaxy install geerlingguy.git
```
```
ls /etc/ansible/roles/geerlingguy.git
```

