Linux程序包安装方法
# 常用安装方式：
1）rpm安装  
2）yum 安装  
3）定制yum仓库  
4）编译安装 

# **一、 rpm安装软件**
> ## 1、rpm 软件安装 ---资源准备 
（1）挂载光盘 
**方法一**：
 `[root@eric ~]#df` 查看光盘路径
参考链接: https://blog.csdn.net/qq_40498551/article/details/88698603 [https://blog.csdn.net/qq_40498551/article/details/88698603]  

**方法二**  
收动挂载  

    [root@eric data]# mount /dev/sr0 /mnt
mount: /dev/sr0 is write-protected, mounting read-only  

缺点：每次使用都要手动挂载，相对繁琐  
**方法三**
使用cd /misc/cd 进入光盘文件，正常机器不能使用，需要安装额外的程序：autofs,可以先按照其他方法安装好这个程序后，直接调用方法三。
这样挂载的光盘一直存在，需要时直接调用即可

>  ## **2、rpm软件  安装**

进入光盘文件下，进入Package 文件后就可以安装想装的安装包了；
下面以安装方法三的autofs为例，进行安装：
   
    [root@eric Packages]# rpm -i autofs-5.0.7-99.el7.x86_64.rpm  

rpm -q autofs   ----查看目前是否已安装，进行验证  
rpm -qa autofs |grep auto  ----模糊搜索目前已安装软件  
rpm -qa auto*    ----通配符模糊搜索目前已安装软件  
rpm -ql +软件名  ----查看软件包内包含的详细包信息
启动服务：  
systemctl start autofs    
查看服务是否开机启动，避免下次开机重新配置

    [root@eric Packages]# systemctl status autofs 
    autofs.service - Automounts filesystems on demandLoaded: loaded (/usr/lib/systemd/system/autofs.service; disabled; vendor preset: disabled)  
开机启动为disable ,所以不是开机启动，需重新配置  

    [root@eric Packages]# systemctl enable autofs  

然后在次打开`ls /misc ` 就能成功打开了 

    [root@eric Packages]# rpm -ivh /misc/cd +安装软件名

实测可以安装  -vh 显示详细信息和进度


若不知道某个程序来自哪个包，可以反向查看：  
rpm -qf +软件   
rpm -i +软件名 --replacepkgs  覆盖安装或者  --force   
rpm -e  +软件名  卸载软件

+ 依赖安装处理  

rpm安装过程中可能会出现包的相互依赖关系，不能依次安装。  
例如安装: ` httpd  ` 时会导致安装失败



# 二、YUM安装

（yum 安装在使用时需要搭建好yum仓库，搭建yum服务器文件使用cls格式。）

> ## **1、yum客户端配置**  
+ yum源 配置光盘源  

yum 的**配置文件**在以下路径：   


    [root@eric yum.repos.d]# cd /etc/yum.repos.d/   
    CentOS-Base.repo  CentOS-Debuginfo.repo         CentOS-Media.repo    CentOS-Vault.repo
    CentOS-CR.repo    CentOS-fasttrack.repo     CentOS-Sources.repo  



目前路径下的配置连接的是Cenos 原始加载路径  
编写配置文件前应先备份原始文件。  

    [root@eric yum.repos.d]# mkdir backup  
    [root@eric yum.repos.d]# mv -r *.repo backup/


接下来开始编写yum配置文件：  
[root@eric yum.repos.d]#vim base.repos  
 [任意开头，但是结尾必须是repo]

    [base]
    name=centos base
    baseurl= https://mirrors.aliyun.com/centos/$releasever/os/$basearch/
    gpgcheck = 1
    gpgkey=https://mirrors.aliyun.com/centos/$releasever/RPM-GPG-KEY-CentOS-$rele    asever 
   
\ # $releasever  $basearch   centos  系统版本号标量  
查看仓库列表和包信息；
 
    [root@eric yum.repos.d]# yum repolist
    Loaded plugins: fastestmirror, langpacks
    Loading mirror speeds from cached hostfile
    repo id                                                                                 repo name                                                                             status
    !base/7/x86_64                                                                          centos base                                                                           10,019
    repolist: 10,019

查看yum源文件下载位置   
    
    [root@eric ~]# cd /var/cache/yum 

测试：  安装httpd  

yum  install httpd    --首次安装验证key  输入"y"同意
+ yum 安装配置epl源
操作与配置光盘源一样，其地址为：
[https://mirrors.aliyun.com/epel/7/x86_64/](https://mirrors.aliyun.com/epel/7/x86_64/)


卸载 软件
 yum remove httpd  就可以卸载
## 特殊情况 ##
 
安装时会有依赖包的安装，卸载时不卸载与之依赖的包 。

 解决这个问题可以使用  

 yum history 

    [root@eric yum.repos.d]# yum history
    Loaded plugins: fastestmirror, langpacks
    ID     | Login user               | Date and time    | Action(s)      | Altered
    -------------------------------------------------------------------------------
     3 | eric <eric>              | 2019-03-24 18:24 | Install        |    5   
     2 | eric <eric>              | 2019-03-23 11:41 | Install        |    1  <
     1 | System <unset>           | 2019-03-19 16:47 | Install        | 1382 > 
    history list
查看刚做的操作
yum history info2  

yum history undo 2   撤销第2步做是操作。 或者redo  重新操作

> ## **3.yum 安装包组** 
yum grouplist  

yum groupinstall   
包租安装需要一个整体"  包组名   "  ------包组存在空格

yum groupinfo  +  包组名，查看包组信息

yum  install 还可以直接复制网址进行安装 

yum install  [https://mirrors.aliyun.com/epel/7/x86_64/]
(https://mirrors.aliyun.com/epel/7/x86_64/)


> ##  **4、 yum本地光盘服务器仓库**

`vim /etc/yum.repos.d/base.repo  `  

修改其中的baseurl为如下  :

`baseurl =fiel:///misc/cd`

yum clean all  清除缓存

> ## **5、 yum  用mirrorlist 文件**

在根目录下新建 `vim yumlist`  

        1 file:///misc/cseurl =fiel:///misc/cd  

        2 https://mirrors.aliyun.com/centos/$releasever/os/$basearch/  

        3 https://mirrors.aliyun.com/epel/$releasever/os/$basearch/`
再次打开`vim /etc/yum.repos.d/base.repo `

使用mirrorlist  形式编辑

    mirrorlist =file:/root/yumlist








    
  




