# **Puppet - Live Project 1**


为了在 Puppet 节点上执行应用配置和清单的实时测试，我们将使用实时工作演示。这可以直接复制粘贴来测试配置是如何工作的。如果用户希望使用相同的代码集，他需要具有相同的命名约定，如下面的代码片段所示。

让我们从创建一个新模块开始。

## **创建新模块**

测试和应用 httpd 配置的第一步是创建一个模块。

为此，用户需要将其工作目录更改为 Puppet 模块目录并创建基本模块结构。结构创建可以手动完成，也可以使用 Puppet 为模块创建样板。

```
# cd /etc/puppet/modules
# puppet module generate Live-module
```

**注意** - **Puppet 模块生成命令要求模块名称采用 [用户名]-[模块] 的格式以符合 Puppet forge 规范**。

**新模块包含一些基本文件，包括清单目录**。

该目录已经包含一个名为 `init.pp` 的清单，它是模块的主要清单文件。这是模块的空类声明。

```
class live-module {
}
```

**该模块还包含一个测试目录，其中包含一个名为 `init.pp` 的清单**。此测试清单包含对 `manifest/init.pp` 中 `live-module` 类的引用：

```
include live-module
```


Puppet 将使用这个测试模块来测试清单。现在我们准备将配置添加到模块中。

## **安装 HTTP 服务器**

Puppet 模块将安装必要的包来运行 http 服务器。这需要一个资源定义来定义 httpd 包的配置。

在模块的清单目录中，创建一个名为 httpd.pp 的新清单文件

```
# touch test-module/manifests/httpd.pp
```

此清单将包含我们模块的所有 HTTP 配置。出于分离目的，我们将 httpd.pp 文件与 init.pp 清单文件分开

我们需要将以下代码放入 httpd.pp 清单文件中。

```
class test-module::httpd { 
   package { 'httpd': 
      ensure => installed, 
   } 
}
```

这段代码定义了一个名为 httpd 的 test-module 子类，然后为 httpd 包定义了一个包资源声明。 

`ensure => installed` 属性检查是否安装了所需的包。

如果没有安装，`Puppet `使用 `yum` 实用程序来安装它。接下来，是将这个子类包含在我们的主清单文件中。我们需要编辑 `init.pp` 清单。

```
class test-module { 
   include test-module::httpd 
}
```

现在，是时候测试模块了，可以按如下方式完成

```
# puppet apply test-module/tests/init.pp --noop
```


`puppet apply` 命令应用存在于目标系统清单文件中的配置。

**在这里，我们使用的是 `test init.pp`，它指的是 `main init.pp`**。 

`–noop` 执行配置的试运行，它只显示输出但实际上不做任何事情。

以下是输出。

```
Notice: Compiled catalog for puppet.example.com in environment
production in 0.59 seconds

Notice: /Stage[main]/test-module::Httpd/Package[httpd]/ensure:
current_value absent, should be present (noop)

Notice: Class[test-module::Httpd]: Would have triggered 'refresh' from 1
events

Notice: Stage[main]: Would have triggered 'refresh' from 1 events
Notice: Finished catalog run in 0.67 seconds
```


高亮线是 `ensure => installed` 属性的结果。 

**`current_value `不存在意味着 Puppet 已检测到 httpd 包已安装。如果没有 `–noop` 选项，Puppet 将安装 httpd 包。**

### **运行 httpd 服务器**

安装好httpd服务器后，我们需要使用其他资源启动服务：Service

我们需要编辑 httpd.pp 清单文件并编辑以下内容。

```
class test-module::httpd { 
   package { 'httpd': 
      ensure => installed, 
   } 
   service { 'httpd': 
      ensure => running, 
      enable => true, 
      require => Package["httpd"], 
   } 
}
```

以下是我们通过上述代码实现的目标列表。

* `ensure => running` 状态检查服务是否正在运行，如果没有，则启用它。

* `enable => true `属性将服务设置为在系统启动时运行。
 
* `require => Package["httpd"]` 属性定义了一个资源减速和另一个之间的排序关系。在上述情况下，它确保了 httpd 服务在 http 包安装后启动。这会在服务和相应的包之间创建依赖关系


运行 puppet apply 命令再次测试更改。

