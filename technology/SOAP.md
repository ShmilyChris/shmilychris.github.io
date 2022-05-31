## 记SOAP

#### 0x00 Java中的三种Webservice

- ###### JAX-WS规范

  - 早期的基于`SOAP`的`Java`的Web服务规范`JAX-RPC`目前已经被`JAX-WS`规范取代
  - 采用标准`SOAP`协议传输，`SOAP`属于`w3c`标准`Soap`协议是基于`http`的应用层协议，`soap`协议传输的是`xml`数据
  - 采用`wsdl`作为描述语言即`WebService`使用说明书，`wsdl`属`w3c`标准
  - `xml`是`WebService`的跨平台的基础
  - `W3C`为`WebService`制定了一套传输数据类型，即**XSD**,使用xml进行描述，任何编程语言写的`Webservice`接口发送数据时都要转换成`webservice`标准的**XSD**发送

- ###### JAX-RS规范

- ###### JAXM&SAAJ(废弃)

------

#### 0x01 WebService(jax-ws)三要素

- **SOAP：**基于HTTP协议，采用XML格式，用于传递信息
- **WSDL：**用来描述如何访问具体的服务
- **UDDI：**用户自己可以按UDDI标准搭建UDDI服务器，用来管理、分发、查询WebService，其它用户可以自己注册发布WebService调用

------

#### 0x02 SOAP(通讯协议)(包括四部分)

- SOAP封装：封装定义一个描述消息，**内容是什么**、**是谁发送**、**谁应当接受处理**、**处理框架**
- SOAP编码规则：用于表示应用程序需要使用的数据类型实例
- SOAP RPC：表示远程过程调用和应答的协定
- SOAP绑定：使用底层协议交换信息

**SOAP请求(SOAP HTTP Binding)：**

**HTTP+XML = SOAP**

```xml-dtd
Content-Type: MIMEType; charset=character-encoding
```

**完整请求：**

```http
POST /InStock HTTP/1.1
Host: www.example.org
Content-Type: application/soap+xml; charset=utf-8
Content-Length: nnn

<?xml version="1.0"?>
<soap:Envelope
xmlns:soap="http://www.w3.org/2001/12/soap-envelope"
soap:encodingStyle="http://www.w3.org/2001/12/soap-encoding">

<soap:Body xmlns:m="http://www.example.org/stock">
  <m:GetStockPrice>
    <m:StockName>IBM</m:StockName>
  </m:GetStockPrice>
</soap:Body>

</soap:Envelope>
```

解读SOAP基本结构：

```xml-dtd
<?xml version="1.0"?>
<soap:Envelope
xmlns:soap"http://www.w3.org/2001/12/soap-envelope"
soap:encodingStyle="http://www.w3.org/2001/12/soap-encoding">

<soap:Header>
...
</soap:Header>

<soap:Body>
...
  <soap:Fault>
  ...
  </soap:Fault>
</soap:Body>

</soap:Envelope>
```

- `<soap:Envelope`：**SOAP Envelope元素**，必要的SOAP消息根元素，用于把XML文档定义为SOAP消息
- `xmlns:soap`：SOAP命名空间，SOAP消息必须拥有与命令空间`http://www.w3.org/2001/12/soap-envelope`相关联的Envelope元素
- `soap:encodingStyle`：SOAP消息没有默认的编码方式，**SOAP encodingStyle属性**用于定义在文档中使用的数据类型
- `<soap:Header>`：可选的 **SOAP Header** 元素包含头部信息，可包含有关 SOAP 消息的应用程序专用信息（比如认证、支付等）
- `<soap:Body>`：强制使用，**SOAP Body** 元素包含实际的 SOAP 消息，包含打算传送到消息最终端点的实际 SOAP 消息
- `<soap:Fault>`：SOAP Fault 元素用于存留 SOAP 消息的错误和状态信息

------

#### 0x03 WSDL

WSDL文档实例：

