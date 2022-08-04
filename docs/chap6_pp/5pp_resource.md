# **5 Puppet 资源**

资源是用于设计和构建任何特定基础设施或机器的 Puppet 的关键基本单元之一。它们主要用于建模和维护系统配置。 Puppet 具有多种类型的资源，可用于定义系统架构，或者用户可以利用这些资源来构建和定义新资源。

清单文件或任何其他文件中的 Puppet 代码块称为资源声明。代码块是用一种称为 Declarative Modelling Language (DML) 的语言编写的。以下是它的外观示例。

```
user { 'vipin': 
   ensure => present, 
   uid    => '552', 
   shell  => '/bin/bash', 
   home   => '/home/vipin', 
}
```

在 Puppet 中，任何特定资源类型的资源声明都是在代码块中完成的。在以下示例中，用户主要由四个预定义参数组成。

* Resource Type − 在上面的代码片段中，它是用户
* Resource Parameter − 在上面的代码片段中，它是 Vipin。.
* Attributes − 在上面的代码片段中，是 ensure, uid, shell, home
* Values − 这些是对应于每个属性的值。


每种资源类型都有自己定义定义和参数的方式，用户有权选择他希望资源的外观。


### **资源类型**

Puppet 中有不同类型的资源可用，它们有自己的功能方式。可以使用`describe`命令和`-list`选项查看这些资源类型。

```
[root@puppetmaster ~]# puppet describe --list
These are the types known to puppet:
augeas - Apply a change or an array of changes to the ...
computer - Computer object management using DirectorySer ...
cron - Installs and manages cron jobs
exec - Executes external commands
file - Manages files, including their content, owner ...
filebucket - A repository for storing and retrieving file ...
group - Manage groups
host - Installs and manages host entries
interface - This represents a router or switch interface
k5login - Manage the ‘.k5login’ file for a user
macauthorization - Manage the Mac OS X authorization database
mailalias - .. no documentation ..
maillist - Manage email lists
mcx - MCX object management using DirectoryService ...
mount - Manages mounted filesystems, including puttin ...
nagios_command - The Nagios type command
nagios_contact - The Nagios type contact
nagios_contactgroup - The Nagios type contactgroup
nagios_host - The Nagios type host
nagios_hostdependency - The Nagios type hostdependency
nagios_hostescalation - The Nagios type hostescalation
nagios_hostextinfo - The Nagios type hostextinfo
nagios_hostgroup - The Nagios type hostgroup

nagios_service - The Nagios type service
nagios_servicedependency - The Nagios type servicedependency
nagios_serviceescalation - The Nagios type serviceescalation
nagios_serviceextinfo - The Nagios type serviceextinfo
nagios_servicegroup - The Nagios type servicegroup
nagios_timeperiod - The Nagios type timeperiod
notify - .. no documentation ..
package - Manage packages
resources - This is a metatype that can manage other reso ...
router - .. no documentation ..
schedule - Define schedules for Puppet
scheduled_task - Installs and manages Windows Scheduled Tasks
selboolean - Manages SELinux booleans on systems with SELi ...
service - Manage running services
ssh_authorized_key - Manages SSH authorized keys
sshkey - Installs and manages ssh host keys
stage - A resource type for creating new run stages
tidy - Remove unwanted files based on specific crite ...
user - Manage users
vlan - .. no documentation ..
whit - Whits are internal artifacts of Puppet's curr ...
yumrepo - The client-side description of a yum reposito ...
zfs - Manage zfs
zone - Manages Solaris zones
zpool - Manage zpools
```

### **资源标题**


在上面的代码片段中，我们的资源标题为 vipin，它对于在同一代码文件中使用的每个资源都是唯一的。**这是此用户资源类型的唯一标题。我们不能拥有同名的资源，因为它会导致冲突。**


资源命令可用于查看所有使用类型用户的资源列表。

