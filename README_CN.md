## APISIX

[![Build Status](https://travis-ci.org/iresty/apisix.svg?branch=master)](https://travis-ci.org/iresty/apisix)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://github.com/iresty/apisix/blob/master/LICENSE)

- **QQ 交流群**: 552030619

## 什么是 APISIX？

APISIX 是一个基于云原生、高速可扩展的开源微服务网关节点实现，其自身主要优势是高性能和强大的扩展性。

## 安装

APISIX 在以下操作系统中做过安装和运行测试:

|OS          |Status|
|------------|------|
|CentOS 7    |√     |
|Ubuntu 18.04|√     |
|Debian 9    |√     |

现在有两种方式来安装: 如果你是 CentOS 7 的系统，推荐使用 RPM 包安装；其他的系统推荐使用 Luarocks 安装。


#### 通过 RPM 包安装（CentOS 7）
```shell
sudo yum install yum-utils
sudo yum-config-manager --add-repo https://openresty.org/package/centos/openresty.repo
sudo yum install -y openresty etcd
sudo service etcd start

sudo yum install -y https://github.com/iresty/apisix/releases/download/v0.4/apisix-0.4-0.el7.noarch.rpm
```

如果安装成功，就可以参考 [**快速上手**](#快速上手) 来进行体验。如果失败，欢迎反馈给我们。


### 通过 Luarocks 安装

#### 依赖项

APISIX 是基于 [openresty](http://openresty.org/) 之上构建的, 配置数据的存储和分发是通过 [etcd](https://github.com/etcd-io/etcd) 来完成。

我们推荐你使用 [luarocks](https://luarocks.org/) 来安装 APISIX，不同的操作系统发行版本有不同的依赖和安装步骤，具体可以参考: [Install Dependencies](https://github.com/iresty/apisix/wiki/Install-Dependencies)

#### 安装 APISIX

```shell
sudo luarocks install apisix
```

如果一些顺利，你会在最后看到这样的信息：
> apisix is now built and installed in /usr (license: Apache License 2.0)

恭喜你，APISIX 已经安装成功了。


## 快速上手

1. 启动 APISIX

```shell
sudo apisix start
```

2. 测试限流插件

为了方便测试，下面的示例中设置的是 60 秒最多只能有 2 个请求，如果超过就返回 503：

```shell
curl http://127.0.0.1:2379/v2/keys/apisix/routes/1 -X PUT -d value='
{
	"methods": ["GET"],
	"uri": "/index.html",
	"id": 1,
	"plugin_config": {
		"limit-count": {
			"count": 2,
			"time_window": 60,
			"rejected_code": 503,
			"key": "remote_addr"
		}
	},
	"upstream": {
		"type": "roundrobin",
		"nodes": {
			"39.97.63.215:80": 1
		}
	}
}'
```

```shell
$ curl -i http://127.0.0.1:9080/index.html
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 13175
Connection: keep-alive
X-RateLimit-Limit: 2
X-RateLimit-Remaining: 1
Server: APISIX web server
Date: Mon, 03 Jun 2019 09:38:32 GMT
Last-Modified: Wed, 24 Apr 2019 00:14:17 GMT
ETag: "5cbfaa59-3377"
Accept-Ranges: bytes

...
```

## 性能测试
使用谷歌云的 4 核心服务器来运行 APISIX，QPS 可以达到 60000，同时延时只有 0.5 毫秒。

你可以看出[性能测试文档](doc/benchmark-cn.md)来了解更多详细内容。


## 开发文档
[详细设计文档](doc/architecture-design-cn.md)

## 参与社区

如果你对 APISIX 的开发和使用感兴趣，欢迎加入我们的 QQ 群来交流:

<img src="doc/images/qq-group.png" width="302" height="302">


## 致谢
灵感来自 Kong