```
# puppet apply test-module/tests/init.pp --noop
Notice: Compiled catalog for puppet.example.com in environment
production in 0.56 seconds

Notice: /Stage[main]/test-module::Httpd/Package[httpd]/ensure:
current_value absent, should be present (noop)

Notice: /Stage[main]/test-module::Httpd/Service[httpd]/ensure:
current_value stopped, should be running (noop)

Notice: Class[test-module::Httpd]: Would have triggered 'refresh' from 2
events

Notice: Stage[main]: Would have triggered 'refresh' from 1 events
Notice: Finished catalog run in 0.41 seconds
```

## **配置 httpd 服务器**

完成上述步骤后，我们将安装并启用 HTTP 服务器。下一步是为服务器提供一些配置。默认情况下，httpd 在 `/etc/httpd/conf/httpd.conf `中提供了一些默认配置，它提供了一个 webhost 端口 80。我们将添加一些额外的主机来为 web-host 提供一些用户特定的设施。

模板将用于提供额外的端口，因为它需要变量输入。我们将创建一个名为 `template` 的目录，并在新目录中添加一个名为 `test-server.config.erb` 的文件，并添加以下内容。

**`test-server.config.erb`**

```
Listen <%= @httpd_port %> 
NameVirtualHost *:<% = @httpd_port %> 

<VirtualHost *:<% = @httpd_port %>> 
   DocumentRoot /var/www/testserver/ 
   ServerName <% = @fqdn %> 
   
   <Directory "/var/www/testserver/"> 
      Options All Indexes FollowSymLinks 
      Order allow,deny 
      Allow from all 
   </Directory> 
</VirtualHost>
```

上面的模板遵循标准的 apache-tomcat 服务器配置格式。唯一的区别是使用 Ruby 转义字符从模块中注入变量。我们有存储系统完全限定域名的 FQDN。**这被称为系统事实**。

在生成每个相应系统的 puppet 目录之前，从每个系统收集系统事实。 

**Puppet 使用 facter 命令获取此信息，并且可以使用 facter 获取有关系统的其他详细信息。我们需要在 httpd.pp 清单文件中添加高亮行**。

```
class test-module::httpd { 
   package { 'httpd': 
      ensure => installed, 
   } 
   service { 'httpd': 
      ensure => running, 
      enable => true, 
      require => Package["httpd"], 
   } 
   file {'/etc/httpd/conf.d/testserver.conf': 
      notify => Service["httpd"], 
      ensure => file, 
      require => Package["httpd"], 
      content => template("test-module/testserver.conf.erb"), 
   } 
   file { "/var/www/myserver": 
      ensure => "directory", 
   } 
}
```

这有助于实现以下目标 -

* 这会为服务器配置文件 (`/etc/httpd/conf.d/test-server.conf`) 添加文件资源声明。该文件的内容是之前创建的 `test-serverconf.erb` 模板。我们还检查了在添加此文件之前安装的 httpd 包。

* 这添加了第二个文件资源声明，它为 Web 服务器创建了一个目录 (`/var/www/test-server`)。

* **接下来，我们使用 `notify => Service["httpd"]`  属性添加配置文件和 https 服务的关系。这将检查是否有任何配置文件更改。如果有，则 Puppet 重新启动服务。**

接下来是在主清单文件中包含 `httpd_port`。为此，我们需要结束主 init.pp 清单文件并包含以下内容

```
class test-module ( 
   $http_port = 80 
) { 
   include test-module::httpd 
}
```
这会将 httpd 端口设置为默认值 80。接下来是运行 Puppet 应用命令。

以下将是输出。

```
# puppet apply test-module/tests/init.pp --noop
Warning: Config file /etc/puppet/hiera.yaml not found, using Hiera
defaults

Notice: Compiled catalog for puppet.example.com in environment
production in 0.84 seconds

Notice: /Stage[main]/test-module::Httpd/File[/var/www/myserver]/ensure:
current_value absent, should be directory (noop)

Notice: /Stage[main]/test-module::Httpd/Package[httpd]/ensure:
current_value absent, should be present (noop)

Notice:
/Stage[main]/test-module::Httpd/File[/etc/httpd/conf.d/myserver.conf]/ensure:
current_value absent, should be file (noop)

Notice: /Stage[main]/test-module::Httpd/Service[httpd]/ensure:
current_value stopped, should be running (noop)

Notice: Class[test-module::Httpd]: Would have triggered 'refresh' from 4
events

Notice: Stage[main]: Would have triggered 'refresh' from 1 events
Notice: Finished catalog run in 0.51 seconds
```

