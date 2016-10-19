#### 1. 使用crontab -e命令
这个命令的使用比较简单。直接输入
```shell
~# crontab -e
```
就会打开一个编辑窗口，第一行会有内容格式的提示：
```shell
# m h  dom mon dow   command
```
具体意义表示：分钟 小时 日期 月份 星期 命令，在某月（mon）的某天（dom）或者星期几（dow）的几点（h，24小时制）几分（m）执行某个命令（command），
*表示任意时间。例如：
```shell
3 * * * * /home/meng/hello.sh
```
就是：每小时的03时执行/home/meng/下的hello.sh脚本。
在保存之后，根据屏幕下面的提示输入Ctrl+X退出，此时会提示是否保存，输入Y；提示输入文件名，并且有一个临时的文件名，由于只是测试，直接回车保存。
注意：在完成编辑以后，要重新启动cron进程：
```shell
~# /etc/init.d/cron restart
```
观察运行结果，会发现hello.sh会每隔一小时，在03分时被执行一次。
同理PHP脚本也可运行，例如CI框架：
```shell
* * * * *  php -f /disk2/www/wanpiApi/index.php v1_0/api/controllers/funciton
```
* 在使用这个命令时，最大的担心就是在系统重启以后是否还能顺利执行呢？我重启系统以后发现一切正常，于是打消了这个顾虑。但是，仍然有一个问题，一般情况下，服务器都是在重启后处于登录状态下，并没有用户登入。那么如果我在执行crontab -e命令时，不是使用root账户，那么在系统重启之后是否还会顺利执行呢？

#### 2. 编辑crontab文件
crontab位于/ect/文件夹，在[ http://wiki.ubuntu.org.cn/CronHowto]( http://wiki.ubuntu.org.cn/CronHowto)上有关于它的详细介绍，但是我看的不是太懂。
打开crontab文件，如果没有编辑过可以看到如下类似的内容：
```shell
# /etc/crontab: system-wide crontab 
# Unlike any other crontab you don't have to run the `crontab' 
# command to install the new version when you edit this file 
# and files in /etc/cron.d. These files also have username fields, 
# that none of the other crontabs do.
SHELL=/bin/sh 
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
# m h dom mon dow user  command 
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly 
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily ) 
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly ) 
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
```
* 由于对脚本的认知有限，不能详细解释每个命令的含义。在第10行，同样定义了文件内容的格式。可以看到比使用crontab -e命令时，多了一个user。它表示了执行命令的用户，如果是root，就表明是系统用户。于是，我加了如下一行：
```shell
3 * * * * root /home/meng/hello.sh
```
然后保存
