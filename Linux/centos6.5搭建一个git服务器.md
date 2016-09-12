# centos6.5搭建一个git服务器
## :cyclone:服务端安装
### 登录服务器安装
```shell
#yum install perl openssh git
```
:heavy_exclamation_mark:装不上,那就装下面的依赖
```shell
#yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel perl-devel
```
### 建立一个用户
```shell
#adduser --system --shell /bin/sh --create-home --home-dir /home/git git
#cd /home/git
#mkdir repositories
#chown git:git -R ./repositories
#chmod 700 ./repositories
```
### 切换至刚建的git用户
```shell
#su git
#git clone git://github.com/sitaramc/gitolite
#mkdir -p $HOME/bin
#gitolite/install -to $HOME/bin
```
:heavy_exclamation_mark:这里可能会报错缺少模块,就需要 切换到root 安装缺失模块.比如perl-Time-HiRes 
```shell
#yum install perl-Time-HiRes
#su git
#gitolite/install -to $HOME/bin
```
然后这里安装的就完了.:white_check_mark:
## :cyclone:本机, 本机我这里是Mac,
```shell
#ssh-keygen -t rsa //不需要密码一路回车
#cd ./.ssh
```
### 里面有*id_ras.pub* 和*id_rsa* ,一个是公钥,一个是私钥. 如果之前你装过openssl产生过密钥,那这里就不要覆盖了
复制到服务器的*/tmp*
```shell
#scp ~/.ssh/id_rsa.pub server_username@server_host:/tmp
```
### 输入密码回到服务器,
```shell
#cd /tmp
#mv id_rsa.pub admin.pub 
```
####　为什么要改成admin.pub 因为gitolite根据这个文件名来设立帐号.我这里用admin
```shell
#su git 切换到git用户
#$HOME/bin/gitolite setup -pk admin.pub
```
###　然后去/home/git/repositories 里面,可以看见仓库文件.gitolite-admin.git 和test.git 一个是管理仓库的,一个是测试用
本机拉服务器代码
```shell
#git clone git@server_host:gitolite-admin
```
进入仓库后可以看到conf 和keydir ,conf/gitolite.conf 是添加用户/仓库的配置, keydir 是放对应用户的公钥.
修改好后可以直接push了.
debian的和上面一样，唯一需要注意的adduser 的参数不同
```shell
#adduser --system --shell /bin/sh --home  /home/git git 
```
获取并配置gitosis-admin
### 在本地执行，获取gitosis管理项目
```shell
$git clone git@xxx:gitosis-admin.git  
$vi  gitosis-admin/gitosis.conf  //编辑gitosis-admin配置文件
```
###　在gitosis.conf底部增加
```txt
@devgroup = admin //这里的用户名字
```
```txt
repo myrepos
 RW+ = @devgroup
```

##### VI下按ZZ两次会执行自动保存并退出，完成后执行
```shell
$ git commit -a -m “xxx xx” 
```
##### 要记住的是，每次添加新文件必须执行git add .或者git add filename。
##### 修改了文件以后一定要PUSH到服务器，否则不会生效。
```shell
$ git push
```
## :cyclone:如何使用git创建项目(本地)

* 建立一个存放工程的文件夹
* git init命令用于初始化当前所在目录的这个项目
* 创建项目文件
* git status 查看项目状态
* git add .   给这个项目制作一个快照snapshot（快照只是登记留名，快照不等于记录在案，git管快照叫做索引index)
* git commit用于将快照里登记的内容永久写入git仓库中
  直接提交 git commit -a -m "注解"
注意：无法把新增文件或文件夹加入进来，如果你新增了文件或文件夹，那么就要先git add . 再git commit
7. git log -p 确认日志


## :cyclone:建立远程仓库


1. 服务器建立repos （见上述服务器设置）
2. git remote add  [shortname] git@server_addr:myrepos.git
3. git push [shortname] master<br>
也可以2、3不合并直接 git push git@server_addr:myrepos.git master

####　查看远程仓库信息
```shell
git remote show [remote-name]
```
返回信息：
```txt
* remote [remote-name]
  Fetch URL: git@192.168.10.1:myrepos.git
  Push  URL: git@192.168.10.1:myrepos.git
  HEAD branch: master
  Remote branch:
    master tracked
  Local ref configured for 'git push':
    master pushes to master (up to date)
```
它告诉你如果是在 master 分支，就可以用
```shell
git pull 命令抓取数据合并到本地
 ```
远程仓库的删除和重命名<br>
```shell
git remote rename  [shortname]
```
命令修改某个远程仓库的简短名称<br>
移除对应的远端仓库，可以运行
```shell
git remote rm  [shortname]
```


