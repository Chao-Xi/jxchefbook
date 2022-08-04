# **3 Puppet 编码风格**

在 Puppet 中，编码风格定义了在尝试将机器配置上的基础设施转换为代码时需要遵循的所有标准。 Puppet 使用资源工作并执行所有定义的任务。

Puppet 的语言定义有助于以结构化方式指定所有资源，这是管理任何需要管理的目标机器所必需的。 Puppet 使用 Ruby 作为其编码语言，它具有多个内置功能，可以通过代码端的简单配置轻松完成工作。

## **1 Fundamental Units**

Puppet 使用多种基本编码风格，易于理解和管理。以下是几个列表

### **1-2 资源**

在 Puppet 中，资源被称为基本建模单元，用于管理或修改任何目标系统。资源涵盖系统的所有方面，例如文件、服务和包。 Puppet 具有内置功能，允许用户或开发人员开发自定义资源，这有助于管理机器的任何特定单元

在 Puppet 中，所有资源都通过使用“定义Define”或“类Class”聚合在一起。这些聚合功能有助于组织模块。

以下是一个示例资源，它包含多个类型、一个标题和一个 Puppet 可以支持多个属性的属性列表。 Puppet 中的每个资源都有自己的默认值，可以在需要时覆盖。

### **1-3 文件的样本 Puppet 资源**


在以下命令中，我们尝试为特定文件指定权限。

```
file {  
   '/etc/passwd': 
   owner => superuser, 
   group => superuser, 
   mode => 644, 
}
```


每当在任何机器上执行上述命令时，它都会验证系统中的 **passwd** 文件是否已按照描述进行配置。

前面的文件  `:colon` 是资源的标题，在Puppet配置的其他部分可以称为资源

## **2 除标题外还指定本地名称**

```
file { 'sshdconfig': 
   name => $operaSystem ? { 
      solaris => '/usr/local/etc/ssh/sshd_config', 
      default => '/etc/ssh/sshd_config', 
   }, 
   owner => superuser, 
   group => superuser, 
   mode => 644, 
}
```

通过使用始终相同的title，可以很容易地在配置中引用文件资源，而无需重复与操作系统相关的逻辑。

另一个示例可能是使用依赖于文件的服务。

```
service { 'sshd': 
   subscribe => File[sshdconfig], 
} 
```

有了这个依赖，一旦 **sshdconfig** 文件发生变化，**sshd** 服务总是会重启。

这里要记住的一点是 **File[sshdconfig]** 是一个小写形式的 File 声明，但是如果我们将其更改为 **FILE[sshdconfig]** 那么它将是一个引用。

在声明资源时需要牢记的一个基本点是，每个配置文件只能声明一次。多次重复声明同一资源会导致错误。通过这个基本概念，Puppet 确保配置得到很好的建模。

我们甚至有能力管理资源依赖，这有助于管理多个关系。

```
service { 'sshd': 
   require => File['sshdconfig', 'sshconfig', 'authorized_keys']
}   
```


## **2 Metaparameters**

Metaparameters在 Puppet 中称为全局参数。Metaparameters的关键特性之一是，它适用于 Puppet 中的任何类型的资源。

### **2-1 资源默认值**

当需要定义默认资源属性值时，Puppet 提供了一组语法来归档它，使用没有标题的大写资源规范。

例如，如果我们想设置所有可执行文件的默认路径，可以使用以下命令完成。

```
Exec { path => '/usr/bin:/bin:/usr/sbin:/sbin' } 
exec { 'echo Testing mataparamaters.': } 
```

在上述命令中，第一条语句 Exec 将设置 exec 资源的默认值。 **Exec 资源需要完全限定的路径或看起来像可执行文件的路径**。

有了这个，可以为整个配置定义一个默认路径。默认值适用于 Puppet 中的任何资源类型。

**默认值不是全局值，但是，它们只影响定义它们的范围或它的下一个变量。**

如果想要为完整的配置定义**默认值**，那么我们将在下一节中定义**默认值**和**类**。


## **3 资源集合**

聚合是将事物收集在一起的方法。 Puppet 支持非常强大的聚合概念。在 Puppet 中，聚合用于将 Puppet 的基本单元资源分组在一起。 

Puppet 中的这种聚合概念是通过使用称为类和定义的两种强大方法来实现的。

聚合是将事物收集在一起的方法。 Puppet 支持非常强大的聚合概念。在 Puppet 中，聚合用于将 Puppet 的基本单元资源分组在一起。 Puppet 中的这种聚合概念是通过使用称为**类Class和定义definition**的两种强大方法来实现的。