## **配置防火墙**

为了与服务器通信，需要一个开放端口。这里的问题是不同类型的操作系统使用不同的方法来控制防火墙。**在 Linux 的情况下，低于 6 的版本使用 iptables，而版本 7 使用 firewalld**。

Puppet 使用系统事实及其逻辑在一定程度上处理了使用适当服务的决定。为此，我们需要首先检查操作系统，然后运行相应的防火墙命令。

为了实现这一点，我们需要在 `testmodule::http` 类中添加以下代码片段。

```
if $operatingsystemmajrelease <= 6 { 
   exec { 'iptables': 
      command => "iptables -I INPUT 1 -p tcp -m multiport --ports 
      ${httpd_port} -m comment --comment 'Custom HTTP Web Host' -j ACCEPT && 
      iptables-save > /etc/sysconfig/iptables", 
      path => "/sbin", 
      refreshonly => true, 
      subscribe => Package['httpd'], 
   } 
   service { 'iptables': 
      ensure => running, 
      enable => true, 
      hasrestart => true, 
      subscribe => Exec['iptables'], 
   } 
}  elsif $operatingsystemmajrelease == 7 { 
   exec { 'firewall-cmd': 
      command => "firewall-cmd --zone=public --addport = $ { 
      httpd_port}/tcp --permanent", 
      path => "/usr/bin/", 
      refreshonly => true, 
      subscribe => Package['httpd'], 
   } 
   service { 'firewalld': 
      ensure => running, 
      enable => true, 
      hasrestart => true, 
      subscribe => Exec['firewall-cmd'], 
   } 
} 
```

上面的代码执行以下操作 -

* 使用 `operatingsystemmajrelease` 确定使用的操作系统是版本 6 还是 7。
* 如果版本是 6，那么它运行所有必需的配置命令来配置 Linux 6 版本。
* 如果操作系统版本为 7，则它运行配置防火墙所需的所有命令。
* 两个操作系统的代码片段都包含一个逻辑，该逻辑确保配置仅在安装 http 包后运行。

最后，运行 Puppet 应用命令。

```
# puppet apply test-module/tests/init.pp --noop
Warning: Config file /etc/puppet/hiera.yaml not found, using Hiera
defaults

Notice: Compiled catalog for puppet.example.com in environment
production in 0.82 seconds

Notice: /Stage[main]/test-module::Httpd/Exec[iptables]/returns:
current_value notrun, should be 0 (noop)

Notice: /Stage[main]/test-module::Httpd/Service[iptables]: Would have
triggered 'refresh' from 1 events
```

## **配置 SELinux**
	
由于我们正在使用版本 7 及更高版本的 Linux 机器，因此我们需要对其进行配置以进行 http 通信。 SELinux 默认限制对 HTTP 服务器的非标准访问。如果我们定义一个自定义端口，那么我们需要配置 SELinux 以提供对该端口的访问。

Puppet 包含一些资源类型来管理 SELinux 功能，例如布尔值和模块。


在这里，我们需要执行 semanage 命令来管理端口设置。该工具是 `policycoreutils-python` 软件包的一部分，默认情况下未安装在 red-hat 服务器上。

为了实现上述目的，我们需要在 `test-module::http` 类中添加以下代码。


```
exec { 'semanage-port': 
   command => "semanage port -a -t http_port_t -p tcp ${httpd_port}", 
   path => "/usr/sbin", 
   require => Package['policycoreutils-python'], 
   before => Service ['httpd'], 
   subscribe => Package['httpd'], 
   refreshonly => true, 
} 

package { 'policycoreutils-python': 
   ensure => installed, 
} 
```

上面的代码执行以下操作 -

