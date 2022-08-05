# **7 Puppet - Function**


## **1 Function**

Puppet 支持任何其他编程语言的功能，因为 Puppet 的基本开发语言是 Ruby。它支持两种类型的函数，**称为语句statement和rvalue**。

* **Statements**代表，它们没有任何返回类型。它们用于执行独立任务，例如在新清单文件中导入其他 Puppet 模块。
* Rvalue 返回值，并且只能在语句需要值时使用，例如赋值或 case 语句

Puppet 中函数执行的关键在于，它只在 Puppet master 上执行，它们不在客户端或 Puppet 代理上执行。

因此，他们只能访问 Puppet Master 上可用的命令和数据。已经存在不同类型的功能，甚至用户也有权根据需要创建自定义功能。下面列出了一些内置函数。

### **文件功能**

文件资源的文件功能是在 Puppet 中加载一个模块，并以字符串的形式返回所需的输出。它查找的参数是 `<module name>/<file>` 引用，它有助于从 Puppet 模块的文件目录加载模块。

像 `script/tesingscript.sh` 会从 `<module name>/script/files/testingscript.sh` 加载文件。

函数具有读取和接受绝对路径的能力，这有助于从磁盘上的任何位置加载文件。


### **包含函数**


在 Puppet 中，include 函数与任何其他编程语言中的 include 函数非常相似。它用于声明一个或多个类，从而评估这些类中存在的所有资源，最后将它们添加到目录中。它的工作方式是，include 函数接受类名、类列表或以逗号分隔的类名列表。



使用 include 语句时要记住的一件事是，它可以在一个类中多次使用，但有一个限制，即只能包含一个类。

如果包含的类接受参数，则包含函数将使用 `<class name>::<parameter name>` 作为查找键自动查找它们的值。


包含函数不会导致类在声明时包含在类中，因为我们需要使用包含函数。它甚至不会在声明的类和围绕它的类中创建依赖关系。


**在 include 函数中，只允许使用类的全名，不允许使用相对名称。**

### **定义函数**

在 Puppet 中，定义的函数有助于确定给定类或资源类型的定义位置，并返回布尔值或不返回。还可以使用define 来确定是否定义了特定资源或定义的变量是否具有值。

使用定义函数时要记住的关键点是，该函数至少接受一个字符串参数，可以是类名、类型名、资源引用或“$name”形式的变量引用。

定义本机和已定义函数类型的函数检查，包括模块提供的类型。类型Type和类Class由它们的名称匹配。该函数通过使用资源引用匹配资源减速。

**Define Function Matches**

```
# Matching resource types 
defined("file") 
defined("customtype")  

# Matching defines and classes 
defined("testing") 
defined("testing::java")  

# Matching variables 
defined('$name')  

# Matching declared resources 
defined(File['/tmp/file']) 
```

## **2 自定义函数**

函数为用户提供了开发自定义函数的特权。 Puppet 可以通过使用自定义函数来扩展其解释能力。自定义函数有助于增加和扩展 Puppet 模块和清单文件的功能。


### **编写自定义函数**

在编写函数之前，有几件事情需要记住。


* 在 Puppet 中，函数由编译器执行，这意味着所有函数都在 Puppet master 上运行，它们不需要处理任何 Puppet 客户端。功能只能与代理交互，提供的信息是事实的形式。
* Puppet master 捕获自定义函数，这意味着如果对 Puppet 函数进行一些更改，则需要重新启动 Puppet master。
* 函数将在服务器上执行，这意味着该函数需要的任何文件都应该存在于服务器上，如果该函数需要直接访问客户端计算机，则无法执行任何操作。
* 完全有两种不同类型的函数可用，一种是返回值的 Rvalue 函数和不返回任何内容的语句函数。
* 包含函数的文件名应与文件中的函数名相同。否则，它不会自动加载


### **放置自定义函数的位置**


所有自定义函数都实现为单独的 .rb 文件并分布在模块之间。需要将自定义函数放在 `lib/puppet/parser/function` 中。可以从以下位置从 .rb 文件加载函数。

* `$libdir/puppet/parser/functions`
* `puppet/parser/functions sub-directories in your Ruby $LOAD_PATH`


### **创建新函数**


使用 `puppet::parser::Functions` 模块中的 newfunction 方法创建或定义新函数。需要将函数名称作为符号传递给 newfunction 方法，并将代码作为块运行。以下示例是一个函数，用于将字符串写入 /user 目录内的文件。

```
module Puppet::Parser::Functions 
   newfunction(:write_line_to_file) do |args| 
      filename = args[0] 
      str = args[1] 
      File.open(filename, 'a') {|fd| fd.puts str } 
   end 
end
```

Once the user has the function declared, it can be used in the manifest file as shown below.

```
write_line_to_file('/user/vipin.txt, "Hello vipin!")
```