### **3-1 Classes and Definition**

**类Classes** 负责对节点的基本方面进行建模。他们可以说节点是一个 Web 服务器，而这个特定的节点就是其中之一。

在 Puppet 中，编程类是单例的，每个节点可以评估一次。

另一方面，**定义可以在单个节点上多次使用**。它们的工作方式与使用该语言创建自己的 Puppet 类型类似。

它们被创建为多次使用，每次都有不同的输入。这意味着可以将变量值传递到定义中。


### **3-2 类与定义的区别**

类和定义之间的唯一关键区别是在定义构建结构和分配资源时，每个节点只对类进行一次评估，而另一方面，在同一个节点上多次使用定义。

**Classes**

Puppet 中的类是使用 class 关键字引入的，该特定类的内容包含在花括号内，如下例所示。

```
class unix { 
   file { 
      '/etc/passwd': 
      owner => 'superuser', 
      group => 'superuser', 
      mode => 644; 
      '/etc/shadow': 
      owner => 'vipin', 
      group => 'vipin', 
      mode => 440; 
   } 
}
```

在下面的例子中，我们使用了一些类似于上面的简写。

```
class unix { 
   file { 
      '/etc/passwd': 
      owner => 'superuser', 
      group => 'superuser', 
      mode => 644; 
   }  
   
   file {'/etc/shadow': 
      owner => 'vipin', 
      group => 'vipin', 
      mode => 440; 
   } 
} 
```

### **3-3 Puppet 类中的继承**

在 Puppet 中，默认支持 OOP 的继承概念，其中类可以扩展先前的功能，而无需在新创建的类中再次复制和粘贴完整的代码位。继承允许子类覆盖父类中定义的资源设置。**使用继承时要记住的一件事是，一个类只能从一个父类继承特性，不能超过一个**。

```
class superclass inherits testsubclass { 
   File['/etc/passwd'] { group => wheel } 
   File['/etc/shadow'] { group => wheel } 
}
```


如果需要撤消父类中指定的某些逻辑，**我们可以使用 undef 命令**。

```
class superclass inherits testsubcalss { 
   File['/etc/passwd'] { group => undef } 
} 
```


**使用继承的另一种方式**

```
class tomcat { 
   service { 'tomcat': require => Package['httpd'] } 
} 
class open-ssl inherits tomcat { 
   Service[tomcat] { require +> File['tomcat.pem'] } 
}
```

### **3-4 Puppet 中的嵌套类**

Puppet 支持类嵌套的概念，它允许使用嵌套类，这意味着一个类在另一个类中。这有助于实现模块化和范围界定。

```
class testclass { 
   class nested { 
      file {  
         '/etc/passwd': 
         owner => 'superuser', 
         group => 'superuser', 
         mode => 644; 
      } 
   } 
} 
class anotherclass { 
   include myclass::nested 
} 
```

### **3-5 参数化类**

在 Puppet 中，类可以扩展其功能以允许将参数传递给类。

要在类中传递参数，可以使用以下构造 -

```
class tomcat($version) { 
   ... class contents ... 
} 
```

在 Puppet 中要记住的一个关键点是，带参数的类不是使用 include 函数添加的，而是可以将生成的类作为定义添加

```
node webserver { 
   class { tomcat: version => "1.2.12" } 
}
```

**默认值作为类中的参数**

```
class tomcat($version = "1.2.12",$home = "/var/www") { 
   ... class contents ... 
} 
```

## **4 Run Stages**

Puppet 支持运行阶段的概念，这意味着用户可以根据需要添加多个阶段，以管理任何特定资源或多个资源。当用户想要开发复杂的目录时，此功能非常有用。

在一个复杂的目录中，有大量的资源需要编译，同时记住不应该影响定义的资源之间的依赖关系。

Run Stage 在管理资源依赖方面非常有帮助。这可以通过在定义的阶段添加类来完成，其中特定类包含资源集合。

通过运行阶段，Puppet 保证定义的阶段将在每次目录运行并应用于任何 Puppet 节点时以指定的可预测顺序运行。

为了使用它，需要在已经存在的阶段之外声明额外的阶段，然后可以将 Puppet 配置为使用相同的资源关系语法按指定的顺序管理每个阶段，然后再使用 `require “->”` 和 `“+>”`。然后，该关系将保证与每个阶段关联的类的顺序。

### **4-1 使用 Puppet 声明式语法声明其他阶段**

