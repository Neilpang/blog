---
layout: post
status: publish
published: true
title: 学一点 mysql 双机异地热备份----mysql主从，主主备份原理及实践
author: Neil




author_login: Neil

wordpress_id: 338
wordpress_url: http://blog.byneil.com/?p=338
date: '2014-02-15 18:34:37 +0000'
date_gmt: '2014-02-15 10:34:37 +0000'
categories:
- 服务器建站
tags: []
comments: []
---
<p>简单介绍mysql双机，多机异地热备简单原理实战。</p>
<p>双机热备的概念简单说一下，就是要保持两个数据库的状态自动同步。对任何一个数据库的操作都自动应用到另外一个数据库，始终保持两个数据库数据一致。 这样做的好处多。 1. 可以做灾备，其中一个坏了可以切换到另一个。 2. 可以做负载均衡，可以将请求分摊到其中任何一台上，提高网站吞吐量。&nbsp; 对于异地热备，尤其适合灾备。废话不多说了。我们直接进入主题。 我们会主要介绍两部分内容：</p>
<p>一， mysql 备份工作原理</p>
<p>二， 备份实战</p>
<p>&nbsp;</p>
<p>我们开始。</p>
<p>我使用的是mysql 5.5.34，</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image6.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb5.png" alt="image" width="511" height="44" border="0" /></a></p>
<p>&nbsp;</p>
<p>一， mysql 备份工作原理</p>
<p>简单的说就是把 一个服务器上执行过的sql语句在别的服务器上也重复执行一遍， 这样只要两个数据库的初态是一样的，那么它们就能一直同步。</p>
<p>&nbsp;</p>
<p>当然这种复制和重复都是mysql自动实现的，我们只需要配置即可。</p>
<p>&nbsp;</p>
<p>我们进一步详细介绍原理的细节， 这有一张图：</p>
<p><img src="https://blog.byneil.com/wp-content/uploads/2014/08/0_1330439010P7lI.gif" alt="" /></p>
<p>上图中有两个服务器， 演示了从一个主服务器（master） 把数据同步到从服务器（slave）的过程。</p>
<p>这是一个主-从复制的例子。 主-主互相复制只是把上面的例子反过来再做一遍。 所以我们以这个例子介绍原理。</p>
<p>&nbsp;</p>
<p>对于一个mysql服务器， 一般有两个线程来负责复制和被复制。当开启复制之后。</p>
<p>&nbsp;</p>
<p>1. 作为主服务器Master，&nbsp; 会把自己的每一次改动都记录到 二进制日志 Binarylog 中。 （从服务器会负责来读取这个log， 然后在自己那里再执行一遍。）</p>
<p>&nbsp;</p>
<p>2. 作为从服务器Slave， 会用master上的账号登陆到 master上， 读取master的Binarylog,&nbsp; 写入到自己的中继日志 Relaylog， 然后自己的sql线程会负责读取这个中继日志，并执行一遍。&nbsp; 到这里主服务器上的更改就同步到从服务器上了。</p>
<p>&nbsp;</p>
<p>在mysql上可以查看当前服务器的主，从状态。 其实就是当前服务器的 Binary（作为主服务器角色）状态和位置。 以及其RelayLog（作为从服务器）的复制进度。</p>
<p>&nbsp;</p>
<p>例如我们在主服务器上查看主状态：</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image7.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb6.png" alt="image" width="644" height="181" border="0" /></a></p>
<pre class="csharpcode">mysql> show master status\G
*************************** 1. row ***************************
            File: mysql-bin.000014
        Position: 107
    Binlog_Do_DB: 
