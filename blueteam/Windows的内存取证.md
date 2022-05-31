## Dumpit+Volatility的组合

###### Dumpit：https://down10.software/download-dumpit/download/

###### Volatility：https://www.volatilityfoundation.org/releases

#### 0x00 浅谈一下内存取证以及具体思路

这里只记录一下简单的内存取证，很基础，用来应付一些hvv跟ctf题目，具体流程为使用Dumpit保持系统的内存镜像，然后通过Volatility去分析。

想深入学可以看看京东高级开发JinZhenguo师傅的博客：https://94275.cn/about/，这里直接引用师傅的思维导图，提供一个学习方向

![](https://raw.staticdn.net/ShmilyChris/wiki/main/5e5b3737e4b0d4dc8776d2c4.png)

#### 0x01 Dumpit保存当前的内存数据

1. 直接放cmd里面运行

   ![image-20220506183617552](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220506183617552.png)

2. 运行成功后会在目录下生成一个内存镜像![image-20220506185401014](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220506185401014.png)

#### 0x02 Volatility基础分析取证

###### 环境是找师傅要的2018年强网杯的镜像：easy_dump.img

- **查看镜像系统信息(我这里使用的版本是2.6，不支持win11镜像)：**

  `.\volatility.exe -f .\easy_dump.img imageinfo`

  ![image-20220507131155143](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220507131155143.png)

- **通过Suggested Profile(s) 猜系统获取volshell(系统正确才能进入volshell模式)：**

  `.\volatility.exe -f .\easy_dump.img --profile=Win7SP1x64 volshell`

  ![image-20220507131430858](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220507131430858.png)

- **枚举内存镜像当时运行的进程:**

  `.\volatility.exe -f .\easy_dump.img --profile=Win7SP1x64 pslist`

  ![image-20220507131705271](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220507131705271.png)

- **枚举内存镜像当时运行的进程加强版(可以列出子父进程,方便找隐藏病毒)：**

  `.\volatility.exe -f .\easy_dump.img --profile=Win7SP1x64 pstree`

  ![image-20220507131921536](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220507131921536.png)

- **提取进程(`-p`为进程的pid，`-D`为保存dump出来的文件的路径，后面会专门写篇文章用procdump64分析dmp)**

  `.\volatility.exe -f .\easy_dump.img --profile=Win7SP1x64 memdump -p 248 -D ./`
  
  ![image-20220507132323433](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220507132323433.png)
  
- **枚举注册表(可接`-o Virtual`导出注册表地址)**
  
  `.\volatility.exe -f .\easy_dump.img --profile=Win7SP1x64 hivelist`
  
  ![image-20220507133817257](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220507133817257.png)
  
- **获取浏览器浏览历史(感觉没啥用，2022了谁用ie)**
  
  `.\volatility.exe -f .\easy_dump.img --profile=Win7SP1x64 iehistory`
  
- **查找文件(Linux可以配合grep精准查找)**
  
  ` .\volatility.exe -f .\easy_dump.img --profile=Win7SP1x64 filescan`
  
  ![image-20220507134251148](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220507134251148.png)
  
- **提取文件(`-Q `文件的内存地址  `--dump-dir`导出文件路径):**
  
  `.\volatility.exe -f .\easy_dump.img --profile=Win7SP1x64 dumpfiles -Q 0x000000000e1683c0 --dump-dir=./`
  
  ![image-20220507134941125](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220507134941125.png)
  
- **获取最后登录系统用户：**
  
  ` .\volatility.exe -f .\easy_dump.img --profile=Win7SP1x64 printkey -K "SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"`
  

  ![image-20220507135253990](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220507135253990.png)

- **枚举用户和密码：**
  
  `.\volatility.exe -f .\easy_dump.img --profile=Win7SP1x64 printkey -K "SAM\Domains\Account\Users\Names"`
  
  ![image-20220507135548872](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220507135548872.png)
  
- **获取内存中的密码哈希：**
  
  1. 使用`.\volatility.exe -f .\easy_dump.img --profile=Win7SP1x64 hivelist`记录下`\SystemRoot\System32\Config\SAM`和`\REGISTRY\MACHINE\SYSTEM`的Virtual地址
  
     ![image-20220507135756076](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220507135756076.png)
  
  2. **获取hash**:`.\volatility.exe -f .\easy_dump.img --profile=Win7SP1x64 hashdump -y 0xfffff8a000024010 -s 0xfffff8a001363010`(`-y`接SYSTEM地址，-s接SAM表)
  
     ![image-20220507140015695](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220507140015695.png)
  
- **获取屏幕截图(需要python PIL库的支持)：**

  `python -m pip install Pillow`

  `.\volatility.exe -f .\easy_dump.img --profile=Win7SP1x64 screenshot --dump-dir=./`

- **获取CMD命令使用情况：**

  `.\volatility.exe -f .\easy_dump.img --profile=Win7SP1x64 cmdscan`

  ![image-20220507140558604](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220507140558604.png)

- **查看服务开启情况**

  `.\volatility.exe -f .\easy_dump.img --profile=Win7SP1x64 svcscan`

  ![image-20220507140712140](https://raw.staticdn.net/ShmilyChris/wiki/main/image-20220507140712140.png)

  

  

  