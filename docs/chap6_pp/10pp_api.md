# **10 Puppet - RESTful API**

Puppet 使用 RESTful API 作为 Puppet master 和 Puppet 代理之间的通信通道。以下是访问此 RESTful API 的基本 URL。

```
https://brcleprod001:8140/{environment}/{resource}/{key}
https://brcleprod001:8139/{environment}/{resource}/{key}
```

## **REST API 安全**

Puppet 通常负责安全性和 SSL 证书管理。但是，如果希望在集群外使用 RESTful API，则需要在尝试连接到机器时自行管理证书。 Puppet 的安全策略可以通过 rest authconfig 文件进行配置。

### **测试 REST API**

Curl 实用程序可用作休息 RESTful API 连接的基本实用程序。以下是我们如何使用 REST API curl comman 检索节点目录的示例

```
curl --cert /etc/puppet/ssl/certs/brcleprod001.pem --key
/etc/puppet/ssl/private_keys/brcleprod001.pem
```

在下面的一组命令中，我们只是设置 SSL 证书，这将根据 SSL 目录的位置和正在使用的节点的名称而有所不同。例如，让我们看一下以下命令。

```
curl --insecure -H 'Accept: yaml'
https://brcleprod002:8140/production/catalog/brcleprod001
```


在上面的命令中，我们只需发送一个标头，指定我们想要返回的格式和一个用于在生产环境中生成 brcleprod001 目录的 RESTful URL，将生成以下输出。

```
--- &id001 !ruby/object:Puppet::Resource::Catalog
aliases: {}
applying: false
classes: []
...
```

让我们假设另一个例子，我们想从 Puppet Master 那里取回 CA 证书。它不需要使用自己签名的 SSL 证书进行身份验证，因为这是在进行身份验证之前需要的东西。

```
curl --insecure -H 'Accept: s' https://brcleprod001:8140/production/certificate/ca

-----BEGIN CERTIFICATE-----
MIICHTCCAYagAwIBAgIBATANBgkqhkiG9w0BAQUFADAXMRUwEwYDVQQDDAxwdXBw
```


### **Puppet Master 和代理共享 API 参考**

```
GET /certificate/{ca, other}

curl -k -H "Accept: s" https://brcelprod001:8140/production/certificate/ca
curl -k -H "Accept: s" https://brcleprod002:8139/production/certificate/brcleprod002
```


## **2 Puppet Master API 参考**


经过身份验证的资源（需要有效的签名证书）

### Catalogs

```
GET /{environment}/catalog/{node certificate name}

curl -k -H "Accept: pson" https://brcelprod001:8140/production/catalog/myclient
```

### Certificate Request

```
GET /{environment}/certificate_requests/{anything} GET
/{environment}/certificate_request/{node certificate name}

curl -k -H "Accept: yaml" https://brcelprod001:8140/production/certificate_requests/all
curl -k -H "Accept: yaml" https://brcleprod001:8140/production/certificate_request/puppetclient
```

### Reports Submit a Report

```
PUT /{environment}/report/{node certificate name}
curl -k -X PUT -H "Content-Type: text/yaml" -d "{key:value}" https://brcleprod002:8139/production
```

### Node − Facts Regarding a Specific Node

```
GET /{environment}/node/{node certificate name}

curl -k -H "Accept: yaml" https://brcleprod002:8140/production/node/puppetclient
```

### Status − Used for Testing

```
GET /{environment}/status/{anything}

curl -k -H "Accept: pson" https://brcleprod002:8140/production/certificate_request/puppetclient
```


## Puppet 代理 API 参考

在任何机器上设置新代理时，默认情况下 Puppet 代理不会侦听 HTTP 请求。它需要通过在 puppet.conf 文件中添加`“listen=true”`在 Puppet 中启用。

这将使 Puppet 代理能够在 Puppet 代理启动时侦听 HTTP 请求。

### **Facts**

```
GET /{environment}/facts/{anything}

curl -k -H "Accept: yaml" https://brcelprod002:8139/production/facts/{anything}
```

### Run − Causes the client to update like puppetturn or puppet kick.

```
PUT /{environment}/run/{node certificate name}

curl -k -X PUT -H "Content-Type: text/pson" -d "{}"
https://brcleprod002:8139/production/run/{anything}
```