```xml
<definitions>
 
<types>
  data type definitions........
</types>
 
<message>
  definition of the data being communicated....
</message>
 
<portType>
  set of operations......
</portType>
 
<binding>
  protocol and data format specification....
</binding>
 
</definitions>
```

- **WSDL types**：`<types>`元素，定义Web Service使用的数据类型

- **WSDL 消息**：`<message>`元素，定义一个操作的数据元素

- **WSDL 端口**：`<portType>`，最重要是WSDL元素，描述一个Web Service、可执行的操作与相关的消息，可以理解为编程里的函数库,**WSD定义了四种操作类型**：

  - **One-way**：此操作可接受消息，但不会返回响应

    - ```xml
      <message name="newTermValues">
        <part name="term" type="xs:string"/>
        <part name="value" type="xs:string"/>
      </message>
       
      <portType name="glossaryTerms">
        <operation name="setTerm">
          <input name="newTerm" message="newTermValues"/>
        </operation>
      </portType >
      ```

      - 端口`glossaryTerms`定义了一个名为`setTerm`的**One-way操作类型**操作
      - `setTerm`操作接受项目消息的输入，此消息带有输入参数 `term` 和 `value`，由`<message>`标签所定义
      - 由于操作没有定义任何输出，所以属于**One-way操作类型**

  - **Request-response**：此操作可接受一个请求并会返回一个响应

    - ```xml
      <message name="getTermRequest">
        <part name="term" type="xs:string"/>
      </message>
       
      <message name="getTermResponse">
        <part name="value" type="xs:string"/>
      </message>
       
      <portType name="glossaryTerms">
        <operation name="getTerm">
          <input message="getTermRequest"/>
          <output message="getTermResponse"/>
        </operation>
      </portType>
      ```

      - 与One-way操作类型不同，因为有`<output message="getTermResponse"/>`输出操作，所以属于**Request-response操作类型**

  - **Solicit-response**：此操作可发送一个请求，并会等待一个响应

  - **Notification**：此操作可发送一条消息，但不会等待响应

- **WSDL Bindings**：`<binding>`元素为每个端口定义消息格式和协议细节

  - WSDL绑定实例：

    - ```xml
      <binding type="glossaryTerms" name="b1">
         <soap:binding style="document"
         transport="http://schemas.xmlsoap.org/soap/http" />
         <operation>
           <soap:operation soapAction="http://example.com/getTerm"/>
           <input><soap:body use="literal"/></input>
           <output><soap:body use="literal"/></output>
        </operation>
      </binding>
      ```

      - `soap:binding`元素有两个属性：style 属性和 transport 属性
        - style 属性可取值 `rpc`或 `document`
        - `transport` 属性定义了要使用的 SOAP 协议
      - `operation`元素定义了每个端口提供的操作符

- **WSDL UDDI**：

  - UDDI 是一种目录服务，企业可以使用它对 Web services 进行注册和搜索
  - UDDI 是一种用于存储有关 web services 的信息的目录
  - UDDI 是一种由 WSDL 描述的 web services 界面的目录
  - UDDI 经由 SOAP 进行通信

------

#### 0x04 SOAP service漏洞挖掘

- BurpSuite插件：wsdler

  - 作用：解析WSDL文档构造请求

- SOAP 注入Payload：

  ```
  ==
  =
  ' --
  ' #
  ' –
  '--
  '/*
  " --
  " #
  " –
  "--
  "/*
  '
  "
  )
  %
  '-- -
  "-- -
  )-- -
  ```

------

#### 0x05 SOAP漏洞案例

- **Websphere 反序列化为 RCE**：
  - https://wya.pl/tag/websphere-application-server/
- **SOAP文件信息泄露**：
  - https://elmahdi.tistory.com/3
- **OOB-XXE(基于SOAP未经身份验证)**：
  - https://infosecwriteups.com/soap-based-unauthenticated-out-of-band-xml-external-entity-oob-xxe-in-a-help-desk-software-c27a6abf182a
