# **Terraform Exam(Backend/Command/Script/Others)**

## Remote Backend

1.You are working on your Terraform infrastructure with the `google-beta` provider and are trying to **store the remote state backend in Google Cloud Storage(GCS)**. What argument would you use to properly configure the backend?

> **`data "terraform_remote_state" "foo" { }`**
> 
> **`data "terraform_remote_state"`**

2.You are going through the Terraform state locking process to **prevent others from acquiring your lock and potentially corrupting state**. However, Terraform is not continuing due to **automatic state locking failing**. How could you fix this issue?

> Manually unlock the state with the `force-unlock` command


3.What stores a state as a given key in a given bucket on Amazon S3 and provides support for **state locking and consistency checking via DynamoDB**?

> **The S3 backend**

4.You are trying to use the terraform validate command and you need an initialized working directory with any referenced plugins and modules installed. What command could you use to **initialize a working directory for validation without accessing any configured remote backend**?

> **`terraform init -backend=false`**

5.What is the difference between a **remote backend and a local backend**?

> **Local backends store files in a local JSON file on disk, while remote backends allow you to store the state file in a remote, shared store**
> 
> Local backends: store files in a local JSON file on disk
> 
> Remote backends allow you to store the state file in a remote, shared store

6.Which mechanism retrieves **state data** from a remote data store?

> **`terraform_remote_state`**

7.You are trying to collaborate with remote state to improve the quality of your team's workflow. You have configured an **S3 bucket policy and would like to enable remote state**. What script could you use as a template to accomplish this?

```
terraform {
	backend "s3" {
		encrypt = true
		bucket  =  "terraform-remote-state-storage"
		region  = "us-east-1"
		key     = terraform/state
		dynamo_table = "terraform-state-lock"
	}
}
```

7.What is the purpose of running Terraform through the Google Cloud Platform Cloud Shell?

> **To enable the usage of tools from both Terraform and it's GCP provider resources, as well as maintaining optimal use of GCP services and features**.

8.What is remote state a feature of?

> **Backends**

9.What service stores a state as an object in a configurable prefix in a given bucket on **Google Cloud Storage**, and supports state locking?

> **The GCS backend**

## **Terraform command**


1.You are using **Terraform and want to create a new workspace from a pre-existing local state file**. What script could you use as a template to accomplish this?


> `-state=old.terraform.tfstate example`

```
terraform workspace new -state=old.terraform.tfstate example
```

2.What is the function of the **terraform console** command?

* command provides an interactive console for evaluating expressions.
* **It allows you to experiment with the behavior of Terraform built-in functions.**

3.When using the **`terraform refresh` command**, what is the **purpose of the `-var "foo=bar"`** flag?

> **Setting a variable in the Terraform configuration**

4.You are trying to **taint** resources in your deployment with the **`terraform taint` command.** The command is occasionally not succeeding due
to some of your resources being missing. How can you prevent this failure?

> **Specify the `-allow-missing` command-line flag**

5.You write a new Terraform configuration and are using the `terraform init` command. You are having issues because Terraform is searching for **default plugin locations**, but you want it to only search for a **specified path**. What can you do to fix this issue?

```
Run `terraform init` with the `-plugin-dir=<PATH>`
option and a non-empty<PATH>/
```

6.You are initializing a Terraform configuration with **the `init` command** and you need a **given module to be copied into a target directory** before
any other initialization steps are run. What option could you use to accomplish this?

```
from-module=MODULE-SOURCE
```

7.What command accepts all the arguments and flags that the apply command accepts, with the exception of a plan file argument?

**The `terraform destroy` command**

8.Company A suffered a major security breach after saved plan files containing hundreds of variables with secrets were easily accessed. In hindsight, what Terraform fact, if known, could have helped prevent this issue from occurring?

> **For plan files that are saved with the `-out` flag, Terraform itself does not encrypt the plan file**

