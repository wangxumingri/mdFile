#Linux



## 常用命令：

###  `rm`

​	-r : 递归删除

​	-f：忽略确认



### **`tar 归档命令:`**

参数：

​	-c：创建

​	-v：详情

​	-f ：文件名

​	-t ：列表

​	-x：解压

```
打包不压缩：
	tar -cvf 打包后的名称.tar  被打包的文件（可以有多个文件，空格分割）
	tar -cvf test.tar a.txt b.txt
	(只是将目标打成一个包，并未压缩，后缀是.tar)
打包压缩：
	tar -czvf 打包压缩后的名称.tar.gz  文件
	tar -czvf test.tar.gz a.txt /aa/
	(将目标打成一个包，并进行压缩,后缀是.tat.gz)
解压tar文件：
	tar -xf xxx.tar
解压tar.tz文件：
	tar -zxvf
查看：
	tar -tvf 压缩包名称 
	tar -tvf test.tar  :查看test.tar包的内容
解压：
	tar -xvf 压缩包名称   				 ---解压到当前目录
	tar -xvf 压缩包名称  -C 目录 	        ---解压到指定目录
```

**`tips：`**

​	tar与tar.gz文件的操作区别：多了一个   `z`

```
在linux系统中不根据扩展名，判断文件类型：需要使用 file 命令查看：
	file 文件名
```

`du `

​	-h 查看目录下每个文件的大小

​	-sh 查看目录的总大小

***

### **`vim命令:`**

​	`：wq`：保存退出

​	 `:q!`：强制退出，不保存

​	`ctrl+s`：锁定屏幕

​	`ctrl+q`：解锁屏幕

### **`ll命令:`**

​	`-h` : 查看文件大小，多个文件，空格分割

ls  目录   * **文件名** *  列出目录下的指定文件



***

```
Linux一些包都在系统盘里：
	虚拟机设置---CD/DVD---选中启动时连接---选中镜像
进入虚拟机：
	mount  /dev/cdrom  /mnt/ 将镜像挂载到 mnt
查看：ls /mnt/
	主要用的是Packages (注意大小写)
wc -l 统计数量
包结构：
	zsh    -4		.3		.10   -4.1 			X86				64	
	软件名  主版本号 次版本号   修订 release版本  CPU架构系统平台 支持的系统位数
tips：
	/dev/cdrom 是 /dev/sr0的快捷方式

```

### `systemctl:`



```
启动服务： 	systemctl start mysqld 		# 启动mysql服务
查看服务状态：systemctl status mysqld	   # 查看mysql服务状态
关闭服务：    systemctl stop mysqld		# 关闭mysql服务
```



### **`rpm命令:` 包管理**

​	**语法:**    **`rpm  参数  包名或包中的命令名`**

​	`--help`:查看命令帮助

​	**安装：**

​		`-ivh` : 安装包  

​			安装某些包时，有时会提示需要依赖，否则不让安装,解决方法：

​				1.可以rpm网站去下载

​				2.强制安装 :`-ivh  --nodeps`

​		**`tips：`**

```
安装时，如果提示 nokey，可以使用 rpm --import 导入RPM-GPG-KEY.对rpm的签名进行验证
一般位于 ： /etc/pki/rpm-gpg/RPM-GPG-KEY***
```

​	**查询:**

​		`-q` : 查询指定包是否已安装

​		`-qa` : 查询系统一共安装了多少包

​		`-qf` : 查询指定命令是哪个包安装的

​			`which zsh`：查询zsh命令的位置   ---> **/bin/zsh**

​			`-qf ` **/bin/zsh** :  查询命令所属的包 ： --->zsh-xxx64

​		`-ql` : 查询指定包，安装后产生了哪些文件和目录

​		`-pql` : 预先查看安装包后，会产生哪些文件和目录（**软件未安装**）

​				可以查看配置文件

​	**更新：**

​		`-Uvh`  :后跟包名+版本号

​	**卸载：**

​		`-e` : 卸载包 (**只写包名即可**)

​		`-e  --nodeps` :强制卸载：有依赖关系包，卸载时需要

`tips:`

​	`rpm -qf  'which  zsh'`

```
使用 反引号，包裹命令，优先执行被包裹的命令，然后将其的执行结果作为前者的条件
```

***

### `yum 命令`

​	自动下载安装软件包，自动解决依赖关系问题，基于C/S架构

​	需要设置yum源