```
stage { "first": before => Stage[main] } 
stage { "last": require => Stage[main] } 
```


一旦声明了阶段，一个类可以与该阶段相关联，而不是使用该阶段的主要阶段。

```
class { 
   "apt-keys": stage => first; 
   "sendmail": stage => main; 
   "apache": stage => last; 
}
```

* 与类 apt-key 关联的所有资源将首先运行。 
* Sendmail 中的所有资源将是主类，
* 与 Apache 相关的资源将是最后一个阶段。

## **5 定义 Definitions**

在 Puppet 中，任何清单文件中的资源收集都是通过类class或定义defintions完成的。

定义与 Puppet 中的类非常相似，但是它们是通过定义 **关键字（不是类）define keyword (not class)** 引入的，并且它们支持参数而不是继承。它们可以使用不同的参数在同一系统上多次运行。

例如，如果一个人想要创建一个定义来控制源代码存储库，其中一个人试图在同一系统上创建多个存储库，那么可

```
define perforce_repo($path) { 
   exec {  
      "/usr/bin/svnadmin create $path/$title": 
      unless => "/bin/test -d $path", 
   } 
} 
svn_repo { puppet_repo: path => '/var/svn_puppet' } 
svn_repo { other_repo: path => '/var/svn_other' }
```

这里要注意的关键点是如何将变量与定义一起使用。我们使用 `($)`美元符号变量。

在上面，我们使用了 `$title`。定义可以同时具有 `$title `和 `$name` 来表示名称和标题。

默认情况下，`$title` 和 `$name` 设置为相同的值，但可以设置一个 title 属性并将不同的名称作为参数传递。 `$title` 和 `$name` 仅适用于定义，不适用于类或其他资源。

## **6 Modules**

一个模块可以定义为所有配置的集合，Puppet master 将使用这些配置在任何特定的 Puppet 节点（代理）上应用配置更改。

它们也被称为执行特定任务所需的不同类型配置的便携式集合。**例如，一个模块可能包含配置 Postfix 和 Apache 所需的所有资源。**



## **7 节点**
节点是非常简单的剩余步骤，这是我们如何将我们定义的内容（“这就是Webserver的样子”）与选择哪些机器来完成这些指令相匹配。

节点定义看起来就像类，包括支持的继承，但是它们是特殊的，当一个节点（运行 puppet 客户端的托管计算机）连接到 Puppet 主守护程序时，它的名称将在定义的节点列表中查找。定义的信息将针对节点进行评估，然后节点将发送该配置。

```
node 'www.vipin.com' { 
   include common 
   include apache, squid 
}
```

上面的定义创建了一个名为 www.vipin.com 的节点，并包含了 common、Apache 和 Squid 类

我们可以通过用逗号分隔每个节点来将相同的配置发送到不同的节点。

```
node 'www.testing.com', 'www.testing2.com', 'www3.testing.com' { 
   include testing 
   include tomcat, squid 
}
```

### **匹配节点的正则表达式**

```
node /^www\d+$/ { 
   include testing 
}
```


### **节点继承**


Node 支持有限的继承模型。像类一样，节点只能从另一个节点继承

```
node 'www.testing2.com' inherits 'www.testing.com' { 
   include loadbalancer 
}
```

在上面的代码中，www.testing2.com 继承了 www.testing.com 的所有功能以及一个额外的负载均衡器类。


## **8 高级支持的功能**

**引用** - 在大多数情况下，我们不需要在 Puppet 中引用字符串。任何以字母开头的字母数字字符串都应不加引号。但是，为任何非负值引用字符串始终是最佳实践。

### **带引号的变量插值 Variable Interpolation with Quotes**

到目前为止，我们已经在定义方面提到了变量。如果需要将这些变量与字符串一起使用，请使用双引号，而不是单引号。单引号字符串不会做任何变量插值，双引号字符串会做。变量可以括在 `{}` 中，这使它们更易于一起使用且更易于理解。

```
$value = "${one}${two}"
```

作为一种最佳实践，应该对所有不需要字符串插值的字符串使用单引号。

## **9 Capitalization**

Capitalization是用于引用、继承和设置特定资源的默认属性的过程。基本上有两种基本的使用方法。

* **引用 Referencing** - 这是引用已创建资源的方式。它主要用于依赖目的，必须将资源名称大写。例如，`要求 => 文件 [sshdconfig]`
* **继承 Inheritance** - 从子类覆盖父类的设置时，**使用资源名称的大写版本**。使用小写版本将导致错误。
* **设置默认属性值** - 使用没有标题的大写资源来设置资源的默认值。