* `require => Package['policycoreutils-python'] `确保我们安装了所需的 python 模块。
* Puppet 使用 semanage 打开端口，使用 `httpd_port` 作为验证。
* before => 服务确保在 httpd 服务启动之前执行此命令。如果 HTTPD 在 SELinux 命令之前启动，则 SELinux 服务请求和服务请求失败。


```
# puppet apply test-module/tests/init.pp --noop
...
Notice: /Stage[main]/test-module::Httpd/Package[policycoreutilspython]/
ensure: current_value absent, should be present (noop)
...
Notice: /Stage[main]/test-module::Httpd/Exec[semanage-port]/returns:
current_value notrun, should be 0 (noop)
...
Notice: /Stage[main]/test-module::Httpd/Service[httpd]/ensure:
current_value stopped, should be running (noop)
```

Puppet先安装python模块，然后配置端口访问，最后启动httpd服务。


## **在 Web 主机中复制 HTML 文件**

通过以上步骤我们就完成了http服务器的配置。现在，我们已经准备好安装一个基于 Web 的应用程序的平台，Puppet 也可以对其进行配置。为了测试，我们将一些示例 html 索引网页复制到服务器。

在 files 目录中创建一个 index.html 文件。

```
<html> 
   <head> 
      <title>Congratulations</title> 
   <head> 
   
   <body> 
      <h1>Congratulations</h1> 
      <p>Your puppet module has correctly applied your configuration.</p> 
   </body> 
</html> 
```

**在 manifest 目录中创建一个 `manifest app.pp` 并添加以下内容。**

```
class test-module::app { 
   file { "/var/www/test-server/index.html": 
      ensure => file, 
      mode => 755, 
      owner => root, 
      group => root, 
      source => "puppet:///modules/test-module/index.html", 
      require => Class["test-module::httpd"], 
   } 
}
```


这个新类包含单个资源。这会将文件从模块的文件目录复制到 Web 服务器并设置其权限。 `required` 属性确保 `test-module::http `类在应用 `test-module::app` 之前成功完成配置。

最后，我们需要在我们的主 `init.pp` 清单中包含一个新清单。

```
class test-module ( 
   $http_port = 80 
) { 
   include test-module::httpd 
   include test-module::app 
} 
```

现在，运行 apply 命令来实际测试正在发生的事情。以下将是输出。

```
# puppet apply test-module/tests/init.pp --noop
Warning: Config file /etc/puppet/hiera.yaml not found, using Hiera
defaults

Notice: Compiled catalog for brcelprod001.brcle.com in environment
production in 0.66 seconds

Notice: /Stage[main]/Test-module::Httpd/Exec[iptables]/returns:
current_value notrun, should be 0 (noop)

Notice: /Stage[main]/Test-module::Httpd/Package[policycoreutilspython]/
ensure: current_value absent, should be present (noop)

Notice: /Stage[main]/Test-module::Httpd/Service[iptables]: Would have
triggered 'refresh' from 1 events

Notice: /Stage[main]/Test-module::Httpd/File[/var/www/myserver]/ensure:
current_value absent, should be directory (noop)

Notice: /Stage[main]/Test-module::Httpd/Package[httpd]/ensure:
current_value absent, should be present (noop)

Notice:
/Stage[main]/Test-module::Httpd/File[/etc/httpd/conf.d/myserver.conf]/ensur
e: current_value absent, should be file (noop)

Notice: /Stage[main]/Test-module::Httpd/Exec[semanage-port]/returns:
current_value notrun, should be 0 (noop)

Notice: /Stage[main]/Test-module::Httpd/Service[httpd]/ensure:
current_value stopped, should be running (noop)

Notice: Class[test-module::Httpd]: Would have triggered 'refresh' from 8
Notice:
/Stage[main]/test-module::App/File[/var/www/myserver/index.html]/ensur:
current_value absent, should be file (noop)

Notice: Class[test-module::App]: Would have triggered 'refresh' from 1
Notice: Stage[main]: Would have triggered 'refresh' from 2 events Notice:
Finished catalog run in 0.74 seconds
```

突出显示的行显示了 index.html 文件被复制到网络主机的结果。

## **完成模块**

通过上述所有步骤，我们创建的新模块就可以使用了。如果我们想创建模块的存档，可以使用以下命令完成。

```
# puppet module build test-module
```