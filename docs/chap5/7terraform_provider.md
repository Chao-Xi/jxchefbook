# Terraform provider update


https://www.terraform.io/internals/provider-registry-protocol

```
$ terraform init
Initializing modules...

Initializing the backend...
...
│ Error: Invalid legacy provider address
│ 
│ This configuration or its associated state refers to the unqualified provider "azurerm".
│ 
│ You must complete the Terraform 0.13 upgrade process before upgrading to later versions.
```

```
$ terraform providers

Providers required by configuration:
.
├── provider[registry.terraform.io/hashicorp/azurerm] 2.10.0
├── module.storageaccount
│   └── provider[registry.terraform.io/hashicorp/azurerm]
├── module.subnet
│   └── provider[registry.terraform.io/hashicorp/azurerm]
├── module.cdn
│   └── provider[registry.terraform.io/hashicorp/azurerm]
└── module.database
    └── provider[registry.terraform.io/hashicorp/azurerm]

Providers required by state:

    provider[registry.terraform.io/-/azurerm]
```


```
$ terraform state replace-provider registry.terraform.io/-/azurerm registry.terraform.io/hashicorp/azurerm
Acquiring state lock. This may take a few moments...
Terraform will perform the following actions:

  ~ Updating provider:
    - registry.terraform.io/-/azurerm
    + registry.terraform.io/hashicorp/azurerm
 ...
```

```
$ terraform state replace-provider registry.terraform.io/-/azurerm registry.terraform.io/hashicorp/azurerm
```




```
 terraform init 
Initializing modules...

Initializing the backend...

Initializing provider plugins...
- Finding hashicorp/aws versions matching "~> 2.70"...
- Finding latest version of hashicorp/azurerm...
- Installing hashicorp/aws v2.70.1...
- Installed hashicorp/aws v2.70.1 (signed by HashiCorp)
- Installing hashicorp/azurerm v3.1.0...
- Installed hashicorp/azurerm v3.1.0 (signed by HashiCorp)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.
```


```
# This file is maintained automatically by "terraform init".
# Manual edits may be lost in future updates.

provider "registry.terraform.io/hashicorp/aws" {
  version     = "2.70.1"
  constraints = "~> 2.70"
  hashes = [
    "h1:B+Cs8HwWnLn9ymYI/r3B5S10w1+hOD5WmfFxiLHTUIA=",
    "zh:04137cdf128cf21dcd190bbba4d4bba43c7868c52ad646b0eaa54a8b8b8160a7",
    "zh:30c9f956133a102b4a426d76dd3ef1a42332d9875261a06aa877409aa6b2b556",
    "zh:3107a43647454a3d6d847fba6aa593650af0f6a353272c04450408af5f4d353a",
    "zh:3f17285478313af822447b453fa4e37f30ef221f0b0e8f2e4655f1ac9f9de1a2",
    "zh:5a626f7a3c4a9fea3bdfde63aedbf6eea73760f3b228f776f1132b61d00c7ff2",
    "zh:6aafc9dd79b511b9e3d0ec49f7df1d1fd697c3c873d1d70a2be1a12475b50206",
    "zh:6fb29b48ccc85f7e9dfde3867ce99d6d65fb76bea68c97d404fae431758a8f03",
    "zh:c47be92e1edf2e8675c932030863536c1a79decf85b2baa4232e5936c5f7088f",
    "zh:cd0a4b28c5e4b5092043803d17fd1d495ecb926c2688603c4cdab4c20f3a91f4",
    "zh:fb0ff763cb5d7a696989e58e0e4b88b1faed2a62b9fb83f4f7c2400ad6fabb84",
  ]
}

provider "registry.terraform.io/hashicorp/azurerm" {
  version = "3.1.0"
  hashes = [
    "h1:XuBendit3GIKmev2A9YpdxDxBO+FeUxnzrcPNLK/aVY=",
    "zh:39f925413e88c608f5319e01a540505d5ac4a1aa3073ad31823f5e9f8a00aafa",
    "zh:3a4d42ee0b1b5ae76949a25b2d2ca11ac98f3ae483b407e56d48ba4bd6f249be",
    "zh:5f220c18ebf2f848450b34f1f12a946e3e7b0585c7681eb6135be7e4658d8c0c",
    "zh:7e176275fbb87987ef84515a588b4a5a5a692b3c2ae31171cbec0a0c977c8e64",
    "zh:8980a4f2aca62b3833c12999abdc090a850a22cb88ef7e80fe6f33d6e688dc78",
    "zh:a3c5da6f59afd566b77b789bfc2d4acddc7bf4c777fab9fc2483c1580143e1e0",
    "zh:a9db6d2aec9bb17f08f3bac940ae313dd8d9e83726e71b27daeffca39eca7e67",
    "zh:db5d31bade0ee02d33f620b266de435e630defd91f0b79398b94163fdaa6a3af",
    "zh:e6d22de2fc2cd61a2d72d306d62c212aedc2667564e02915723f92775289cda5",
    "zh:e92b6e64601cffbf891558047a6effeaacb1180df993270ecbab2a9f7f964c03",
    "zh:fd40d50a64109b26f6db0178ce4a2f78e81a709ad4b1728c7d923b5e5c551cde",
  ]
}
```