## **10 数组**

Puppet 允许在多个区域 [1,2,3] 中使用数组。

一些类型成员，例如主机定义中的别名，在其值中接受数组。具有多个别名的主机资源如下所示。

```
host { 'one.vipin.com': 
   alias => [ 'satu', 'dua', 'tiga' ], 
   ip => '192.168.100.1', 
   ensure => present, 
}
```

上面的代码会将主机`“one.brcletest.com”`添加到具有三个别名`“satu”“dua”“tiga”`的主机列表中。如果想将多个资源添加到一个资源中，可以如下例所示进行。

```
resource { 'baz': 
   require => [ Package['rpm'], File['testfile'] ], 
}
```


## **11 变量**

像大多数其他编程语言一样，Puppet 支持多个变量。 Puppet 变量用 `$` 表示。

```
$content = 'some content\n' 
file { '/tmp/testing': content => $content } 
```

如前所述，Puppet 是一种声明式语言，这意味着它的作用域和赋值规则与命令式语言不同。

主要区别在于不能在单个范围内更改变量，因为它们依赖于文件中的顺序来确定变量的值。顺序在声明性语言中无关紧要。

```
$user = root 
file {  
   '/etc/passwd': 
   owner => $user, 
} 

$user = bin 
   file {  
      '/bin': 
      owner => $user, 
      recurse => true, 
   }
```


### **变量范围**

变量范围定义是否所有定义的变量都有效。与最新功能一样，Puppet 目前是动态范围的，在 Puppet 术语中，这意味着所有定义的变量都在其范围内进行评估，而不是在它们定义的位置上进行评估。

```
$test = 'top' 
class Testclass { 
   exec { "/bin/echo $test": logoutput => true } 
} 

class Secondtestclass { 
   $test = 'other' 
   include myclass 
} 

include Secondtestclass 
```


### **限定变量**

Puppet 支持在类或定义中使用限定变量。当用户希望在他已经定义或将要定义的其他类中使用相同的变量时，这非常有用

```
class testclass { 
   $test = 'content' 
} 

class secondtestclass { 
   $other = $myclass::test 
} 
```


在上面的代码中，$other 变量的值评估了内容。

## **12 Conditionals**

条件是用户希望在满足定义的条件或所需条件时执行一组语句或代码的情况。 Puppet 支持两种类型的条件。

只能在定义的资源内使用选择器条件来选择机器的正确值。

语句条件是清单中更广泛使用的条件，它有助于包含用户希望包含在同一清单文件中的其他类。在一个类中定义一组不同的资源，或做出其他结构决策。

## **13 选择器**

当用户希望指定与基于事实或其他变量的默认值不同的资源属性和变量时，选择器很有用。在 Puppet 中，选择器索引的工作方式类似于多值三向运算符。选择器还能够在 no 值中定义自定义默认值，这些值在清单中定义并匹配条件。

```
$owner = $Sysoperenv ? { 
   sunos => 'adm', 
   redhat => 'bin', 
   default => undef, 
}
```

在 Puppet 0.25.0 的更高版本中，选择器可以用作正则表达式。

```
$owner = $Sysoperenv ? { 
   /(Linux|Ubuntu)/ => 'bin', 
   default => undef, 
}
```


在上面的例子中，选择器 `$Sysoperenv` 的值匹配 Linux 或 Ubuntu，那么 bin 将是选择的结果，否则用户将被设置为 undefined。

## **14 语句条件**

语句条件是 Puppet 中的另一种条件语句，与 Shell 脚本中的 switch case 条件非常相似。在这种情况下，定义了一组多例语句，并且给定的输入值与每个条件匹配。

匹配给定输入条件的 case 语句被执行。此 case 语句条件没有任何返回值。在 Puppet 中，条件语句的一个非常常见的用例是运行一组基于底层操作系统的代码位

```
case $ Sysoperenv { 
   sunos: { include solaris }  
   redhat: { include redhat }  
   default: { include generic}  
}
```

Case 语句还可以通过用逗号分隔多个条件来指定多个条件。

```
case $Sysoperenv { 
   development,testing: { include development } testing,production: { include production }
   default: { include generic }  
} 
```


## **15 If-Else 语句**

Puppet 支持基于条件的操作的概念。为了实现它，If/else 语句提供了基于条件返回值的分支选项。如以下示例所示 -

```
if $Filename { 
   file { '/some/file': ensure => present } 
} else { 
   file { '/some/other/file': ensure => present } 
} 
```