9.You are importing an AWS security group which imports an `aws_security_group` and a `aws_security_group` rule for each rule. However, you run into an issue as Terraform planned to destroy some of your imported objects in the next run. What can you do to prevent the objects being destroyed?

> **Consult the import output and create a resource block in your configuration for each secondary resource**


## **Terraform script**

1.You are utilizing source control to enable code storage in your Terraform infrastructure with the Azure Provider, and are configuring a Source Control token. You run the command resource

```
"azure_app_service_control_source_token" "example" {type ="GitHub" token ="7e57735e77e577e57"}
```

but encounter an invalid command prompt. What is the correct command to run?

> **Answer**

```
resource "azurerm app_service_source_control_token"  "example" {type="GitHub" token="7e57735677e577e57"}
```

2.You are trying to successfully specify required provider versions. You want to specify a version **that is greater than the version number "2.7.0"** What script could you use as a template to accomplish this?

```
terraform {
    required_providers {
        aws = ">2.7.0" 
    }
}
```


3.You are using count loops in a Terraform configuration and you want to use count to get the index of each iteration in the loop. What script could you use to accomplish this?

```
resource "awe_iam_user" "example" {
   count  = 3
   name   = "neo.${count.index}"
} 
```

4.You are going over the process of merging `variable` blocks. Your original variable block defines a `default` value and your override block changes the variable's `type`. After Terraform attempts to convert the default value to the overridden type. you receive an error. Why?

> **The conversion is not possible due to incompatible types.**


5.You are working on your Terraform infrastructure, and are adding and configuring the Azure Provider. What argument would you use to instantiate the Azure provider?


> **`provider "azurerm" { }`**

6.You are declaring an input variable in Terraform, however, after writing the below script you realize that Terraform is not letting you use source as a
variable name. Why? 

`variable "source" { type = string }`

> **The "source" term is reserved for meta-arguments in module configuration blocks**


7.You are dealing with outputs in Terraform and require creating additional explicit dependencies. What argument would accomplish your goal?

> **`depends_on`**

8.You are utilizing source control to enable code storage in your Terraform infrastructure with the Azure Provider, and are configuring a Source
Control token. You run the command 

```
resource "azure_app_service_control_source_token" "example" { type = "GitHub" token =
"7e57735e77e577e57"
``` 

but encounter an invalid command prompt. What is the correct command to run?

```
resource "azure_app_service_control_source_token" "example" { type="GitHub" token= "7e57735e77e577e57" }
```

9.You are using the **file provisioner and want to copy the string in content into `/tmp/file.log`**, What script could you use to accomplish this?

```
provisioner "file" {
	...
	destination = "/tmo/file.log"
}
```

10.You are new to using Hashicorp Configuration Language and want to include **multi-line strings**. What can you do to accomplish this?

> **Use an opening <<EOF, followed by a closing EOF on its own line**.

11.What happens when you call a child module from your Terraform script?

> **The contents of that module are included in the configuration with specific values for its input variables**

## **Data Resource**


1.You are pulling information about an existing virtual network using the azurerm virtual network data source in Terraform with the Azure Provider. What command would you run to utilize the data source?

```
data "azurerm_virtual_network" "example"
{
    name = "production"
    resource_group_name = "networking"
}

```


2.You are using the **`template_file` data source** to read a file at a given path and have its contents rendered as a template using a supplied set
of template variables. In the past, you've had problems with the syntax of this command. **What script example could you use as a template to help
prevent** this from occurring again?

```
data "template_file" "init" {
	template = "${file("${path.module}/init.tpl")}"
	vars = {
		consul_address = "${aws_instance.consul.private_ip}"
	}
}
```

> **`$`sign is pretty important**


4.You are pulling information about an existing virtual network using the `azurerm_virtual_network` data source in Terraform with the Azure Provid What command would you run to utilize the data source?

```
data "azurerm_virtual_network" "example"
{
	name = "production"
	resource_group_name = "networking"
}
```

5.Using the AzureRM Provider, which blocks of code creates a Resource Group in the West Europe region?

