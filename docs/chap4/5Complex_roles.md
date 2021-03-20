# **L5 Managing complex playbooks with roles and ansible galaxy**

> (Create `group:user` on linux machine)


## **1、Simple way to create `group:user`**

```
.
├── 3-1create_play.yml
├── tasks
│   └── create_user.yml
└── templates
    └── bashrc.j2
```

### **1-1 `3-1create_play.yml`**

```
---
- hosts: all
  vars:
    user_name: jacob
    user_state: present
    # user_state: absent
    ssh_key: ~/.ssh/id_rsa.pub
  tasks:
     - include_tasks: tasks/create_user.yml
```

* **Defining variables in a playbook**

```
hosts: all
  vars:
    user_name: jacob
    user_state: present
    # user_state: absent
    ssh_key: ~/.ssh/id_rsa.pub
```

* **Defining `tasks`**
* **`include_tasks` – Dynamically include a task list: - 	`include_tasks: tasks/create_user.yml`**

### 1-2 `tasks/create_user.yml`

```
---
# tasks file for user_create
- name: Create user on remote host
  user:
    name: '{{user_name}}'
    state: '{{user_state}}'
    remove: yes
    shell: /bin/bash
    groups: vagrant
    append: yes
  become: yes
  become_method: "sudo"

- name: Publish local ssh public key for remote login
  authorized_key:
    user: '{{user_name}}'
    state: '{{user_state}}'
    key: "{{ lookup('file', '{{ssh_key}}') }}"
  become: yes
  become_method: "sudo"

- name: Add bashrc to include host and user
  template:
    dest: '~{{user_name}}/.bashrc'
    src: templates/bashrc.j2
  become: yes
  become_method: "sudo"  
```

### **1-3 user – Manage user accounts**

Manage user accounts and user attributes.

* **name**: Name of the user to create, remove or modify.
* **state**: `[absent / present]` Whether the account should exist or not, taking action if the state is different from what is stated.
* **remove**: `When state is 'absent' and user exists` Whether or not to remove the user account
* **shell**: `When state is 'absent' and user exists`Whether or not to remove the user account
* **groups**: 	Optionally sets the user's primary group (takes a group name).
* **append**: `When state is 'present' and the user exists` Whether or not to append the user to groups

**Check Linux existing user and group**

```
$ groups
vagrant

$ awk -F: '{ print $1}' /etc/passwd
users...
```

**Errors:**

```
fatal: [githost]: FAILED! => {"changed": false, "cmd": "/sbin/useradd -G vagrant -s /bin/bash -m jacob", "msg": "[Errno 13] Permission denied", "rc": 13}

become: yes
become_method: "sudo"
```

### **1-4 `authorized_key` – Adds or removes an SSH authorized key**

```
authorized_key:
	user: '{{user_name}}'
	state: '{{user_state}}'
	key: "{{ lookup('file', '{{ssh_key}}') }}"
```

* key: The SSH public key(s), as a string or (since Ansible 1.9) url 
* `lookup`: `{{ lookup('file', '{{ssh_key}}') }}`


### **1-5 Jinja template**

```
template:
	dest: '~{{user_name}}/.bashrc'
	src: templates/bashrc.j2
```

**Run the play book**

```
ansible-playbook -i ../inventory.ini 3-1create_play.yml
```

## **2、Comprehenisve role to create `group:user` with `ansible galxy`**

```
ansible-galaxy init create_user
```

**galaxy** is a directory of roles. 

So when people find something really useful like let's deploy Apache or Engine X web servers, we can create a role for that and store it in galaxy and then share amongst not only our local peers but potentially the internet at large.

```
cp tasks/create_user.yml create_user/tasks/main.yml
cp templates/bashrc.j2 create_user/templates/
```


```
$ tree create_user/
create_user/
├── README.md
├── defaults
│   └── main.yml
├── files
├── handlers
│   └── main.yml
├── meta
│   └── main.yml
├── tasks
│   └── main.yml
├── templates
│   └── bashrc.j2
├── tests
│   ├── inventory
│   └── test.yml
└── vars
    └── main.yml

8 directories, 9 files
```

### **2-1 `3-1create_role.yml`**

```
---
- hosts: all
  tasks:
    - include_role:
          name: create_user
      vars:
        user_name: jacob
        # user_state: present
        user_state: absent
        ssh_key: ~/.ssh/id_rsa.pub
```

### **2-2 `tasks/main.yml`**

