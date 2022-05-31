## 记DOM XSS

#### 0x00 DOM剖析

文档对象模型 = DOM，Web浏览器对页面上元素的分层表示形式，网站可以使用JavaScript来操控DOM，当网站包含JavaScript时，就会出现基于DOM的漏洞。

------

#### 0x01 DOM基础

- 一些常用的HTML DOM方法：

  - getElementByld(id)：返回带有指定ID的节点

  - appendChild(node)：插入新的子节点

  - removeChild(node)：删除子节点
  - getElementsByTagName：返回带指定标签名的所有元素

- 一些常用的HTML DOM属性：

  - innerHTML：节点的文本值
  - parentNode：节点的父节点
  - childNodes：节点的子节点
  - attributes：节点的属性节点

- 一些常见的HTML DOM事件：

  - onload与onunload：进入或离开页面时会触发，onload 事件可用于检查访客的浏览器类型和版本
  - onchange：输入字段验证
  - onmouseover与onmouseout：在鼠标指针移动到或离开元素时触发函数。
  - onmousedown与onmouseup与onclick 事件：onmousedown、onmouseup 以及 onclick 事件是鼠标点击的全部过程。首先当某个鼠标按钮被点击时，触发 onmousedown 事件，然后，当鼠标按钮被松开时，会触发 onmouseup 事件，最后，当鼠标点击完成时，触发 onclick 事件。

------

#### 0x02 Browser 对象

- **Windows对象**

  | 属性           | 描述                                                         |
  | -------------- | ------------------------------------------------------------ |
  | closed         | 返回窗口是否已被关闭                                         |
  | defaultStatus  | 设置或返回窗口状态栏中的默认文本                             |
  | document       | 对 Document 对象的只读引用                                   |
  | frames         | 返回窗口中所有命名的框架。该集合是 Window 对象的数组，每个 Window 对象在窗口中含有一个框架 |
  | history        | 对 History 对象的只读引用                                    |
  | innerHeight    | 返回窗口的文档显示区的高度                                   |
  | innerWidth     | 返回窗口的文档显示区的宽度                                   |
  | localStorage   | 在浏览器中存储 key/value 对。没有过期时间                    |
  | length         | 设置或返回窗口中的框架数量                                   |
  | location       | 用于窗口或框架的 Location 对象                               |
  | name           | 设置或返回窗口的名称                                         |
  | navigator      | 对 Navigator 对象的只读引用                                  |
  | opener         | 返回对创建此窗口的窗口的引用                                 |
  | outerHeight    | 返回窗口的外部高度，包含工具条与滚动条                       |
  | outerWidth     | 返回窗口的外部宽度，包含工具条与滚动条                       |
  | pageXOffset    | 设置或返回当前页面相对于窗口显示区左上角的 X 位置            |
  | pageYOffset    | 设置或返回当前页面相对于窗口显示区左上角的 Y 位置            |
  | parent         | 返回父窗口                                                   |
  | screen         | 对 Screen 对象的只读引用                                     |
  | screenLeft     | 返回相对于屏幕窗口的x坐标                                    |
  | screenTop      | 返回相对于屏幕窗口的y坐标                                    |
  | sessionStorage | 在浏览器中存储 key/value 对，在关闭窗口或标签页之后将会删除这些数据 |
  | screenY        | 返回相对于屏幕窗口的y坐标                                    |
  | self           | 返回对当前窗口的引用，等价于 Window 属性                     |
  | status         | 设置窗口状态栏的文本                                         |
  | top            | 返回最顶层的父窗口                                           |
  
- **Navigator 对象属性**

  | 属性          | 说明                                        |
  | ------------- | ------------------------------------------- |
  | appCodeName   | 返回浏览器的代码名                          |
  | appName       | 返回浏览器的名称                            |
  | appVersion    | 返回浏览器的平台和版本信息                  |
  | cookieEnabled | 返回指明浏览器中是否启用 cookie 的布尔值    |
  | platform      | 返回运行浏览器的操作系统平台                |
  | userAgent     | 返回由客户机发送服务器的user-agent 头部的值 |

- History 对象

  | 方法      | 说明                              |
  | --------- | --------------------------------- |
  | back()    | 加载 history 列表中的前一个 URL   |
  | forward() | 加载 history 列表中的下一个 URL   |
  | go()      | 加载 history 列表中的某个具体页面 |

- Location 对象

  | 属性      | 描述                          |
  | --------- | ----------------------------- |
  | hash      | 返回一个URL的锚部分           |
  | host      | 返回一个URL的主机名和端口     |
  | hostname  | 返回URL的主机名               |
  | href      | 返回完整的URL                 |
  | pathname  | 返回的URL路径名               |
  | port      | 返回一个URL服务器使用的端口号 |
  | protocol  | 返回一个URL协议               |
  | search    | 返回一个URL的查询部分         |
  | Location  | 对象方法                      |
  | assign()  | 载入一个新的文档              |
  | reload()  | 重新载入当前文档              |
  | replace() | 用新的文档替换当前文档        |

  