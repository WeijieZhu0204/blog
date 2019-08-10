# shadowsocks+proxifier实现真全局代理

问题：

​	我们平常使用shadowsocks的时候，只能解决浏览器代理的问题，而无法解决其他应用场景的代理问题。即使shadowsocks有"全局模式"，也并非是我们想要的真正的全局网络代理。

​	那么在我们日常开发的时候，肯定会遇到以下类似的问题：

- 通过npm安装sass模块的网络问题。
- 通过docker下载docker hub镜像的网络问题。
- 各种软件自动更新下载安装包的网络问题。
- 等等....



下面就教大家一个通过shadowsocks + proxifier实现真全局代理的方法。

1. 先去proxifier官网下载安装。[https://www.proxifier.com/](https://www.proxifier.com/)

   由于软件收费，附带一个mac注册码：P427L-9Y552-5433E-8DSR3-58Z68

2. 获取shadowsocks代理端口

   shadowsocks就跟平时科学上网的时候一样正常打开即可。打开shadowsocks高级设置，查询”本地Socks5监听端口“，一般默认端口是*1086*。

3. 添加Proxy Servers

   点击Proxies -> Add；

   Address: 127.0.0.1；

   Port: 步骤二查询的端口；

   Protocol：SOCKS Version 5；

   OK

4. 添加Proxy Rules

   点击Rules，按照如下配置，一般会有默认配置；

   | Name                    | Applications | Target Hosts                              | Target Ports | Action                      |
   | ----------------------- | ------------ | ----------------------------------------- | ------------ | --------------------------- |
   | ss-local [auto-created] | ss-local     | Any                                       | Any          | Direct                      |
   | Localhost               | Any          | localhost; 127.0.0.1; ::1; %ComputerName% | Any          | Direct                      |
   | Default                 | Any          | Any                                       |              | Proxy SOCKS5 127.0.0.1:1086 |

   ss-local [auto-created]是指ss本地程序走直接连接（软件会弹窗提示自动创建）。

   Default是指所有的程序都会走1086端口代理。

5. 测试

   到此为止，已经完成了所有配置。那我们来测试一下是否代理成功。打开一个本地终端，输入`curl www.google.com`，尝试获取google首页的html内容。

   如果一切正常的话，你会马上收到如下返回：

   ```powershell
   ➜  ~ curl www.google.com
   <!doctype html><html itemscope="" itemtype="http://schema.org/WebPage" lang="en"><head><meta content="Search the world's information, including webpages, images, videos and more. Google has many special features to help you find exactly what you're looking......
   ```

   同时在proxifier你会发现Connections里面有日志打出：

   ```powershell
   [08.10 16:20:59] curl - www.google.com:80 open through proxy 127.0.0.1:1086 SOCKS5
   
   [08.10 16:20:59] curl - www.google.com:80 close, 78 bytes sent, 12210 bytes (11.9 KB) received, lifetime <1 sec
   ```

   如果失败的话则收不到返回的html内容，请检查自己的配置是否有问题。

   >Tips:
   >
   >这里不能用 ping www.google.com 来测试，因为ping命令走的并非TCP或UDP协议。

6. 配置某些程序走代理

   如果我们只希望某些程序走代理，则在Rules里面添加一条新规则，Applications里面选择自己期望的程序即可，其他配置与Default规则一致。

   同时Default规则的Action要改为Direct：直接连接。