```
---
# tasks file for user_create
- name: Create user on remote host
  user:
    name: '{{user_name}}'
    state: '{{user_state}}'
    remove: yes
    shell: /bin/bash
    groups: vagrant
    append: yes
  become: yes
  become_method: "sudo"

- name: Publish local ssh public key for remote login
  authorized_key:
    user: '{{user_name}}'
    state: '{{user_state}}'
    key: "{{ lookup('file', '{{ssh_key}}') }}"
  become: yes
  become_method: "sudo"

- name: Add bashrc to include host and user
  template:
    dest: '~{{user_name}}/.bashrc'
    src: templates/bashrc.j2
  become: yes
  become_method: "sudo"  
```

### **2-3 `templates/bashrc.j2`**

```
export PS1='{{inventory_hoastname}}:{{user_name}} $'
```

```
ansible-playbook -i ../inventory.ini 3-2create_role.yml
```

## **3、Variables in roles and variable precedence**

### **3-1 `3-1create_role.yml` Delete `user_state` from tasks**

```
---
- hosts: all
  tasks:
    - include_role:
          name: create_user
      vars:
        user_name: jacob
        # user_state: present
        # user_state: absent
        ssh_key: ~/.ssh/id_rsa.pub
```

### **3-2 Add this is variable in `default/main.yml`**

```
---
# defaults file for create_user
 user_state: present
```

```
ansible-playbook -i ../inventory.ini 3-2create_role.yml
```

### **3-3 Add this is variable can be also passed in `default/main.yml`**


```
ansible-playbook -i ../inventory.ini 3-2create_role.yml -e user_state= absent
```

**`-e user_state= absent`**

## **4、Role-based templates**

**Add more variables `default/main.yml`**

```
---
# defaults file for create_user
user_state: present
user_name: default
``` 

### **4-1 Dont forget document newly added variables： `READEME.md`**

```
Role Variables
--------------

# Define the user you would like to create
user_name: default

# Define the user state present or absent
user_state: present
```


## **5、Documenting your role for reuse**

### **5-1 `READEME.md`**

```
Role Variables
--------------

# Define the user you would like to create
user_name: default

# Define the user state present or absent
user_state: present

# Define the path to the ssh public key
ssh_key: ~/.ssh/id_rsa.pub


Dependencies
------------

None

Example Playbook
----------------

---
- hosts: all
  tasks:
    - include_role:
          name: create_user
      vars:
        user_name: jacob
        ssh_key: ~/.ssh/id_rsa.pub

License
-------

MIT

Author Information
------------------

...
```

### **5-2 `meta/main.yml`**

```
galaxy_info:
  author: jaocb
  description: Create user with ssh authentication
  company: your company (optional)

 license: MIT 

 min_ansible_version: 2.4
 
 galaxy_tags: [user,admin]

```

## **6、Pushing a role to Galaxy**

* Step one push projects into github
* `ansible-galaxy login` with github token(username and password)
* `ansible-galaxy import chao-xi(repo_owner) create_user(project_name)`


## **7、Finding roles via Ansible Galaxy**

### **7-1 Search**

```
ansible-galaxy search create_user

Found 1816 roles matching your search. Showing first 1000.

 Name                                                      Description
 ----                                                      -----------
 0utsider.ansible_zabbix_agent                             Installing and maintaining zabbix-agent for RedHat/Debian/Ub
 0x5a17ed.ansible_role_netbox                              Installs and configures NetBox, a DCIM suite, in a productio
 1davidmichael.ansible-role-nginx                          Nginx installation for Linux, FreeBSD and OpenBSD.
 1it.users                                                 Ansible role 
 ...
```


### **7-2 download**

```
$ ansible-galaxy search create_user | grep docker
 alexinthesky.docker                                       Install Docker and Docker Compose.
 

$ ansible-galaxy install alexinthesky.docker -p ./
- downloading role 'docker', owned by alexinthesky
- downloading role from https://github.com/alexinthesky/ansible-docker/archive/v0.1.6.tar.gz
- extracting alexinthesky.docker to /Users/i515190/Devops_sap/ansible/code/task3/alexinthesky.docker
- alexinthesky.docker (v0.1.6) was installed successfully
```

```
$  tree alexinthesky.docker
alexinthesky.docker
├── CHANGES.md
├── LICENSE
├── README.md
├── defaults
│   └── main.yml
├── handlers
│   └── main.yml
├── meta
│   └── main.yml
├── tasks
│   └── main.yml
├── templates
│   └── etc
│       └── systemd
│           └── system
│               └── docker.service.j2
└── tests
    ├── inventory
    └── main.yml

9 directories, 10 files
```

**Centralizing roles with `roles_path`**

```
$ vi /etc/ansible/ansible.cfg

[defaults]
role_path=/etc/ansible/roles
```


