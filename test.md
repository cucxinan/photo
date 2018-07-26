<center>2018暑期小学期实验报告</center>
=
<center>——Electronic Code Book（电子密码本）</center>
-
**一、实验内容**

&emsp;&emsp;利用ECB（电子密码本）加密模式的漏洞，对用户注册与登录系统的验证过程进行研究，找出网站cookie的规律性，利用Ruby语言进行反向编解码，从而实现在位置用户名与账户前提下进行账户登录。

**二、实验目的**

&emsp;&emsp;进一步理解ECB加密模式的优势与缺陷，在简单加密模式下进行密码的破解，了解信息安全管理与信息存储的细节，加深对加密过程的理解。

**三、实验准备**

&emsp;&emsp;1.Debian GNU Linux虚拟机镜像：提供实验环境与网站注册登录界面。

&emsp;&emsp;2.基础Ruby语言代码知识：在终端中对cookie和base-64代码进行编解码（加密）

&emsp;&emsp;3.页面代理与管理者工具：保存，提供cookie信息，提供控制台输入端口
     
**四、实验步骤**   

&emsp;&emsp;1.进入Linux虚拟机，查询本机实验环境IP地址及相关信息，进入浏览器键入10.37.129.3进入环境       
![1](https://github.com/cucxinan/photo.git/1.png)    
&emsp;&emsp;2.确认登录界面，尝试登陆admin（失败），确认实验环境无误    
![2](https://github.com/cucxinan/photo.git/2.png)  
&emsp;&emsp;3.注册测试用用户名（此处截图只显示其中之一）adminadmin , adminadmin1 , adminadmin11 ,将密码全部设置为同一数值（本次测试中设置为123456）      
![3](https://github.com/cucxinan/photo.git/3.png)  
&emsp;&emsp;4.记录每一注册用户名的cookie值，利用Ruby语言将cookie转化为base-64d编码模式，并将结果记录下来，进行比对，由此可知用户名有较大重复的情况时，cookie值与base-64的值在前几位会有较大重复  
![4](https://github.com/cucxinan/photo.git/0.png)  
&emsp;&emsp;5.此时单独拿出重复cookie部分进行base-64解码，得到值“Tu\xEF\xE8\xDD\xBE ”，而测试用户中重复的值序列为“Tu\xEF\xE8\xDD\xBE\x98\xF5 ”由此可以猜测出该网站使用的是“|用户名|分隔符|密码|”的填充加密模式，且加密的分组长度为8bites，故而会在接下来的位中产生不同的部分  
![5](https://github.com/cucxinan/photo.git/4.png)   
&emsp;&emsp;6.根据步骤5，可以明确知道网站对用户信息的加密模式与加密分组方式，此时可以构造人为的具有重复字母的用户“aaaaaaaaaaaaaaaaaaaa”(20个a)，密码与用户名相同，注册完毕后得到新的cookie值“GkzSM2vKHdcaTNIza8od1wS28inRHiC2GkzSM2vKHdcaTNIza8od1ys96EXmirn5”，进行内容比对可以发现cookie值中有大量的重复单元  
![6](https://github.com/cucxinan/photo.git/5.png)     
&emsp;&emsp;7.将步骤6中的cookie值与重复单元分别进行base-64编解码，得到对应的数值，并进行重复性检验，由此可以确定步骤6中的所做的猜测正确，此时可知“\x1AL\xD23k\xCA\x1D\xD7“部分即为8bites分组下下全a数值的加密结果  
![7](https://github.com/cucxinan/photo.git/6.png)     
&emsp;&emsp;8.注册一个新用户“aaaaaaaaadmin”,得到其cookie值GkzSM2vKHdfgVmQuKXLregdPxmQZ4yvj，并对其进行base-64解码，找到重复部分\x1AL\xD23k\xCA\x1D\xD7\xE0Vd.)r\xEBz\aO\xC6d\x19\xE3+\xE3，故推测不重复部分\xE0Vd.)r\xEBz\aO\xC6d\x19\xE3+\xE3即为admin账户下的cookie编码值  
![8](https://github.com/cucxinan/photo.git/7.png)    
&emsp;&emsp;9. 利用Ruby语言对非重复部分进行base-64反向编码，得到预计的admin账户的cookie值"4FZkLily63oHT8ZkGeMr4w%3D%3D"  
![9](https://github.com/cucxinan/photo.git/8.png)     
&emsp;&emsp;10.在控制台中进行手工cookie值的键入，刷新界面，此时发现网站在没有键入用户名且不知道密码的情况下自动登入了账户admin，实验假设得到验证  
![10](https://github.com/cucxinan/photo.git/9.png)     
&emsp;&emsp;**实验结论：对于使用固定密码本的ECB加密模式，在使用简单加密模式时，容易受到规律性的约束继而受到攻击，网站进行简单身份认证与身份信息存储时需要从多方进行考虑，尽量避免使用简单ECB。**

&emsp;&emsp;（额外测试实验：登入adminadmin账户，运用相同的和实验步骤，对所得的base-64代码进行编解码，采用直接键入cookie的形式实现该账户无需输入密码的的自动登入。  
     ![1](https://github.com/cucxinan/photo.git/10.png)     
     ![2](https://github.com/cucxinan/photo.git/11.png)   

由此可得，对于大多数的具有重复性内容的账户，只要掌握了编解码规律，就能通过多次实验得出分组长度和加密规则，继而对安全性不强的网站进行攻击，且切实可行。）

**五、扩展实验**


&emsp;&emsp;1.建立一个新用户，账户名设置为“password（七个空格”，密码设置为“admin（三个空格）”，以适应编解码的|用户名|分隔符|密码|ECB加密模式  
     ![1](https://github.com/cucxinan/photo.git/12.png)   

&emsp;&emsp;2. 取出cookie值，对其进行base-64编码，得到"\x98\xD1S\x89\fVIl\xB5\x06QEk7wz\xC8q\xEA\xE5\xCD\xBD)\xF1"，使用Burp decoder工具对该base-64码进行位数分解与位置互换，使得编码的位置变为|密码|分隔符|用户名|形式，即" xC8q\xEA\xE5\xCD\xBD)\xF1\x98\xD1S\x89\fVIl\xB5\x06QEk7wz\ "

&emsp;&emsp;3.将位置更改后解码得到的对应的cookie值键入控制台，刷新界面，此时可以发现网站自动登录了admin账户而没有输入相应的账户名与密码。  
     ![2](https://github.com/cucxinan/photo.git/13.png)

     