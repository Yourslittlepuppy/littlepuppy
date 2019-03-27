# seed用法：
sed [option]... 'script' inputfile…  
 
> **1、常用选项：**   

-n：不输出模式空间内容到屏幕，即不自动打印  
-e: 多点编辑  
-f：/PATH/SCRIPT_FILE: 从指定文件中读取编辑脚本  
-r: 支持使用扩展正则表达式  
-i.bak: 备份文件并原处编辑  
-p 打印  
+ script:    

'地址命令'   ----缺省则对整个问价进行修改  
不输入命令则默认打印 

> 2、sed工具  

+ 地址定界：  

(1) 不给地址：对全文进行处理  

(2) 单地址：  

sed -n "2p" passwd   
打印2行  

"\#数字": 指定的行，$：最后一行  

/pattern/：被此处模式所能够匹配到的每一行  

(3) 地址范围：

\,#  
 \#,+#  
/pat1/,/pat2/  
\#,/pat1/  
(4) ~：步进  
1~2 奇数行  
2~2 偶数行  
sed -n "1~2p"  
+ 编辑命令：  

sed -n "1-2d"

d: 删除模式空间匹配的行，并立即启用下一轮循环  

p：打印当前模式空间内容，追加到默认输出之后  

a [\]text：在指定行后面追加文本    

支持使用\n实现多行追加

i [\]text：在行前面插入文本

c [\]text：替换行为单行或多行文本

w /path/somefile: 保存模式匹配的行至指定文件

r /path/somefile：读取指定文件的文本至模式空间中匹配到的行后 

=: 为模式空间中的行打印行号

!:模式空间中匹配行取反处理


+ 搜索替代

s///：查找替换,支持使用其它分隔符，s@@@，s###

s#需要替代内容#替代后内容#

+ 替换标记：

g: 行内全局替换

p: 显示替换成功的行

w /PATH/TO/SOMEFILE：将替换成功的行保存至文件中

_sed示例_

◆ sed ‘2p’  /etc/passwd

◆ sed –n ‘2p’ /etc/passwd

◆ sed –n ‘1,4p’ /etc/passwd

◆ sed –n ‘/root/p’  /etc/passwd

◆ sed –n ‘2,/root/p’  /etc/passwd 从2行开始

◆ sed -n ‘/^$/=’  file 显示空行行号

◆ sed –n –e ‘/^$/p’ –e ‘/^$/=’  file 
多点操作

◆ sed  ‘/root/a\superman’ /etc/passwd行后

◆ sed ‘/root/i\superman’ /etc/passwd 行前

◆ sed ‘/root/c\superman’ /etc/passwd 代替行




_sed示例_

◆sed ‘/^$/d’ file
◆sed ‘1,10d’ file
◆nl /etc/passwd | sed ‘2,5d’

◆nl /etc/passwd | sed ‘2a tea’

◆sed 's/test/mytest/g' example

◆sed –n ‘s/root/&superman/p’ /etc/passwd 单词后

◆sed –n ‘s/root/superman&/p’ /etc/passwd 单词前

◆sed -e ‘s/dog/cat/’ -e ‘s/hi/lo/’ pets

◆sed –i.bak ‘s/dog/cat/g’ pets

  
  修改文件，先备份，备份文件名  .bak  

