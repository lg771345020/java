## host文件的用途和用法  

### 一． Hosts文件的位置   

    C:\Windows\System32\drivers\etc

### 二． Hosts文件的基本内容和语法  

用记事本打开hosts文件，就可以看见了微软对这个文件的说明。Hosts文件文一般有如下面的基本内容

```
# Copyright (c) 1993-2009 Microsoft Corp.
#
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
#
# This file contains the mappings of IP addresses to host names. Each
# entry should be kept on an individual line. The IP address should
# be placed in the first column followed by the corresponding host name.
# The IP address and the host name should be separated by at least one
# space.
#
# Additionally, comments (such as these) may be inserted on individual
# lines or following the machine name denoted by a '#' symbol.
#
# For example:
#
#      102.54.94.97     rhino.acme.com          # source server
#       38.25.63.10     x.acme.com              # x client host

# localhost name resolution is handled within DNS itself.
#	127.0.0.1       localhost
#	::1             localhost

127.0.0.1       localhost
```

这个文件是根据 TCP/IP for Windows 的标准来工作的。它的作用是定义 IP 地址和 Host name (主机名)的映射关系，是一个映射 IP 地址和 Host name (主机名) 的规定。这个规定中，要求每段只能包括一个映射关系，也就是一个 IP 地址和一个与之有映射关系的主机名。 IP 地址要放在每段的最前面，映射的 Host name (主机名)在 IP 后面，中间用空格分隔。对于这段的映射说明，用“#”分割后用文字说明。

### 三． Hosts文件的工作方式   

现在让我们来看看Hosts在Windows中是怎么工作的。   我们知道在网络上访问网站，要首先通过DNS服务器把要访问的网络域名（XXXX.com）解析成XXX.XXX.XXX.XXX的IP地址后，计算机才能对这个网络域名作访问。   要是对于每个域名请求我们都要等待域名服务器解析后返回IP信息，这样访问网络的效率就会降低，因为DNS做域名解析和返回IP都需要时间。   为了提高对经常访问的网络域名的解析效率，可以通过利用Hosts文件中建立域名和IP的映射关系来达到目的。根据Windows系统规定，在进行DNS请求以前，Windows系统会先检查自己的Hosts文件中是否有这个网络域名映射关系。如果有则，调用这个IP地址映射，如果没有，再向已知的DNS服务器提出域名解析。也就是说Hosts的请求级别比DNS高。    

### 四． Hosts文件的工作方式和具体作用   

现在来看一下Hosts文件的工作方式以及它在具体使用中起哪些作用。    

1、加快域名解析    

对于要经常访问的网站，我们可以通过在Hosts中配置域名和IP的映射关系，提高域名解析速度。由于有了映射关系，当我们输入域名计算机就能很快解析出IP，而不用请求网络上的DNS服务器。    

2、方便局域网用户    

在很多单位的局域网中，会有服务器提供给用户使用。但由于局域网中一般很少架设DNS服务器，访问这些服务器时，要输入难记的IP地址。这对不少人来说相当麻烦。现在可以分别给这些服务器取个容易记住的名字，然后在Hosts中建立IP映射，这样以后访问的时候，只要输入这个服务器的名字就行了。    

3、屏蔽网站    

现在有很多网站不经过用户同意就将各种各样的插件安装到你的计算机中，其中有些说不定就是木马或病毒。对于这些网站我们可以利用Hosts把该网站的域名映射到错误的IP或本地计算机的IP，这样就不用访问了。在WINDOWSX系统中，约定127.0.0.1为本地计算机的IP地址, 0.0.0.0是错误的IP地址。  

如果，我们在Hosts中，写入以下内容：    

```
127.0.0.1 # 要屏蔽的网站 A    
0.0.0.0   # 要屏蔽的网站 B    
```

这样，计算机解析域名 A和 B时，就解析到本机IP或错误的IP，达到了屏蔽网站A 和B的目的。    

4、顺利连接系统 

对于Lotus的服务器和一些数据库服务器，在访问时如果直接输入IP地址那是不能访问的，只能输入服务器名才能访问。那么我们配置好Hosts文件，这样输入服务器名就能顺利连接了。    

### 五． 屏蔽不想访问的网站的例子 
  
这里给出一些收集到的利用Hosts文件对一些网址屏蔽的例子，共大家学习使用Hosts文件时参考。    

例1. 在 hosts文件中加入如下内容就可以屏蔽文件中定义的对应的网址  

```
127.0.0.1 localhost   
127.0.0.1 bar.baidu.com #百度IE搜索伴侣  
127.0.0.1 www.baidu.com #百度IE搜索伴侣  
127.0.0.1 baidu.com     #百度IE搜索伴侣    
```

例2. IDEA 免注册

```
0.0.0.0 account.jetbrains.com  
```

例3. qq去广告，在ect加入 

```
# 屏蔽 QQ 游戏
0.0.0.0 adsfile.qq.com 
0.0.0.0 c.l.qq.com 
 ```
 
 例4. 视频网页去广告 
 
 ```
#优酷  
0.0.0.0 stat.youku.com 
  ```
