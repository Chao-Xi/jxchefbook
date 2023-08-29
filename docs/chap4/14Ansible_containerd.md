# **L14 Ansible Role批量部署Containerd服务**

**<MARK>kubernetes 1.24版本正式弃用docker，使用Containerd替换Docker为kubernetes集群的容器运行时。</MARK>**

使用ansible自动化运维工具在企业内网环境，**使用二进制进行部署containerd服务，并制作成role，要求具有幂等性，服务以system进行启动和管理，以变量的形式声明版本号，方便进行复用**。

## 1.创建Ansible Role

使用ansible-galaxy命令创建一个新的Ansible Role

```
$ cd /etc/ansible
$ ansible-galaxy init  roles/containerd
```

这将创建一个名为containerd的新Role，其中包含了一些默认的目录和文件。

## 2.声明环境变量

声明相关环境变量，不同的环境修改变量的值即可。

```
$ cat /etc/ansible/roles/containerd/vars/main.yml
---
# 声明containerd版本号
containerd_version: 1.7.4
# 声明临时文件存放位置
containerd_file_dir: /mnt/containerd-file
# 声明nerdctl客户端工具版本号
nerdctl_version: 1.5.0
```

## 3.下载离线安装包

```
$ wget -P /etc/ansible/roles/containerd/file/ \
http://rpmfind.net/linux/centos/8-stream/BaseOS/x86_64/os/Packages/libseccomp-2.5.1-1.el8.x86_64.rpm
$ wget -P /etc/ansible/roles/containerd/file/ \
https://github.com/containerd/containerd/releases/download/v1.7.4/containerd-1.7.4-linux-amd64.tar.gz
$ wget -P /etc/ansible/roles/containerd/file/ \
https://github.com/containerd/nerdctl/releases/download/v1.5.0/nerdctl-full-1.5.0-linux-amd64.tar.gz

# 创建nerdctl客户端管理工具的配置文件
$ cat<<EOF > /etc/ansible/roles/containerd/files/crictl.yaml
runtime-endpoint: unix:///run/containerd/containerd.sock
image-endpoint: unix:///var/run/containerd/containerd.sock
timeout: 10
debug: false
EOF
```

## 4.编写Ansible Playbook

在containerd Role的tasks目录下，创建一个名为main.yml的文件，编写Ansible Playbook，实现Containerd的二进制部署。以下是一个示例Playbook：

```
$ cat /etc/ansible/roles/containerd/tasks/main.yml

---
# 任务1：安装依赖包
- name: "Example Task"
  debug: 
    msg: "Containerd file directory is {{ containerd_file_dir }}"
- name: "[1] 安装依赖包libseccomp（rpm)"
  block:
  - name: "[1.1] 创建文件存放目录"
    file: 
      name: "{{ containerd_file_dir }}/nerdctl"
      state: directory
  - name: "[1.2] 分发libseccomp依赖包"
    copy:
      src: libseccomp-2.5.1-1.el8.x86_64.rpm
      dest: "{{ containerd_file_dir }}/libseccomp-2.5.1-1.el8.x86_64.rpm"
  - name: "[1.3] 安装libseccomp"
    yum: 
      name: "{{ containerd_file_dir }}/libseccomp-2.5.1-1.el8.x86_64.rpm"
      state: present

# 任务2： 二进制安装containerd
- name: "[2] 二进制安装Containerd服务"
  block:
  - name: "[2.1] 创建安装目录"
    file:
      name: /etc/containerd
      state: directory
  - name: "[2.2] 分发并解压二进制安装包"
    unarchive: 
      src: "cri-containerd-{{ containerd_version }}-linux-amd64.tar.gz"
      dest: "{{ containerd_file_dir }}"
    when:  ansible_architecture == "x86_64"
  - name: "[2.3] Copy可执行文件到PATH"
    copy:
      src: "{{ containerd_file_dir }}/usr/local/{{ item }}"
      dest: /usr/local/bin/
      remote_src: true
      mode: '0755'
    with_items:
      - 'bin/ctr'
      - 'bin/crictl'
      - 'bin/critest'
      - 'bin/ctd-decoder'
      - 'bin/containerd'
      - 'bin/containerd-stress'
      - 'bin/containerd-shim'
      - 'bin/containerd-shim-runc-v1'
      - 'bin/containerd-shim-runc-v2'
      - 'sbin/runc'
  - name: "[2.4] 创建默认配置文件"
    shell: containerd config default > /etc/containerd/config.toml
  - name: "[2.5] 修改驱动为systemd"
    shell: sed -i 's#SystemdCgroup = false#SystemdCgroup = true#g' /etc/containerd/config.toml
  - name: "[2.6] 创建服务启动文件"
    copy:
      src: "{{ containerd_file_dir }}/etc/systemd/system/containerd.service"
      dest: /usr/lib/systemd/system/containerd.service
      remote_src: true
  - name: "[2.7] 启动服务"
    service: 
      name: containerd
      state: started
      enabled: yes
  - name: "[2.8] 配置crictl客户端"
    copy: 
      src: crictl.yaml
      dest: /etc/crictl.yaml

# 部署客户端管理工具nerdctl
  - name: "[3] 部署客户端管理工具nerdctl"
    block:
    - name: "[3.1] 分发（二进制）安装包"
      unarchive:
        src: "nerdctl-full-{{ nerdctl_version }}-linux-amd64.tar.gz"
        dest: "{{ containerd_file_dir }}/nerdctl/"
    - name: "[3.2] 复制可执行文件到PATH"
      copy: 
        src: "{{ containerd_file_dir }}/nerdctl/bin/nerdctl"
        dest: /usr/local/bin/
        mode: '0755'
        remote_src: true
# 清理安装环境
- name: "[4.0] 清理安装环境"
  file: 
    name: "{{ containerd_file_dir }}"
    state: absent
```

## 5.使用role批量部署服务

使用Role：在其他Playbook或任务中，使用这个Role来安装Containerd。例如：

* 设置hosts

`$ cat  /etc/ansible/hosts`

```
[lidabai]
192.168.2.20
192.168.2.21
192.168.2.22

[lidabai:vars]
ansible_ssh_user=lidabai   #ssh用户（被管主机）
ansible_ssh_port=22     #ssh端口（被管主机）
ansible_ssh_pass=8888888  #ssh用户密码（被管主机）
```

```
$ cat <<EOF > k8s-install-containerd.yml
---
- name: “Install Containerd Server”
  hosts: lidabai
  become: true   #是否进行sudo提权
  roles:
    - containerd
EOF
$ ansible-plabook k8s-install-containerd.yml
```

这个Playbook会在webservers主机组上安装Containerd。**由于这个Role具有幂等性，因此可以在多次运行时保证正确性**。

通过以上步骤，就可以使用Ansible自动化运维工具二进制部署Containerd服务，并制作成具有幂等性的Role。需要注意的是，这个Playbook中的变量和路径可以根据实际情况进行修改。