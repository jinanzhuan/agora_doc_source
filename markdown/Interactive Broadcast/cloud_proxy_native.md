---
title: 使用云代理服务
platform: All Platforms
updatedAt: 2019-07-12 15:35:51
---
## 功能简介
对于安全需求较高的企业用户，设置防火墙可以限制员工访问非认可的网站，保护内部信息安全。

为避免这些企业因防火墙无法使用 Agora 的服务，Agora 开发了云代理服务。用户只需要在防火墙上将特定的 IP 及端口列入白名单，就可以实现内网访问 Agora 服务。

相比配置单一的代理服务器，云代理服务更灵活稳定，因此在大型企业、医院、高校、金融等安全需求较高的机构内都有广泛的应用。


## 实现方法

Native SDK 2.4.0 及以上版本支持云代理服务，开始前请确认你已集成 SDK。

1. 联系 support@agora.io，并提供代理服务使用区域、并发规模、网络运营商等信息。
2. 将以下测试 IP 及端口添加到企业防火墙的白名单。

	| 协议 | 目标地址      | 端口                    | 端口用途     |
| ---- | ------------- | ----------------------- | ------------ |
| TCP  | 120.92.118.34 | 4000                    | 消息数据传输 |
| TCP  | 120.92.18.162 | 4000                    | 消息数据传输 |
| TCP  | 47.74.211.17  | 1080, 8000, 25000, 9700 | 边缘节点通信 |
| TCP  | 52.80.192.229 | 1080, 8000, 25000, 9700 | 边缘节点通信 |
| TCP  | 52.52.84.170  | 1080, 8000, 25000, 9700 | 边缘节点通信 |
| TCP  | 47.96.234.219 | 1080, 8000, 25000, 9700 | 边缘节点通信 |
| UDP  | 120.92.118.34 | 4500 - 4650             | 媒体数据交换 |
| UDP  | 120.92.18.162 | 4500 - 4650             | 媒体数据交换 |
| UDP  | 47.74.211.17  | 1080, 8000, 25000, 9700 | 边缘节点通信 |
| UDP  | 52.80.192.229 | 1080, 8000, 25000, 9700 | 边缘节点通信 |
| UDP  | 52.52.84.170  | 1080, 8000, 25000, 9700 | 边缘节点通信 |
| UDP  | 47.96.234.219 | 1080, 8000, 25000, 9700 | 边缘节点通信 |
	 
<div class="alert note">以上 IP 仅供测试阶段调试使用，正式上线前需要向 Agora 申请独立的云代理服务资源。</div>
		 
3. 在 Agora SDK 内调用如下接口，测试是否能正常使用

	```
	setParameters("{\"rtc.proxy_server\":[proxy_type, \"ip or dns\", port]}");
	```

其中：
* `proxy_type`：代理服务器类型
* `ip or dns`：代理服务器类型所对应的 IP 或域名
* `port`：代理服务器的端口

不同的代理服务器类型，对应的 IP（域名）和端口也不同：

| `proxy_type`                                                 | `ip or dns`                                         | `port`                        |
| ------------------------------------------------------------ | --------------------------------------------------- | ----------------------------- |
| 0：自行部署 Socks5 类型的代理服务器                          | 该代理的 IP                                         | 该代理的端口                  |
| 1：使用云代理服务，配置域名（推荐使用） | ap-proxy.agora.io                                   | 填 0 即可                     |
| 2：使用云代理服务，配置IP列表（域名受限时可使用） | 该代理的 IP 列表，格式为<br/> `"[\"ip1\",\"ip2\"]"` | 该 lbs 的连接端口，<br>默认为 0 |
| 4：不启用代理服务                                            | N/A                                                 | N/A                           |

**代码示例**

**`proxy_server` = 0：**
```
setParameters("{\"rtc.proxy_server\":[0, \"127.0.0.1\", 1080]}");
```
**`proxy_server` = 1：**
```
setParameters("{\"rtc.proxy_server\":[1, \"ap-proxy.agora.io\", 0]}");
```
**`proxy_server` = 2：**
```
setParameters("{\"rtc.proxy_server\":[2, \"[\"192.168.0.112\",\"127.0.0.1\"]\", 0]}");
```
**`proxy_server` = 4：**
```
setParameters("{\"rtc.proxy_server\":[4, \"\", 0]}");
```

4. 测试完成后，Agora 会为你部署云代理服务正式环境，并提供相应的 IP（域名）和端口。将 Agora 提供的 IP 和端口添加到企业防火墙的白名单上。

## 工作原理

Agora 云代理的工作原理如下：
![](https://web-cdn.agora.io/docs-files/1543290381396)

* Agora SDK 在连接 Agora SD-RTN 之前，向云代理发起请求；
* 云代理返回相应代理信息；
* Agora SDK 向云代理发送数据，云代理将接收到的数据透传给 Agora SD-RTN；
* Agora SD-RTN 向云代理返回数据，云代理再将接收到的数据发送给 Agora SDK。