Binlog_Ignore_DB: mysql,information_schema,performance_schema,amh
1 row <span class="kwrd">in</span> set (0.00 sec)</pre>
<p>稍微解释一下这几行的意思：</p>
<p>1. 第一行表明 当前正在记录的 binarylog文件名是： mysql-bin.000014.</p>
<p>我们可以在mysql数据目录下，找到这个文件：</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image8.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb7.png" alt="image" width="545" height="101" border="0" /></a></p>
<p>&nbsp;</p>
<p>2.&nbsp; 第二行， 107. 表示当前的文件偏移量， 就是写入在mysql-bin.000014 文件的记录位置。</p>
<p>这两点就构成了 主服务器的状态。&nbsp; 配置从服务器的时候，需要用到这两个值。 告诉从服务器从哪读取主服务器的数据。 （从服务器会登录之后，找到这个日志文件，并从这个偏移量之后开始复制。）</p>
<p>&nbsp;</p>
<p>3. 第三行，和第四行，表示需要记录的数据库和需要忽略的数据库。 只有需要记录的数据库，其变化才会被写入到mysql-bin.000014日志文件中。&nbsp; 后面会再次介绍这两个参数。</p>
<p>&nbsp;</p>
<p>我们还可以在从服务器上，查看从服务器的复制状态。</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image9.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb8.png" alt="image" width="627" height="484" border="0" /></a></p>
<pre class="lang:default decode:true " >
 mysql> show slave status\G
 *************************** 1. row ***************************
                Slave_IO_State: Waiting for master to send event
                   Master_Host: 198.**.***.***
                   Master_User: r*******
                   Master_Port: 3306
                 Connect_Retry: 60
               Master_Log_File: mysql-bin.000014
           Read_Master_Log_Pos: 107
                Relay_Log_File: mysqld-relay-bin.000013
                 Relay_Log_Pos: 253
         Relay_Master_Log_File: mysql-bin.000014
              Slave_IO_Running: Yes
             Slave_SQL_Running: Yes
               Replicate_Do_DB:
           Replicate_Ignore_DB: mysql,information_schema,amh,performance_schema
            Replicate_Do_Table:
        Replicate_Ignore_Table:
       Replicate_Wild_Do_Table:
   Replicate_Wild_Ignore_Table:
                    Last_Errno: 0
                    Last_Error:
                  Skip_Counter: 0
           Exec_Master_Log_Pos: 107
               Relay_Log_Space: 556
               Until_Condition: None
                Until_Log_File:
                 Until_Log_Pos: 0
            Master_SSL_Allowed: No</pre>
