# VIM编辑器常用命令

# VIM简介  
-----

vim是一个类似于Vi的著名的功能强大、高度可定制的文本编辑器，  
在Vi的基础上改进和增加了很多特性。  
# VIM 配置    
-----
配置路径       
全局：/etc/vimrc
个人：~/.vimrc
普通用户没有权限可以增加修改权限：  

`[zhangsan@zhangsan ~]$chmod +x /etc/vimrc`


配置相关设置：  
如  set number,这个每次打开vim在首行出现编号。    
    set cul(curtorline) 设置vim编辑的下划线；  
    启用：set hlsearch  
    禁用：set nohlsearch    
    设置搜索结果高亮显示
    设置行号结果如下所示：  
    
    1 root:x:0:0:root:/root:/bin/bash
    2 bin:x:1:1:bin:/bin:/sbin/nologin
    3 daemon:x:2:2:daemon:/sbin:/sbin/nologin
    4 adm:x:3:4:adm:/var/adm:/sbin/nologin
    5 lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
    6 sync:x:5:0:sync:/sbin:/bin/sync
    7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown                                                      
      

     
## 常见用法  
>  **1、模式转换**

   命令模式 --> 插入模式  

       i: insert, 在光标所在处输入   
       I：在当前光标所在行的行首输入  
       a: append, 在光标所在处后面输入  
       A：在当前光标所在行的行尾输入  
       o: 在当前光标所在行的下方打开一个新行  
       O：在当前光标所在行的上方打开一个新行  

插入模式 --------> 命令模式   
 ESC  
命令模式 --------> 扩展命令模式  
                  :  
扩展命令模式 --------> 命令模式  
            ESC,enter

> **2、关闭文件**

扩展模式下：  
:q 退出  
:q! 强制退出，丢弃做出的修改  
:wq 保存退出 
:x 保存退出  
命令模式下：  
ZZ：保存退出  
ZQ：不保存退出   

> **3、扩展模式下的操作**  

r +文件名 可以将文件内容读取到当前文件中  
w +文件名 可以将文件内容读取到当前文件中  
！+需要执行的命令如hostname  !hostname  
r!hosstname  将命令执行结果输入到当前文件中  
> **4、 命令模式下的光标跳转**    

字符字节间跳转  
h: 左 l: 右 j: 下 k: 上 3l 表示向右移动3个字符  
w：下一个单词的词首  
e：当前或下一单词的词尾   
b：当前或前一个单词的词首  
当前页跳转  
  H：页首 M：页中间行 L:页底  
行首行尾跳转：
^: 跳转至行首的第一个非空白字符  
0: 跳转至行首  
$: 跳转至行尾  
行间移动：  
G：最后一行  
number +G 跳转指定行, gg: 第一行  
句间移动：  
)：下一句 (：上一句  
段落间移动：  
}:下一段 {：上一段    
> **5、命令模式操作**  

x: 删除光标处的字符  
number +x: 删除光标处起始的#个字符  
xp: 交换光标所在处的字符及其后面字符的位置  
~:转换大小写  
J:删除当前行后的换行符    
替换命令：  
r: 替换光标所在处的字符  
R:切换成REPLACE模式  一直替换  
复制命令  
y ---复制  
yy --复制多行  
Y -- 复制整行    
> **6、多窗口 **   

单文件多窗口    
Ctrl +w +s   水平分割  
Ctrl +w +v   垂直分割  
Ctrl +w +q   取消相邻窗口  
Ctrl +w +o   取消全部窗口
移动光标  Ctrl +w  加方向键  -->  <--  
多文件多窗口
vim -o|-O FILE1 FILE2 ...  
-o: 水平分割  
-O: 垂直分割  
在窗口间切换：Ctrl+w, 方向键  



  


  