```
[root@puppetmaster ~]# puppet resource user 
user { 'abrt': 
   ensure           => 'present', 
   gid              => '173', 
   home             => '/etc/abrt', 
   password         => '!!', 
   password_max_age => '-1', 
   password_min_age => '-1', 
   shell            => '/sbin/nologin', 
   uid              => '173', 
} 

user { 'admin': 
   ensure           => 'present', 
   comment          => 'admin', 
   gid              => '444', 
   groups           => ['sys', 'admin'], 
   home             => '/var/admin', 
   password         => '*', 
   password_max_age => '99999', 
   password_min_age => '0', 
   shell            => '/sbin/nologin', 
   uid              => '55', 
} 

user { 'tomcat': 
   ensure           => 'present', 
   comment          => 'tomcat', 
   gid              => '100', 
   home             => '/var/www', 
   password         => '!!', 
   password_max_age => '-1', 
   password_min_age => '-1', 
   shell            => '/sbin/nologin', 
   uid              => '100', 
} 
```

### **列出特定用户的资源**

```
[root@puppetmaster ~]# puppet resource user tomcat 
user { 'apache': 
   ensure           => 'present', 
   comment          => 'tomcat', 
   gid              => '100', 
   home             => '/var/www', 
   password         => '!!', 
   password_max_age => '-1', 
   password_min_age => '-1', 
   shell            => '/sbin/nologin', 
   uid              => '100’, 
}
```

### **属性和值**

任何资源的主体都是由一组属性值对组成的。在这里可以指定给定资源属性的值。每种资源类型都有自己的一组属性，可以使用键值对进行配置。

描述可用于获取有关特定资源属性的更多详细信息的子命令。在以下示例中，我们拥有有关用户资源的详细信息及其所有可配置属性。

