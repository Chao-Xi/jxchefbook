# **9 Puppet - 类型和提供者**

Puppet 类型用于单独的配置管理。 Puppet 有不同的类型，如服务类型、包类型、提供者类型等。其中每种类型都有提供者。提供者处理不同平台或工具上的配置。例如，包类型有 aptitude、yum、rpm 和 DGM 提供程序。种类很多，Puppet涵盖了一个很好的频谱配置管理项，需要管理。

Puppet 使用 Ruby 作为其基础语言。存在的所有 Puppet 类型和提供程序都是用 Ruby 语言编写的。

由于它遵循标准编码格式，因此可以简单地创建它们，如管理存储库的 repo 示例中所示。在这里，我们将创建类型 repo 和提供者的 svn 和 git。 repo 类型的第一部分是类型本身。这些类型通常存储在 lib/puppet/type 中。为此，我们将创建一个名为 repo.rb 的文件。

```
$ touch repo.rb
```


在文件中添加以下内容。

```
Puppet::Type.newtype(:repo) do  
@doc = "Manage repos"  
   Ensurable   
   newparam(:source) do 
      desc "The repo source"  
      
      validate do |value| 
         if value =~ /^git/ 
            resource[:provider] = :git 
         else 
            resource[:provider] = :svn 
         end 
      end 
      isnamevar 
   end  

   newparam(:path) do 
      desc "Destination path"  
      validate do |value| 
         unless value =~ /^\/[a-z0-9]+/ 
            raise ArgumentError , "%s is not a valid file path" % value 
         end 
      end 
   end 
end 
```

**In the above script, we have created a block `"Puppet::Type.newtype(:repo) do"` which creates a new type with the name repo.**


然后，我们有`@doc`，它有助于添加想要添加的任何级别的细节。下一个语句是 Ensurable；它创建了一个基本的 **ensure** 属性。 Puppet 类型使用 ensure 属性来确定配置项的状态。

### **Example**

```
service { "sshd": 
   ensure => present, 
} 
```

ensure 语句告诉 Puppet 除了三个方法：创建、销毁和存在于提供程序中。这些方法提供以下功能 -

* 创建资源的命令
* 删除资源的命令
* 检查资源是否存在的命令


然后我们需要做的就是指定这些方法及其内容。 Puppet 围绕它们创建支持基础设施。

接下来，我们定义一个名为 source 的新参数。

```
newparam(:source) do 
   desc "The repo source" 
   validate do |value| 
      if value =~ /^git/ 
         resource[:provider] = :git 
      else 
         resource[:provider] = :svn 
      end 
   end 
   isnamevar 
end
```

源将告诉存储库类型在哪里检索/克隆/签出源存储库。在此，我们还使用了一个名为 validate 的钩子。在提供者部分，我们定义了 git 和 svn 来检查我们定义的存储库的有效性。

最后，在代码中我们又定义了一个名为 path 的参数。

```
newparam(:path) do 
   desc "Destination path" 
   validate do |value| 
      unless value =~ /^\/[a-z0-9]+/ 
         raise ArgumentError , "%s is not a valid file path" % value 
      end 
```

这是指定将检索到的新代码放在何处的值类型。在这里，再次使用 validate 钩子创建一个检查适当性值的块。

## **Subversion Provider Use Case**

让我们从使用上面创建的类型的`Subversion Provider Use Case`开始。

```
require 'fileutils' 
Puppet::Type.type(:repo).provide(:svn) do 
   desc "SVN Support"  
   
   commands :svncmd => "svn" 
   commands :svnadmin => "svnadmin"  
   
   def create 
      svncmd "checkout", resource[:name], resource[:path] 
   end  
   
   def destroy 
      FileUtils.rm_rf resource[:path] 
   end  
    
   def exists? 
      File.directory? resource[:path] 
   end 
end
```

在上面的代码中，我们预先定义了我们需要 fileutils 库，需要我们将使用方法 from 的“fileutils”。

接下来，我们将提供者定义为 `block Puppet::Type.type(:repo).provide(:svn) do`，它告诉 Puppet 这是名为 repo 的类型的提供者。

然后，我们添加了 desc，它允许向提供程序添加一些文档。我们还定义了此提供程序将使用的命令。在下一行中，我们将检查资源的特性，如创建、删除和存在。

## **创建资源**

完成上述所有操作后，我们将创建一个资源，该资源将在我们的类和清单文件中使用，如以下代码所示。

```
repo { "wp": 
   source => "http://g01063908.git.brcl.org/trunk/", 
   path => "/var/www/wp", 
   ensure => present, 
}
```

