title: 在Nginx中部署基于IP的虚拟主机
date: 2015-12-08 11:42:25
tags: nginx vps
---

### 一、虚拟主机概念

      虚拟主机是在网络服务器上划分出一定的磁盘空间供用户放置站点、应用组件等，提供必要的站点功能、数据存放和传输功能。所谓虚拟主机，也叫“网站空间”， 就是把一台运行在互联网上的服务器划分成多个“虚拟”的服务器，每一个虚拟主机都具有独立的域名和完整的Internet服务器（支持WWW、FTP、 E-mail等）功能，从用户角度来看，每台虚拟主机和一台独立的服务器完全相同，在IP地址日益紧张的今天，基于域名的虚拟主机要比基于IP的虚拟主机 使用的更加广泛。

### 二、系统环境

     系统平台：RHEL 5.4

     Nginx版本：nginx-1.0.15

### 三、配置基于IP的虚拟主机

      Linux、FreeBSD操作系统都允许添加IP别名。IP别名即：可以在一块物理网卡上绑定多个IP地址。这样就能够在使用单一网卡的同一个服务器上运行多个基于IP的虚拟主机。设置IP别名也非常容易，只须配置系统上的网络接口，让它监听额外的lP地址。在Linux系统上，可以使用标准的网络配置工具（比如ifconfig和route命令）添加IP别名。

1）、先用ifconfig命令查看该服务器的lP地址。

[root@linux nginx]# ifconfig

eth0      Link encap:Ethernet  HWaddr 08:00:27:99:E4:21  
          inet addr:10.0.0.133  Bcast:10.255.255.255  Mask:255.0.0.0
          inet6 addr: fe80::a00:27ff:fe99:e421/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:7324 errors:0 dropped:0 overruns:0 frame:0
          TX packets:2051 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:719461 (702.5 KiB)  TX bytes:308638 (301.4 KiB)


lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:16436  Metric:1
          RX packets:765 errors:0 dropped:0 overruns:0 frame:0
          TX packets:765 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:48577 (47.4 KiB)  TX bytes:48577 (47.4 KiB)


2）、在eth0网卡设备上添加两个lP别名10.0.0.189和10.0.0.190，通过ifconfig和route命令来进行：

[root@linux nginx]# ifconfig eth0:1 10.0.0.189 broadcast 10.255.255.255 netmask 255.0.0.0 up

[root@linux nginx]# route add -host 10.0.0.189 dev eth0:1    

[root@linux nginx]# ifconfig eth0:2 10.0.0.190 broadcast 10.255.255.255 netmask 255.0.0.0 up

[root@linux nginx]# route add -host 10.0.0.190 dev eth0:2  

3）、再执行ifconfig命令，就可以看到eth0网卡设备上绑定了两个lP别名

[root@linux nginx]# ifconfig

eth0      Link encap:Ethernet  HWaddr 08:00:27:99:E4:21  
          inet addr:10.0.0.133  Bcast:10.255.255.255  Mask:255.0.0.0
          inet6 addr: fe80::a00:27ff:fe99:e421/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:7490 errors:0 dropped:0 overruns:0 frame:0
          TX packets:2151 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:735277 (718.0 KiB)  TX bytes:342506 (334.4 KiB)

eth0:1    Link encap:Ethernet  HWaddr 08:00:27:99:E4:21  
          inet addr:10.0.0.189  Bcast:10.255.255.255  Mask:255.0.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1

eth0:2    Link encap:Ethernet  HWaddr 08:00:27:99:E4:21  
          inet addr:10.0.0.190  Bcast:10.255.255.255  Mask:255.0.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:16436  Metric:1
          RX packets:771 errors:0 dropped:0 overruns:0 frame:0
          TX packets:771 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:48897 (47.7 KiB)  TX bytes:48897 (47.7 KiB)

[root@linux nginx]#

4）、 从另外一台服务器Ping 10.0.0.189和10.0.0.190两个IP,如果能够Ping通，则证明配置无误。但是，通过ifconfig和route配置的IP别名在服 务器重启后会消失，不过可以将这两条ifconng和route命令添加到/etc/rc.local文件中，让系统开机时自动运行，以下是相关命令：
vi /etc/rc.local
在文件末尾增加以下内容，然后保存即可

ifconfig eth0:1 10.0.0.189 broadcast 10.255.255.255 netmask 255.0.0.0 up

route add -host 10.0.0.189 dev eth0:1    

ifconfig eth0:2 10.0.0.190 broadcast 10.255.255.255 netmask 255.0.0.0 up

route add -host 10.0.0.190 dev eth0:2  

5）、下面开始配置基于IP的虚拟主机，在Nginx配置文件（nginx.conf）中，分别对10.0.0.133、10.0.0.189、10.0.0.190三个IP配置三个纯静态HTML支持的虚拟主机。

    http {
    
        include       mime.types;
        default_type  application/octet-stream;

        log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                          '$status $body_bytes_sent "$http_referer" '
                          '"$http_user_agent" "$http_x_forwarded_for"';

        #access_log  logs/access.log  main;

        sendfile        on;
        #tcp_nopush     on;

        keepalive_timeout  65;

        #gzip  on;
        #第一个虚拟主机
        server {
            listen       10.0.0.133:80;               #监听的IP和端口
            server_name  10.0.0.133;              #主机名称

            access_log  logs/host1.access.log  main;                #访问日志文件存放路径

            location /
            {
                root /usr/local/nginx/html/host1;              #HTML网页文件存放的目录
                index  index.html index.htm;                    #默认首页文件，顺序从左到右，如果找不到index.html文件，则查找index.htm文件作为首页文件
            }
    }
        #第二个虚拟主机
        server {
            listen       10.0.0.189:80;
            server_name  10.0.0.189;

            access_log  logs/host2.access.log  main;

            location /
            {
                root /usr/local/nginx/html/host2;
                index  index.html index.htm;
            }
    }
        #第三个虚拟主机
        server {
            listen       10.0.0.190:80;
            server_name  10.0.0.190;

            access_log  logs/host3.access.log  main;

            location /
            {
                root /usr/local/nginx/html/host3;
                index  index.html index.htm;
            }
    }

      从上面的配置文件中可以看出，一段server{……}就是一个虚拟主机，如果要配置多个虚拟主机，建立多段server{……}配置即可，非常方便。监听的IP和端口也可以不写IP地址，只写端口，把它配置成"listen 80"，则表示监听该服务器上所有IP的80端口，可通过server_name区分不同 的虚拟主机。

### 四、测试
1）、用脚本重启nginx。

[root@linux conf]# service nginx restart

2）、在/usr/local/nginx/html/下分别建立三个目录host1，host2，host3。分别在三个目录中放一个index.html 文件，分别写上自己的IP地址；

3）、用浏览器访问相应IP地址。
![](http://pic002.cnblogs.com/images/2012/370046/2012052116292693.jpg)
![](http://pic002.cnblogs.com/images/2012/370046/2012052116301068.jpg)
![](http://pic002.cnblogs.com/images/2012/370046/2012052116304137.jpg)


*参考文档*:

*[linux下nginx实现虚拟主机（3种方法：基于域名、基于端口、基于ip地址]( linux下nginx实现虚拟主机（3种方法：基于域名、基于端口、基于ip地址）)*