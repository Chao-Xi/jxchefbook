# **解决Terraform初始化慢~配置本地离线源**

需要手动或者terraform init一次下载， 然后缓存。后续直接使用缓存。

本次实践使用的是Linux/Mac 系统，如果是windows系统有两点不同的配置。


* CLI配置文件的名称为`terraform.rc`
* `plugin_cache_dir: D:/xxx/xxx`

### **1. 创建配置文件**

**`.terraformrc`是Terraform CLI的配置文件**


```
plugin_cache_dir  = "$HOME/.terraform.d/terraform-plugin-cache" 
disable_checkpoint = true
```

* `plugin_cache_dir` 是插件的缓存目录（此目录需要提前创建不然init报错）
* `disable_checkpoint` 禁用 需要连接HashiCorp 提供的网络服务的升级和安全公告检查

```
mkdir -p $HOME/.terraform.d/terraform-plugin-cache
```

文件创建好了之后， 要通过配置`TF_CLI_CONFIG_FILE`变量，让TerraformCLI可以加载到配置文件。**这个变量的值没有固定配置，而是取决于`.terraformrc`文件路径**。

```
export TF_CLI_CONFIG_FILE=$HOME/Desktop/terraform/terraform-module-example/.terrafo
rmrc
```

## **2. 进行初始化**

插件下载方式有两种：

通过 `terraform init` 自动下载provider 插件；

登入`registry.terraform.io` 手动到 GitHub下载，并按照目录结构存放到`plugin_cache_dir`;

本次演示先使用`terraform init`进行操作， 如果手动到registry下载，需要按照目录结构存放；

```
terraform init
Initializing modules...
- mydns in ../../modules/dns
- myecs in ../../modules/ecs
- myssecgroup in ../../modules/secgroup
- myvpc in ../../modules/vpc

Initializing the backend...

Initializing provider plugins...
- Finding hashicorp/alicloud versions matching "1.164.0"...
- Installing hashicorp/alicloud v1.164.0...
- Installed hashicorp/alicloud v1.164.0 (signed by HashiCorp)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.
```

初始化之后， 查看`plugin_cache_dir`中的内容：
`$HOME/.terraform.d/terraform-plugin-cache/registry.terraform.io/hashicorp/alicloud/1.164.0/darwin_arm64`

```
➜  .terraform.d pwd
/Users/lizeyang/.terraform.d

➜  .terraform.d tree
.
|____checkpoint_cache
|____checkpoint_signature
|____terraform-plugin-cache
| |____registry.terraform.io
| | |____hashicorp
| | | |____alicloud
| | | | |____1.164.0
| | | | | |____darwin_arm64
| | | | | | |____terraform-provider-alicloud_v1.164.0
```

## **3. 模拟断网，离线初始化**

方法1：初始化时指定`plugin-dir`

`terraform init --plugin-dir $HOME/.terraform.d/terraform-plugin-cache/`

```

terraform init  --plugin-dir $HOME/.terraform.d/terraform-plugin-cache/
Initializing modules...
- mydns in ../../modules/dns
- myecs in ../../modules/ecs
- myssecgroup in ../../modules/secgroup
- myvpc in ../../modules/vpc

Initializing the backend...

Initializing provider plugins...
- Finding hashicorp/alicloud versions matching "1.164.0"...
- Using hashicorp/alicloud v1.164.0 from the shared cache directory

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!
```

**方法2：定义Terraform插件使用本地mirror**

```

provider_installation {
  filesystem_mirror {
    path    = "/Users/lizeyang/.terraform.d/terraform-plugin-cache"
    include = ["registry.terraform.io/*/*"]
  }
}
```

```
➜  dev terraform init
Initializing modules...

Initializing the backend...

Initializing provider plugins...
- Finding hashicorp/alicloud versions matching "1.164.0"...
- Using hashicorp/alicloud v1.164.0 from the shared cache directory

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!
```


到此就完成了terraform离线本地源的配置了， 除了这种方式外其实也可以基于terraform开放的HTTP API协议，使用Python Flask写一个registry server。