<p>&nbsp;</p>
<p>我们还是来重点解释途中的红圈的部分：</p>
<p>1.&nbsp; Master_host 指的是 主服务器的地址。</p>
<p>2. Master_user 指的是主服务器上用来复制的用户。&nbsp; 从服务器会用此账号来登录主服务。进行复制。</p>
<p>3. Master_log_file 就是前面提到的， 主服务器上的日志文件名.</p>
<p>4. Read_Master_log_pos 就是前面提到的主服务器的日志记录位置， 从服务器根据这两个条件来选择复制的文件和位置。</p>
<p>5. Slave_IO_Running:&nbsp; 指的就是从服务器上负责读取主服务器的线程工作状态。 从服务器用这个专门的线程链接到主服务器上，并把日志拷贝回来。</p>
<p>6. Slave_SQL_Running: 指的就是专门执行sql的线程。 它负责把复制回来的Relaylog执行到自己的数据库中。 这两个参数必须都为Yes 才表明复制在正常工作。</p>
<p>&nbsp;</p>
<p>其他的参数之后再介绍。</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>二， mysql 双机热备实战</p>
<p>了解了上面的原理之后， 我们来实战。 这里有两个重点， 要想同步数据库状态， 需要相同的初态，然后配置同步才有意义。 当然你可以不要初态，这是你的自由。 我们这里从头开始配置一遍。</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image10.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb9.png" alt="image" width="644" height="337" border="0" /></a></p>
<p>&nbsp;</p>
<p>我们先以A服务器为起点，&nbsp; 配置它的数据库同步到B。&nbsp; 这就是主-从复制了。 之后再反过来做一次，就可以互相备份了。</p>
<p>&nbsp;</p>
<p>1， 第一步，</p>
<p>在A上面创建专门用于备份的 用户：</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image11.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb10.png" alt="image" width="622" height="73" border="0" /></a></p>
<pre class="lang:default decode:true " >grant replication slave on *.* to 'repl_user'@'192.***.***.***' identified by 'hj34$%&amp;amp;mnkb';</pre>
<p>&nbsp;</p>
<p>上面把ip地址换成B机器的ip地址。 只允许B登录。安全。</p>
<p>用户名为： repl_user</p>
<p>密码为： hj34$********nkb</p>
<p>这个等会在B上面要用。</p>
<p>&nbsp;</p>
<p>2. 开启主服务器的 binarylog。</p>
<p>很多服务器是默认开启的，我们这里检查一下：</p>
<p>打开 /etc/my.cnf</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image12.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb11.png" alt="image" width="390" height="321" border="0" /></a></p>
<p>&nbsp;</p>
<p>我来解释一下红框中的配置：</p>
<p>前面三行， 你可能已经有了。</p>
<p>binlog-do-db 用来表示，只把哪些数据库的改动记录到binary日志中。 可以写上关注hello数据库。 但是我把它注释掉了。 只是展示一下。 可以写多行，表示关注多个数据库。</p>
<p>binlog-ignore-db 表示，需要忽略哪些数据库。我这里忽略了其他的4个数据库。</p>
<p>&nbsp;</p>
<p>后面两个用于在 双主（多主循环）互相备份。 因为每台数据库服务器都可能在同一个表中插入数据，如果表有一个自动增长的主键，那么就会在多服务器上出现主键冲突。&nbsp; 解决这个问题的办法就是让每个数据库的自增主键不连续。&nbsp; 上图说是， 我假设需要将来可能需要10台服务器做备份， 所以auto-increment-increment 设为10.&nbsp;&nbsp; 而 auto-increment-offset=1 表示这台服务器的序号。 从1开始， 不超过auto-increment-increment。</p>
<p>这样做之后， 我在这台服务器上插入的第一个id就是 1， 第二行的id就是 11了， 而不是2.</p>
<p>（同理，在第二台服务器上插入的第一个id就是2， 第二行就是12， 这个后面再介绍） 这样就不会出现主键冲突了。 后面我们会演示这个id的效果。</p>
<p>&nbsp;</p>
<p>3.&nbsp; 获取主服务器状态， 和同步初态。</p>
<p>假设我现在有这些数据库在A上面。</p>
<p>如果你是全新安装的， 那么不需要同步初态，直接跳过这一步，到后面直接查看主服务器状态。</p>
<p>这里我们假设有一个 hello 数据库作为初态。</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image13.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb12.png" alt="image" width="225" height="196" border="0" /></a></p>
<p>&nbsp;</p>
<p>先锁定 hello数据库：</p>
<pre class="csharpcode">FLUSH TABLES WITH READ LOCK;</pre>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image14.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb13.png" alt="image" width="302" height="78" border="0" /></a></p>
<p>&nbsp;</p>
<p>然后导出数据：</p>
<p>我这里只需要导出hello数据库， 如果你有多个数据库作为初态的话， 需要导出所有这些数据库：</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image15.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb14.png" alt="image" width="520" height="78" border="0" /></a></p>
<p>&nbsp;</p>
<p>然后查看A服务器的binary日志位置：</p>
<p>记住这个文件名和 位置， 等会在从服务器上会用到。</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image16.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb151.png" alt="image" width="497" height="140" border="0" /></a></p>
<p>&nbsp;</p>
<p>主服务器已经做完了， 可以解除锁定了：</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image17.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb161.png" alt="image" width="372" height="95" border="0" /></a></p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>4.&nbsp; 设置从服务器 B 需要复制的数据库</p>
<p>打开从服务器 B 的 /etc/my.cnf 文件：</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image18.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb17.png" alt="image" width="406" height="236" border="0" /></a></p>
<p>&nbsp;</p>
<p>解释一下上面的内容。</p>
<p>server-id 必须保证每个服务器不一样。 这可能和循环同步有关。 防止进入死循环。</p>
<p>replicate-do-db 可以指定需要复制的数据库， 我这里注掉了。 演示一下。</p>
<p>replicate-ignore-db 复制时需要排除的数据库， 我使用了，这个。 除开系统的几个数据库之外，所有的数据库都复制。</p>
<p>relay_log 中继日志的名字。 前面说到了， 复制线程需要先把远程的变化拷贝到这个中继日志中， 在执行。</p>
<p>log-slave-updates 意思是，中继日志执行之后，这些变化是否需要计入自己的binarylog。 当你的B服务器需要作为另外一个服务器的主服务器的时候需要打开。&nbsp; 就是双主互相备份，或者多主循环备份。 我们这里需要， 所以打开。</p>
<p>&nbsp;</p>
<p>保存， 重启mysql。</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>5. 导入初态， 开始同步。</p>
<p>把刚才从A服务器上导出的 hello.sql 导入到 B的hello数据库中， 如果B现在没有hello数据库，请先创建一个， 然后再导入：</p>
<p>创建数据库：</p>
<pre class="lang:default decode:true " >create database hello default charset utf8;</pre>
<p>把hello.sql 上传到B上， 然后导入：</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image19.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb18.png" alt="image" width="486" height="39" border="0" /></a></p>
<p>&nbsp;</p>
<p>如果你刚才导出了多个数据库， 需要把他们都一一上传导入。</p>
<p>&nbsp;</p>
<p>开启同步, 在B服务器上执行：</p>
<pre class="csharpcode crayon-selected"> CHANGE MASTER TO 
       MASTER_HOST=<span class="str">'192.***.***.***'</span>, 
       MASTER_USER=<span class="str">'repl_user'</span>, 
       MASTER_PASSWORD=<span class="str">'hj3****'</span>, 
       MASTER_LOG_FILE=<span class="str">'mysql-bin.000004'</span>, 
       MASTER_LOG_POS=7145;</pre>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image20.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb19.png" alt="image" width="468" height="132" border="0" /></a></p>
