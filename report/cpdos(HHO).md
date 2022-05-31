### 记录一下aused by the web cache poisoning denial of service(cpdos)

------

### 0x00 回顾一下

没有实战挖掘过Web缓存投毒，今天遇到了，记录一下，关于Web缓存中毒的原理可以参考portswigger社区里的这篇文章：

`https://portswigger.net/research/practical-web-cache-poisoning`

漏洞检测我使用的是Burp suite的插件`Param-miner`，Burp商店可以直接安装，工具提供了检测和利用(支持自动化)

![image-20220506164554877](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220506164554877.png)

放张配置图

![image-20220506164732178](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220506164732178.png)

### 0x01 案例

案例来自于hackerone爱彼迎项目，下面演示的是web cache poisoning denial of service(cpdos)中的HTTP Header Oversize(HHO)情况，CP-DoS分三种情况，既：

- HTTP超大标头(HHO)
- HTTP元字符(HMC)
- HTTP方法覆盖(HMO)

原理如下图：![image-20220506164927254](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220506164927254.png)

Param-miner插件探测出缓存键：x-forwarded-for

![image-20220506164934960](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220506164934960.png)

为了防止存下的缓存被其它用户访问，测试的时候可以手动添加一个特定的缓存键，比如请求一个不存在文件lbwnb

![image-20220506164940225](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220506164940225.png)

HHO的攻击原理构造会超过header大小的限制，让web服务器拦截请求并返回错误页面，状态为400 Bad Request的错误页面就保存在缓存中了，随后所有攻击资源的请求都会看到错误页面，照成拒绝服务。

![image-20220506164945523](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220506164945523.png)

![image-20220506164950332](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220506164950332.png)

遗憾的是撞洞了，白搞，记录一下

![image-20220506164955419](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220506164955419.png)

------

参考：

美国国防部cp-dos：https://hackerone.com/reports/1183263
嘶吼Cache投毒DoS:https://www.4hou.com/posts/R8WL
