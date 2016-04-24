#### pshandansible
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