```
root@puppetmaster ~]# puppet describe user 
user 
==== 
Manage users.  This type is mostly built to manage system users, 
so it is lacking some features useful for managing normal users. 

This resource type uses the prescribed native tools for creating groups 
and generally uses POSIX APIs for retrieving information about them.
It does not directly modify ‘/etc/passwd’ or anything. 

**Autorequires:** If Puppet is managing the user's primary group 
(as provided in the ‘gid’ attribute), 
the user resource will autorequire that group. 
If Puppet is managing any role accounts corresponding to the user's roles, 
the user resource will autorequire those role accounts.  

Parameters 
---------- 
- **allowdupe** 
   Whether to allow duplicate UIDs. Defaults to ‘false’. 
   Valid values are ‘true’, ‘false’, ‘yes’, ‘no’.  

- **attribute_membership** 
   Whether specified attribute value pairs should be treated as the 
   **complete list** (‘inclusive’) or the **minimum list** (‘minimum’) of 
   attribute/value pairs for the user. Defaults to ‘minimum’. 
   Valid values are ‘inclusive’, ‘minimum’.  

- **auths** 
   The auths the user has.  Multiple auths should be 
   specified as an array. 
   Requires features manages_solaris_rbac.  

- **comment** 
   A description of the user.  Generally the user's full name.  

- **ensure** 
   The basic state that the object should be in. 
   Valid values are ‘present’, ‘absent’, ‘role’.  

- **expiry**
   The expiry date for this user. Must be provided in 
   a zero-padded YYYY-MM-DD format --- e.g. 2010-02-19. 
   If you want to make sure the user account does never 
   expire, you can pass the special value ‘absent’. 
   Valid values are ‘absent’. Values can match ‘/^\d{4}-\d{2}-\d{2}$/’. 
   Requires features manages_expiry.  

- **forcelocal** 
   Forces the mangement of local accounts when accounts are also 
   being managed by some other NSS  

- **gid** 
   The user's primary group. Can be specified numerically or by name. 
   This attribute is not supported on Windows systems; use the ‘groups’ 
   attribute instead. (On Windows, designating a primary group is only 
   meaningful for domain accounts, which Puppet does not currently manage.)  

- **groups** 
   The groups to which the user belongs. The primary group should 
   not be listed, and groups should be identified by name rather than by 
   GID.  Multiple groups should be specified as an array.  

- **home** 
   The home directory of the user.  The directory must be created 
   separately and is not currently checked for existence.  

- **ia_load_module** 
   The name of the I&A module to use to manage this user. 
   Requires features manages_aix_lam.  

- **iterations** 
   This is the number of iterations of a chained computation of the 
   password hash (http://en.wikipedia.org/wiki/PBKDF2).  This parameter 
   is used in OS X. This field is required for managing passwords on OS X 
   >= 10.8. 
   Requires features manages_password_salt. 

- **key_membership**  

- **managehome** 
   Whether to manage the home directory when managing the user. 
   This will create the home directory when ‘ensure => present’, and 
   delete the home directory when ‘ensure => absent’. Defaults to ‘false’. 
   Valid values are ‘true’, ‘false’, ‘yes’, ‘no’.  

- **membership** 
   Whether specified groups should be considered the **complete list** 
   (‘inclusive’) or the **minimum list** (‘minimum’) of groups to which 
   the user belongs. Defaults to ‘minimum’. 
   Valid values are ‘inclusive’, ‘minimum’.  

- **name** 
   The user name. While naming limitations vary by operating system, 
   it is advisable to restrict names to the lowest common denominator, 
   which is a maximum of 8 characters beginning with a letter. 
   Note that Puppet considers user names to be case-sensitive, regardless 
   of the platform's own rules; be sure to always use the same case when 
   referring to a given user.  

- **password** 
   The user's password, in whatever encrypted format the local 
   system requires. 
   * Most modern Unix-like systems use salted SHA1 password hashes. You can use 
      Puppet's built-in ‘sha1’ function to generate a hash from a password. 
   * Mac OS X 10.5 and 10.6 also use salted SHA1 hashes.  

Windows API 
   for setting the password hash. 
   [stdlib]: https://github.com/puppetlabs/puppetlabs-stdlib/ 
   Be sure to enclose any value that includes a dollar sign ($) in single 
   quotes (') to avoid accidental variable interpolation. 
   Requires features manages_passwords.  

- **password_max_age** 
   The maximum number of days a password may be used before it must be changed. 
   Requires features manages_password_age.  

- **password_min_age** 
   The minimum number of days a password must be used before it may be changed. 
   Requires features manages_password_age.  

- **profile_membership** 
   Whether specified roles should be treated as the **complete list** 
   (‘inclusive’) or the **minimum list** (‘minimum’) of roles 
   of which the user is a member. Defaults to ‘minimum’. 
   Valid values are ‘inclusive’, ‘minimum’.  

- **profiles** 
   The profiles the user has.  Multiple profiles should be 
   specified as an array. 
   Requires features manages_solaris_rbac.  

- **project** 
   The name of the project associated with a user. 
   Requires features manages_solaris_rbac.  

- **uid** 
   The user ID; must be specified numerically. If no user ID is 
   specified when creating a new user, then one will be chosen 
   automatically. This will likely result in the same user having 
   different UIDs on different systems, which is not recommended. This is 
   especially noteworthy when managing the same user on both Darwin and 
   other platforms, since Puppet does UID generation on Darwin, but 
   the underlying tools do so on other platforms. 
   On Windows, this property is read-only and will return the user's 
   security identifier (SID). 
```

## **Puppet 资源抽象层**


在 Puppet 中，资源抽象层 (RAL) 可以被视为整个基础架构和 Puppet 设置工作的核心概念化模型。在 RAL 中，每个字母都有自己的重要含义，定义如下。

### **Resource [R]**

一个资源可以被认为是用于对 Puppet 中的任何配置进行建模的所有资源。它们基本上是内置资源，默认情况下存在于 Puppet 中。它们可以被认为是属于预定义资源类型的一组资源。**它们类似于任何其他编程语言中的 OOP 概念，其中对象是类的实例**。在 Puppet 中，它的资源是一种资源类型的实例。

### **Abstraction [A]**

