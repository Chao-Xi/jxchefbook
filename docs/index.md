# 手摸手 Chef & Ansible 技术与实战教程

> Started at March. 2021 By Jacob Xi 

![Alt Image Text](images/indx1_0.png "Body image")

## **内容简介**

本书是本人的“手摸手战术教程”系列的第四本，历经两个月的时间，终于在还没有开花🌸，天天下雨☔️，冷的要死的三月🥶完工了，感谢家人，朋友，同事们的支持与理解，也感谢所在team同事们的帮助与指导。# 最近看了**“大而不倒”，“发条女孩”，“你一生的故事”**，*Only the book can calm me down*。

### **Previous on 手摸手**

> [手摸手 Jenkins 战术教程 (大师版）](https://chao-xi.github.io/jxjenkinsbook/)
> 
> [手摸手 Elasticsearch7 技术与实战教程](https://chao-xi.github.io/jxes7book/)
> 
>  [手摸手 Redis 技术与实战教程](https://chao-xi.github.io/jxredisbook/)


## **Ansible vs Chef**

Ansible calls its configuration files “playbooks”, while Chef calls them “cookbooks”.

End of story. We’re done.

### **Setting it Up**: 

**Chef operates with a master-client architecture. The server part runs on the master machine, while the client portion runs as an agent on every client machine**. Chef also has an extra component named “workstation” that stores all of the configurations that are tested then pushed to the central server.

**Ansible only uses a master running on the server machine, but no agents running on the client machine.** It uses an SSH connection to log in to the client systems or the nodes you want to configure, and the client machine VM has no need for special setup.


### **Source of Truth:**

Source of Truth is defined as the authoritative configuration of a given set of systems or system. Ansible’s source of truth comes from its deployed playbooks, which are perfect as source control systems, while

Chef relies on its own server as the source of truth, and those servers require uploaded cookbooks, which means making sure the latter are consistent and identical.

### **Managing the Tools:**

With Chef, the client pulls configurations from the server. The configurations are in **Ruby DSL**, so you need to have programming skills in order to manage those configurations.

**Ansible uses YAML (Yet Another Markup Language) to manage configurations**, a language that’s similar to English, and the server pushes the configurations to the individual nodes.


### **本书主要内容**

本书共四章，主要介绍Chef和Ansible的使用语法，常用命令以及生产案例。 

Chef 主要介绍了常用的Chef语法，cookbook的创建以及apply, Kitchen管理虚拟机，Ohai属性，Recipes的撰写，属性，databag, 角色，环境的获取，chef-zero以及chef-solot的使用，自动化测试Recipes，Knife的使用技巧以及LAMP的Chef CookBook

Ansible 主要介绍了常用的Ansible语法，playbook的创建，Task的创建，Jinja赋值的Variable, 角色的创建， ansible-valut管理密码，ansible对网络的管理，以及如何更好的实现幂等性

## Comment ca va? C'est Moi

Hello, this is me, Jacob. Currently, I'm working as DevOps and Cloud Engineer in SAP, and I'm the certified AWS Solution Architect and Certified Azure Administrator, Kubernetes Specialist, Jenkins CI/CD and ElasticStack enthusiast. 

I was working as Backend Engineer in New York City and achieved my CS master degree in SIT, America. Believe it or not, I'll keep writing, more and more books will come out at such dramatic and unprecedented 2021. 

If you have anything want to talk to me directly, you can reach out for via email xichao2015@outlook.com。


Salute, c'est moi, Jacob. Actuellement, je travaille en tant qu'ingénieur DevOps et Cloud dans SAP, et je suis architecte de solution AWS certifié et administrateur Azure certifié, spécialiste Kubernetes et passionné de CI/CD.

Je travaillais en tant qu'ingénieur backend à New York et j'ai obtenu mon master CS à SIT, en Amérique. Croyez-le ou non, je continuerai à écrire, de plus en plus de livres sortiront cette année.

## 目录大纲

* **第一章 Learning Chef**
	* [第一节 Chef 介绍和安装](https://chao-xi.github.io/jxchefbook/chap1/chef_tutorial1/)
	* [第二节 Ruby和Chef语法](https://chao-xi.github.io/jxchefbook/chap1/chef_tutorial2/)
	* [第三节 如何写 Chef 配方 & chef-apply](https://chao-xi.github.io/jxchefbook/chap1/chef_tutorial3/)
	* [第四节 用Test Kitchen管理沙盒测试环境](https://chao-xi.github.io/jxchefbook/chap1/chef_tutorial4/)
	* [第五节 用 Chef 户端管理节点](https://chao-xi.github.io/jxchefbook/chap1/chef_tutorial5/)
	* [第六节 撰写和使用菜谱](https://chao-xi.github.io/jxchefbook/chap1/chef_tutorial6/)
	* [第七节 属性](https://chao-xi.github.io/jxchefbook/chap1/chef_tutorial7_attributes/)
	* [第八节 Chef 服务器同时管理多个节点](https://chao-xi.github.io/jxchefbook/chap1/chef_tutorial8/)
	* [第九节 社区以及Chef-Client菜谱](https://chao-xi.github.io/jxchefbook/chap1/chef_tutorial9/)
	* [第十节 Chef zero](https://chao-xi.github.io/jxchefbook/chap1/chef_tutorial10_zero/)
	* [第十一节 数概包Databag](https://chao-xi.github.io/jxchefbook/chap1/chef_tutorial11_databag/)
	* [第十二节 角色](https://chao-xi.github.io/jxchefbook/chap1/chef_tutorial12/)
	* [第十三节 环境](https://chao-xi.github.io/jxchefbook/chap1/chef_tutorial13_env/)
	* [第十四节 测试](https://chao-xi.github.io/jxchefbook/chap1/chef_tutorial14_test/)
	* [第十五节 词汇表](https://chao-xi.github.io/jxchefbook/chap1/chef_tutorial15_term/)
* **第二章 Chef Opt & Adv**
	* [第一节 Chef Roles](https://chao-xi.github.io/jxchefbook/chap2/chef_adv4/)
	* [第二节 Cookbook Versioning](https://chao-xi.github.io/jxchefbook/chap2/chef_adv5/)
	* [第三节 How to Perform Chef Knife SSL Check and Fetch to Verify Certificate](https://chao-xi.github.io/jxchefbook/chap2/chef_adv1/)
	* [第四节 12 Chef Knife Cookbook Command Examples](https://chao-xi.github.io/jxchefbook/chap2/chef_adv2/) 
	* [第五节 knife search](https://chao-xi.github.io/jxchefbook/chap2/chef_op1_kinfe_search/)
	* [第六节 Chef Quick CheatSheet](https://chao-xi.github.io/jxchefbook/chap2/chef_adv3/)
* **第三章 Chef Cookbook Sample LAMP**
	* [第一节 Creating My First Chef Cookbook](https://chao-xi.github.io/jxchefbook/chap3/chef_basic5/)
	* [第二节 Creating LAMP Chef Cookbook Analysis](https://chao-xi.github.io/jxchefbook/chap3/chef_basic6/) 
* **第四章  Learning Ansible**
	* [L1 Ansible Introduction](https://chao-xi.github.io/jxchefbook/chap4/1ansible_intro/) 
	* [L2 Ansible Install(MacOs) and Environment Setup](https://chao-xi.github.io/jxchefbook/chap4/2ansible_over_install/)
	* [L3 Task Execution management](https://chao-xi.github.io/jxchefbook/chap4/3play_task/)
	* [L4 Ansible Variable Management](https://chao-xi.github.io/jxchefbook/chap4/4Ansible_variables/)
	* [L5 Managing complex playbooks with roles and ansible galaxy](https://chao-xi.github.io/jxchefbook/chap4/5Complex_roles/)
	* [L6 Working with Secrets](https://chao-xi.github.io/jxchefbook/chap4/6Secret_management/)
	* [L7 Network Management with Ansible](https://chao-xi.github.io/jxchefbook/chap4/7Network_management/)
	* [L8 Idempotentence with Ansible play](https://chao-xi.github.io/jxchefbook/chap4/8idempotentence/)
	* [L9 Ansible Interview](https://chao-xi.github.io/jxchefbook/chap4/9Ansible_interview/)
	* [L10 Ansible 自动化运维工具PPT](https://chao-xi.github.io/jxchefbook/chap4/10Ansible_learn_ppt/)
	* [L11 任务中心之Ansible基础篇](https://chao-xi.github.io/jxchefbook/chap4/11Ansible_task1/)
	* [L12 任务中心之Ansible进阶篇](https://chao-xi.github.io/jxchefbook/chap4/12Ansible_task2/)
	* [L13 Ansible 面试知识点](https://chao-xi.github.io/jxchefbook/chap4/13Ansible_int/)
* **第五章 Certified Terraform Associate**
	* [HashiCorp Certified Terraform Associate Learning Path](https://chao-xi.github.io/jxchefbook/chap5/1hashicorp_certified_terraform/)
	* [Terraform Cheat Sheet](https://chao-xi.github.io/jxchefbook/chap5/2terraform_cs/) 	 	


## To be continue

本人将带来手摸手战术教程更多的内容和文章， 接下来的将在Datatase, 开发性能&Linux, Azure900, Azure103, AWS Solution Arcitect, AWS Big Data Speciality, Istio, Python带来更多更全面的电子书，敬请期待。

![Alt Image Text](images/indx1_1.jpeg "Body image")