最新版本的 Puppet 支持变量表达式，其中 if 语句也可以根据表达式的值进行分支。

```
if $machine == 'production' { 
   include ssl 
} else { 
   include nginx 
}
```

为了实现代码的更多多样性和执行复杂的条件操作，Puppet 支持嵌套 if/else 语句，如下代码所示。

```
if $ machine == 'production' { 
   include ssl 
} elsif $ machine == 'testing' { 
   include nginx
} else { 
   include openssl 
} 
```


## **16 虚拟资源**

虚拟资源是那些除非实现才发送给客户端的资源。

以下是在 Puppet 中使用虚拟资源的语法。

```
@user { vipin: ensure => present }
```


在上面的示例中，虚拟定义了用户vipin，以实现可以在集合中使用的定义

```
User <| title == vipin |>
```


## **17 注释**

在任何代码位中都使用注释来创建关于一组代码行及其功能的附加节点。在 Puppet 中，目前支持两种类型的评论。

* Unix shell 风格的注释。他们可以在自己的一行或下一行。
* 多行 c 样式注释。

以下是 shell 样式注释的示例。

```
# this is a comment
```

Following is an example of multiline comment.

```
/*
This is a comment
*/
```


## **18 运算符优先级**

Puppet 运算符优先级符合大多数系统中的标准优先级，从最高到最低。

以下是表达式列表

* `!` = not
* `/` = times and divide
* `- +` = minus, plus
* `<< >>` = left shift and right shift
* `== !=` = not equal, equal
* `>= <= > <` = greater equal, less or equal, greater than, less than

## **19 比较表达式**

当用户想要在满足给定条件时执行一组语句时使用比较表达式。比较表达式包括使用 == 表达式的相等性测试。

```
if $environment == 'development' { 
   include openssl 
} else { 
   include ssl 
} 
```
### **Not Equal Example**

```
if $environment != 'development' { 
   $otherenvironment = 'testing' 
} else { 
   $otherenvironment = 'production' 
} 
```

### **Arithmetic Expression**

```
$one = 1 
$one_thirty = 1.30 
$two = 2.034e-2 $result = ((( $two + 2) / $one_thirty) + 4 * 5.45) - 
   (6 << ($two + 4)) + (0×800 + -9)
```

### **Boolean Expression**

Boolean expressions are possible using or, and, & not.

```
$one = 1 
$two = 2 
$var = ( $one < $two ) and ( $one + 1 == $two ) 
```

### **Regular Expression**

Puppet supports regular expression matching using `=~ (match)` and `!~ (not-match)`.

```
if $website =~ /^www(\d+)\./ { 
   notice('Welcome web server #$1') 
}
```


像大小写和选择器正则表达式匹配一样，会为每个正则表达式创建有限的范围变量。

```
exec { "Test": 
   command => "/bin/echo now we don’t have openssl installed on machine > /tmp/test.txt", 
   unless => "/bin/which php" 
}
```

同理，我们可以使用除非，除非一直执行命令，除非unless下的命令成功退出。

```
exec { "Test": 
   command => "/bin/echo now we don’t have openssl installed on machine > /tmp/test.txt", 
   unless => "/bin/which php" 
}
```


## **19 使用模板**

当一个人希望有一个预定义的结构时使用模板，该结构将在 Puppet 的多个模块中使用，并且这些模块将分布在多台机器上。使用模板的第一步是创建一个使用模板方法呈现模板内容的模板。

```
file { "/etc/tomcat/sites-available/default.conf": 
   ensure => "present", 
   content => template("tomcat/vhost.erb")  
}
```


Puppet 在处理本地文件时几乎没有做任何假设，以加强组织和模块化。 Puppet 在模块目录中的文件夹 `apache/templates` 中查找 `vhost.erb` 模板。

## **20 定义和触发服务**

在 Puppet 中，它有一个名为 service 的资源，它能够管理在任何特定机器或环境上运行的所有服务的生命周期。服务资源用于确保服务已初始化和启用。它们也用于服务重启。

例如，在前面的tomcat 模板中，我们设置了apache 虚拟主机。如果要确保 apache 在虚拟主机更改后重新启动，我们需要使用以下命令为 apache 服务创建服务资源。

```
service { 'tomcat': 
   ensure => running, 
   enable => true 
}
```


在定义资源时，我们需要包含通知选项以触发重启。

```
file { "/etc/tomcat/sites-available/default.conf": 
   ensure => "present", 
   content => template("vhost.erb"), 
   notify => Service['tomcat']  
}
```