<p>&nbsp;</p>
<p>上面几个参数我就不解释了。 前面说过了。</p>
<p>&nbsp;</p>
<p>重启mysql，&nbsp; 然后查看slave线程开启了没：</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image21.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb20.png" alt="image" width="611" height="417" border="0" /></a></p>
<p>&nbsp;</p>
<p>注意图中的红框， 两个都是Yes， 说明开启成功。</p>
<p>Slave_IO_Running: Yes</p>
<p>Slave_SQL_Running: Yes</p>
<p>如果其中一个是No， 那就说明不成功。需要查看mysql的错误日志。 我在第一次做的时候就遇到这个问题。有时候密码填错了， 有时候防火墙的3306没有打开。ip地址不对，等等。 都会导致失败。</p>
<p>&nbsp;</p>
<p>我们看错误日志: mysql的错误日志一般在：</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image22.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb21.png" alt="image" width="477" height="109" border="0" /></a></p>
<p>文件名应该是你的机器名， 我这里叫做host1.err 你换成你自己的。</p>
<p>&nbsp;</p>
<p>到这里主-从复制已经打开了。 我们先来实验一下。</p>
<p>我们在A的数据库里面去 添加数据：</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image23.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb22.png" alt="image" width="623" height="489" border="0" /></a></p>
<p>我在A的 hello数据库的test表中 连续插入了3条数据，&nbsp; 注意看他们的自增长id， 分别是1,11,21.&nbsp; 知道这是为什么吗。 前面已经说过了，不懂再回去看。</p>
<p>&nbsp;</p>
<p>我们去看一下B数据库有没有这三条数据：</p>
<p>&nbsp;</p>
<p>打开B的数据库：</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image24.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb23.png" alt="image" width="410" height="147" border="0" /></a></p>
<p>发现已经在这了。 这里效果不直观。</p>
<p>&nbsp;</p>
<p>此时不要在B中修改数据。 我们接着配置从B到A的复制。&nbsp; 如果你只需要主从复制的话， 到这里就结束了。后面可以不看了。 所有A中的修改都能自动同步到B， 但是对B的修改却不能同步到A。 因为是单向的。 如果需要双向同步的话，需要再做一次从B到A的复制。</p>
<p>&nbsp;</p>
<p>基本跟上面一样：我们简单一点介绍：</p>
<p>&nbsp;</p>
<p>1. 在B中创建用户；</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image25.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb24.png" alt="image" width="644" height="69" border="0" /></a></p>
<p>&nbsp;</p>
<p>2. 打开 /etc/my.cnf ， 开启B的binarylog：</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image26.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb25.png" alt="image" width="382" height="370" border="0" /></a></p>
<p>注意红框中所新添加的部分。</p>
<p>&nbsp;</p>
<p>3. 我们不需要导出B的初态了，因为它刚刚才从A导过来。&nbsp; 直接记住它的master日志状态：</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image27.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb26.png" alt="image" width="554" height="144" border="0" /></a></p>
<p>记住这两个数值，等会在A上面要用。</p>
<p>B服务器就设置完了。</p>
<p>&nbsp;</p>
<p>4. 登录到A 服务器。 开启中继：</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image28.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb27.png" alt="image" width="386" height="383" border="0" /></a></p>
<p>注意框中心添加的部分， 不解释了。</p>
<p>&nbsp;</p>
<p>5. 启动同步：</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image29.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb28.png" alt="image" width="491" height="161" border="0" /></a></p>
<p>上面的ip地址是B的ip地址， 因为A把B当做master了。 不解释了。</p>
<p>&nbsp;</p>
<p>然后重启mysql服务。</p>
<p>然后查看，slave状态是否正常：</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image30.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb29.png" alt="image" width="563" height="428" border="0" /></a></p>
<p>图中出现了两个No。</p>
<p>Slave_IO_Running: No</p>
<p>Slave_SQL_Running: No</p>
<p>说明slave没有成功， 即，从B到A的同步没有成功。 我们去查看mysql错误日志，前面说过位置：</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image31.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb30.png" alt="image" width="644" height="48" border="0" /></a></p>
<p>&nbsp;</p>
<p>找到&nbsp; 机器名.err 文件，打开看看：</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image32.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb31.png" alt="image" width="593" height="163" border="0" /></a></p>
<p>&nbsp;</p>
<p>看图中的error信息。&nbsp; 说找不到中继日志文件。</p>
<p>这是因为我们在配置A的中继文件时改了中继文件名，但是mysql没有同步。解决办法很简单。</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image33.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb32.png" alt="image" width="602" height="94" border="0" /></a></p>
<p>&nbsp;</p>
<p>先停掉mysql服务。&nbsp; 找到这三个文件，把他们删掉。 一定要先停掉mysql服务。不然还是不成功。你需要重启一下机器了。 或者手动kill mysqld。</p>
<p>好了， 启动mysql之后。 我们在来检查一下slave状态：</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image34.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb33.png" alt="image" width="634" height="282" border="0" /></a></p>
<p>&nbsp;</p>
<p>注意图中两个大大的Yes。&nbsp; 哈哈。</p>
<p>&nbsp;</p>
<p>Slave_IO_Running: Yes</p>
<p>Slave_SQL_Running: Yes</p>
<p>&nbsp;</p>
<p>证明从B到A的复制也成功了。</p>
<p>&nbsp;</p>
<p>此时我们去B服务器中插入几条数据试试：</p>
<p>&nbsp;</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image35.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb34.png" alt="image" width="442" height="218" border="0" /></a></p>
<p>我在B中插入了两条数据。 注意看他们的id。&nbsp; 不解释。</p>
<p>&nbsp;</p>
<p>然后我们，登录去A中看看，A数据库变了没。</p>
<p><a href="http://blog.byneil.com/wp-content/uploads/2014/02/image36.png"><img style="display: inline; border-width: 0px;" title="image" src="https://blog.byneil.com/wp-content/uploads/2014/08/image_thumb35.png" alt="image" width="475" height="303" border="0" /></a></p>
<p>可以看到已经自动同步到A了。</p>
<p>&nbsp;</p>
<p>至此， AB双主互相热备就介绍完了。</p>
<p>&nbsp;</p>
<p>原理其实很简单，是不是。</p>
<p>&nbsp;</p>
<p>理解了这个原理， 多机循环互备就简单了。这里就不再展开了。</p>
<p>&nbsp;</p>
<p>花了一天时间写这个博客，大家要顶啊。</p>
<p>&nbsp;</p>
<p>欢迎大家访问我的独立博客：<a href="http://blog.byneil.com">http://blog.byneil.com</a> 多多交流。</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>参考：</p>
<p>1. mysql-keepalived-实现双主热备读写分离</p>
<p><a title="http://gitsea.com/2013/06/16/mysql-keepalived-%E5%AE%9E%E7%8E%B0%E5%8F%8C%E4%B8%BB%E7%83%AD%E5%A4%87%E8%AF%BB%E5%86%99%E5%88%86%E7%A6%BB/" href="http://gitsea.com/2013/06/16/mysql-keepalived-%E5%AE%9E%E7%8E%B0%E5%8F%8C%E4%B8%BB%E7%83%AD%E5%A4%87%E8%AF%BB%E5%86%99%E5%88%86%E7%A6%BB/" target="_blank">http://gitsea.com/2013/06/16/mysql-keepalived-%E5%AE%9E%E7%8E%B0%E5%8F%8C%E4%B8%BB%E7%83%AD%E5%A4%87%E8%AF%BB%E5%86%99%E5%88%86%E7%A6%BB/</a></p>
<p>&nbsp;</p>
<p>2. MySQL数据同步【双主热备】<a title="http://www.cnblogs.com/zhongweiv/archive/2013/02/01/mysql_replication_circular.html" href="http://www.cnblogs.com/zhongweiv/archive/2013/02/01/mysql_replication_circular.html" target="_blank">http://www.cnblogs.com/zhongweiv/archive/2013/02/01/mysql_replication_circular.html</a></p>
<p>&nbsp;</p>
<p>3. Mysql双机热备实现</p>
<p><a title="http://yunnick.iteye.com/blog/1845301" href="http://yunnick.iteye.com/blog/1845301">http://yunnick.iteye.com/blog/1845301</a></p>
<p>&nbsp;</p>
<p>4. 高性能Mysql主从架构的复制原理及配置详解<a title="http://blog.csdn.net/hguisu/article/details/7325124" href="http://blog.csdn.net/hguisu/article/details/7325124">http://blog.csdn.net/hguisu/article/details/7325124</a></p>
<p>&nbsp;</p>
<p>5.&nbsp;&nbsp; <a title="mysql 基于 master-master 的双机热备配置" href="http://mozillazg.com/2013/06/mysql-master-master.html">mysql 基于 master-master 的双机热备配置</a></p>