抽象可以被认为是一个关键特性，其中资源是独立于目标操作系统定义的。换句话说，在编写任何清单文件时，用户不必担心目标机器或存在于该特定机器上的操作系统。抽象地说，资源提供了关于 Puppet 代理上需要存在什么的足够信息。

Puppet 将负责幕后发生的所有功能或Magic。无论资源和操作系统如何，Puppet 都会负责在目标机器上实施配置，用户无需担心 Puppet 在幕后是如何工作的。

**在抽象中，Puppet 将资源与其实现分离。此平台特定配置来自提供者。我们可以使用多个子命令及其提供程序。**

### **Layer [L]**

可以根据资源集合定义整个机器设置和配置，并且可以通过 Puppet 的 CLI 界面查看和管理。


### **用户资源类型示例**

```
[root@puppetmaster ~]# puppet describe user --providers 
user 
==== 
Manage users.
This type is mostly built to manage systemusers, 
so it is lacking some features useful for managing normalusers. 
This resource type uses the prescribed native tools for 
creating groups and generally uses POSIX APIs for retrieving informationabout them.
It does not directly modify '/etc/passwd' or anything. 

- **comment** 
   A description of the user.  Generally the user's full name.  

- **ensure** 
   The basic state that the object should be in. 
   Valid values are 'present', 'absent', 'role'.  

- **expiry** 
   The expiry date for this user. 
   Must be provided in a zero-padded YYYY-MM-DD format --- e.g. 2010-02-19. 
   If you want to make sure the user account does never expire, 
   you can pass the special value 'absent'. 
   Valid values are 'absent'. 
   Values can match '/^\d{4}-\d{2}-\d{2}$/'. 
   Requires features manages_expiry.  

- **forcelocal** 
   Forces the management of local accounts when accounts are also 
   being managed by some other NSS 
   Valid values are 'true', 'false', 'yes', 'no'. 
   Requires features libuser.  

- **gid** 
   The user's primary group.  Can be specified numerically or by name. 
   This attribute is not supported on Windows systems; use the ‘groups’ 
   attribute instead. (On Windows, designating a primary group is only 
   meaningful for domain accounts, which Puppet does not currently manage.)  

- **groups** 
   The groups to which the user belongs. The primary group should 
   not be listed, and groups should be identified by name rather than by 
   GID. Multiple groups should be specified as an array.  

- **home** 
   The home directory of the user.  The directory must be created 
   separately and is not currently checked for existence.  

- **ia_load_module**
   The name of the I&A module to use to manage this user. 
   Requires features manages_aix_lam.  

- **iterations** 
   This is the number of iterations of a chained computation of the 
   password hash (http://en.wikipedia.org/wiki/PBKDF2).  
   This parameter is used in OS X. 
   This field is required for managing passwords on OS X >= 10.8.  

- **key_membership** 
   Whether specified key/value pairs should be considered the 
   **complete list** ('inclusive') or the **minimum list** ('minimum') of 
   the user's attributes. Defaults to 'minimum'. 
   Valid values are 'inclusive', 'minimum'.  

- **keys** 
   Specify user attributes in an array of key = value pairs. 
   Requires features manages_solaris_rbac.  

- **managehome** 
   Whether to manage the home directory when managing the user. 
   This will create the home directory when 'ensure => present', and 
   delete the home directory when ‘ensure => absent’. Defaults to ‘false’. 
   Valid values are ‘true’, ‘false’, ‘yes’, ‘no’.  

- **membership** 
   Whether specified groups should be considered the **complete list** 
   (‘inclusive’) or the **minimum list** (‘minimum’) of groups to which 
   the user belongs. Defaults to ‘minimum’. 
   Valid values are ‘inclusive’, ‘minimum’. 

- **name** 
   The user name. While naming limitations vary by operating system, 
   it is advisable to restrict names to the lowest common denominator.  

- **password** 
   The user's password, in whatever encrypted format the local system requires. 
   * Most modern Unix-like systems use salted SHA1 password hashes. You can use 
      Puppet's built-in ‘sha1’ function to generate a hash from a password. 
   * Mac OS X 10.5 and 10.6 also use salted SHA1 hashes. 
   * Mac OS X 10.7 (Lion) uses salted SHA512 hashes. 
   The Puppet Labs [stdlib][] module contains a ‘str2saltedsha512’ 
   function which can generate password hashes for Lion. 
   * Mac OS X 10.8 and higher use salted SHA512 PBKDF2 hashes. 
   When managing passwords on these systems the salt and iterations properties 
   need to be specified as well as the password. 
   [stdlib]: https://github.com/puppetlabs/puppetlabs-stdlib/ 
   Be sure to enclose any value that includes a dollar sign ($) in single 
   quotes (') to avoid accidental variable interpolation. 
   Requires features manages_passwords.  

- **password_max_age** 
   The maximum number of days a password may be used before it must be changed. 
Requires features manages_password_age.  

- **password_min_age** 
   The minimum number of days a password must be used before it may be changed. 
Requires features manages_password_age.  

- **profile_membership** 
   Whether specified roles should be treated as the **complete list** 
   (‘inclusive’) or the **minimum list** (‘minimum’) of roles 
   of which the user is a member. Defaults to ‘minimum’. 
   Valid values are ‘inclusive’, ‘minimum’. 

- **profiles** 
   The profiles the user has.  Multiple profiles should be 
   specified as an array. 
Requires features manages_solaris_rbac.  

- **project** 
   The name of the project associated with a user. 
   Requires features manages_solaris_rbac.  

- **purge_ssh_keys** 
   Purge ssh keys authorized for the user 
   if they are not managed via ssh_authorized_keys. 
   When true, looks for keys in .ssh/authorized_keys in the user's home directory. 
   Possible values are true, false, or an array of 
   paths to file to search for authorized keys. 
   If a path starts with ~ or %h, this token is replaced with the user's home directory. 
   Valid values are ‘true’, ‘false’.  

- **role_membership** 
   Whether specified roles should be considered the **complete list** 
   (‘inclusive’) or the **minimum list** (‘minimum’) of roles the user has. 
   Defaults to ‘minimum’. 
Valid values are ‘inclusive’, ‘minimum’.  

- **roles** 
   The roles the user has.  Multiple roles should be 
   specified as an array. 
Requires features manages_solaris_rbac.  

- **salt** 
   This is the 32 byte salt used to generate the PBKDF2 password used in 
   OS X. This field is required for managing passwords on OS X >= 10.8. 
   Requires features manages_password_salt. 

- **shell** 
   The user's login shell.  The shell must exist and be 
   executable. 
   This attribute cannot be managed on Windows systems. 
   Requires features manages_shell. 

- **system** 
   Whether the user is a system user, according to the OS's criteria; 
   on most platforms, a UID less than or equal to 500 indicates a system 
   user. Defaults to ‘false’. 
   Valid values are ‘true’, ‘false’, ‘yes’, ‘no’.  

- **uid** 
   The user ID; must be specified numerically. If no user ID is 
   specified when creating a new user, then one will be chosen 
   automatically. This will likely result in the same user having 
   different UIDs on different systems, which is not recommended. 
   This is especially noteworthy when managing the same user on both Darwin and 
   other platforms, since Puppet does UID generation on Darwin, but 
   the underlying tools do so on other platforms. 
   On Windows, this property is read-only and will return the user's 
   security identifier (SID).  

Providers 
--------- 

- **aix** 
   User management for AIX. 
   * Required binaries: '/bin/chpasswd', '/usr/bin/chuser', 
   '/usr/bin/mkuser', '/usr/sbin/lsgroup', '/usr/sbin/lsuser', 
   '/usr/sbin/rmuser'. 
   * Default for ‘operatingsystem’ == ‘aix’. 
   * Supported features: ‘manages_aix_lam’, ‘manages_expiry’, 
   ‘manages_homedir’, ‘manages_password_age’, ‘manages_passwords’, 
   ‘manages_shell’. 

- **directoryservice** 
   User management on OS X. 
   * Required binaries: ‘/usr/bin/dscacheutil’, ‘/usr/bin/dscl’, 
   ‘/usr/bin/dsimport’, ‘/usr/bin/plutil’, ‘/usr/bin/uuidgen’. 
   * Default for ‘operatingsystem’ == ‘darwin’. 
   * Supported features: ‘manages_password_salt’, ‘manages_passwords’, 
   ‘manages_shell’.

- **hpuxuseradd** 
   User management for HP-UX. This provider uses the undocumented ‘-F’ 
   switch to HP-UX's special ‘usermod’ binary to work around the fact that 
   its standard ‘usermod’ cannot make changes while the user is logged in. 
   * Required binaries: ‘/usr/sam/lbin/useradd.sam’, 
   ‘/usr/sam/lbin/userdel.sam’, ‘/usr/sam/lbin/usermod.sam’. 
   * Default for ‘operatingsystem’ == ‘hp-ux’. 
   * Supported features: ‘allows_duplicates’, ‘manages_homedir’, 
   ‘manages_passwords’.  

- **ldap** 
   User management via LDAP. 
   This provider requires that you have valid values for all of the 
   LDAP-related settings in ‘puppet.conf’, including ‘ldapbase’.
   You will almost definitely need settings for ‘ldapuser’ and ‘ldappassword’ in order 
   for your clients to write to LDAP. 
* Supported features: ‘manages_passwords’, ‘manages_shell’.  

- **pw** 
   User management via ‘pw’ on FreeBSD and DragonFly BSD. 
   * Required binaries: ‘pw’. 
   * Default for ‘operatingsystem’ == ‘freebsd, dragonfly’. 
   * Supported features: ‘allows_duplicates’, ‘manages_expiry’, 
   ‘manages_homedir’, ‘manages_passwords’, ‘manages_shell’. 

- **user_role_add** 
   User and role management on Solaris, via ‘useradd’ and ‘roleadd’. 
   * Required binaries: ‘passwd’, ‘roleadd’, ‘roledel’, ‘rolemod’, 
   ‘useradd’, ‘userdel’, ‘usermod’. 
   * Default for ‘osfamily’ == ‘solaris’. 
   * Supported features: ‘allows_duplicates’, ‘manages_homedir’, 
   ‘manages_password_age’, ‘manages_passwords’, ‘manages_solaris_rbac’.  

- **useradd** 
   User management via ‘useradd’ and its ilk.  Note that you will need to 
   install Ruby's shadow password library (often known as ‘ruby-libshadow’) 
   if you wish to manage user passwords. 
   * Required binaries: ‘chage’, ‘luseradd’, ‘useradd’, ‘userdel’, ‘usermod’. 
   * Supported features: ‘allows_duplicates’, ‘libuser’, ‘manages_expiry’, 
   ‘manages_homedir’, ‘manages_password_age’, ‘manages_passwords’, 
   ‘manages_shell’, ‘system_users’.  

- **windows_adsi** 
   Local user management for Windows. 
   * Default for 'operatingsystem' == 'windows'. 
   * Supported features: 'manages_homedir', 'manages_passwords'. 
```


### **测试资源**

在 Puppet 中，测试资源直接意味着需要先申请自己想要使用的资源来配置目标节点，从而使机器的状态发生相应的变化。

为了测试，我们将在本地应用资源。因为我们在上面使用 `user = vipin` 预定义了一个资源。**应用资源的一种方法是通过 CLI。这可以通过将完整资源重写为单个命令然后将其传递给资源子命令来完成**。

```
puppet resource user vipin ensure = present uid = '505'
shell = '/bin/bash' home = '/home/vipin'
```

测试应用的资源。

```
[root@puppetmaster ~]# cat /etc/passwd | grep "vipin"
vipin:x:505:501::/home/vipin:/bin/bash
```

上面的输出显示资源已应用于系统，我们创建了一个名为 Vipin 的新用户。建议您自行测试，因为上述所有代码都经过测试并且是工作代码。