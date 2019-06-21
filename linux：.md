linux：

系统安装与配置：

​	VMware15

​	centos7 64位

​	删除不需要的硬件：声卡，打印机

​	配置网络：

​	设置固定IP

开发环境搭建：

​	JDK

​	mysql

​	

ctrl+c 停止ping



ctrl  s  锁死屏幕

ctrl  q 解锁屏幕





/etc/locale.conf : 修改的是localectl

etc/profile 和  /etc/sysconfig/i18n :修改的是locale 和 LANG



1、系统必须安装中文语言包才行
\# yum -y groupinstall chinese-support

2、仅仅有语言包还不行，我们得设置相应的字符集



临时生效  # export LANG="zh_CN.UTF-8"    # 设置为中文  # export LANG="en_US.UTF-8"    # 设置为英文，我比较喜欢这样 export LANG=C  

 永久生效， 编辑/etc/sysconfig/i18n（最好reboot一下）  LANG="zh_CN.UTF-8" 

或者，编辑 /etc/profile配置文件，添加如下一行  export LANG="zh_CN.UTF-8" # 重新载入  # . /etc/profile     

\## 查看当前的字符集 
\# echo $LANG

好了，经过上面的