```
provider "azurerm"
	version = "=2.20.0"
  features {}
}

resource "azurerm_resource_group" "example"{

	name = "example-resources"
	location = "West Europe"
}
```

6.You are trying to create multiple instances of the **google-beta** provider within your Terraform infrastructure, and pass the command

```
provider "google-beta" {credentials = "{file ("account.jsile")}" project = "my-project-id" region = "us-centrall"} 
```

To enable the additional provider google-beta, but you are encountering an issue where your instance is receiving invalid credentials. **What command
should you run to correctly** initialize the google beta provider?

```
provider "google-beta" {credentials = "(file("account.json")}" project= "my-project-id" region="us-centrall"}
```

7.Which Terraform functionality gives you the ability to connect to the Azure Provider and enable access to information through the source?

> **`Data Source`**

8.What does the **coalesce** function do?

> **Takes any number of arguments and returns the first one that is not null or an empty string**

9.What is the purpose of utilizing multiple instances of the AWS provider?

> **To support multiple regions for a cloud platform, as well as multiple Docker hosts, Consul hosts, and to select which one to use on a per-resource basis**

10.Which mechanism retrieves state data from a remote data store?

> **`terraform_remote_state`**

11.To write **data** that reads from the `"aws_ami"` data source and exports to "example." which script template could you use?

```
data "aws_ami" "example" {
		most_recent = true
		Owners      = ["self"]
		tags        = {
			Name   = "app-server"
		  Tested =  "true"
		}
}
```




## Others


1.You are importing an AWS security group which imports an `aws_security_group` and a `as_security_group_rule` for each rule. However you run into an issue as Terraform planned to destroy some of your imported objects in the next run. What can you do to prevent the objects being destroyed?

> **Consult the import output and create a resource block in your configuration for each secondary resource**


2.Which **provisioner would you use to copy files** or directories from the host machine to newly created resources?

> **The File provisioner**

3.You are working on your Terraform infrastructure and are trying to debug your code. **You want to make the most verbose logs** appear so you
efficiently debug your configuration. How can you accomplish this?

> Set the `TF_LOG` environment variable to a positive value and set the level to **TRACE verbosity**

4.You are working on your Terraform infrastructure and are adding and configuring the `google-beta` provider. What argument would you use to instantiate the google-beta provider?

**`provider "google-beta"`**

5.You declare the following variables:

```
variable "red" {}
variable "blue" { default="false"}
```

red is mandatory and blue is assigned with the default value false. How can you override the "blue" value?

> **Set the "blue" variable to a new value `export TF_VAR_bar=newvalue`** and then run terraform in that session

6.You are using the **`terraform plan`** command and need to **save your generated plan to a file for later execution**. What argument would best suit your needs?

> **`-out`**

7.You are pulling information for your Terraform infrastructure while utilizing the AWS provider. You want to use the aws instance data source to get the ID of an Amazon EC2 instance, but are encountering an issue where you have the incorrect instance ID. What can you do to fix this issue?

> Ensure you have configured the data source with the proper instance **ID using the argument `instance_id = "instanceid"`**

8.What are some of the methods of authenticating Azure Terraform?

> **Service Principal and Client Certificate**

9.If you needed to deploy **AWS resources** using Terraform, where would you find an appropriate provider for the task?

**`Terraform Registry`**

10.You need to **override a portion of an existing configuration object in a separate file**. What can you end your configuration files in to achieve this?

> **`_override.tf` or `_override.tf.json`**

11.Your team is going through the configuring provisioner connection settings process and wants to provide multiple connections to have an initial provisioner connect as the root user to set up user accounts, but does not know how to copy the file as the root user using SSH. What script could you use as a template to fix this problem?

```
provisioner "file" {
			source  =  "conf/myapp.conf"
	destination =   "/etc/myapp.conf"
	
	connection  {
			type   =  "ssh"
			user   =  "root"
			password = "${var.root_password}"
			host     = "${var.host}"
 }
}


12.What is terraform distributed as?

**`A single binary`**

