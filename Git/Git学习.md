# Git

## git安装

无脑下一步

## git常用命令

1.安装完之后首先设置签名

```bash
git config --global user.name 用户名
git config --globe user.email 邮箱
```

2.基础命令使用顺序

```bash
git init  						#初始化本地库
git status 						#查看本地库状态
git add 文件名					  #添加到暂存区
git commit -m"日志信息" 文件名     #提交本地库
git reflog   					#查看历史信息
git reset --hard 版本号	      #版本回退
```

## git分支操作

```bash
git branch 分支名				#创建分支
git branch -v				#查看分支
git checkout 分支名			#切换分支
git merge 分支名				#把指定的分支合并到当前分支上，一般是切换到master的分支上进行合并其他分支
```

## gitHub操作

```bash
git remote -v				#查看当前所有远程地址名
git remote add 别名 远程地址		#起别名
git push 别名 分支		#推送本地分支上的内容到远程仓库
git clone 远程地址		#将远程仓库克隆到本地
git pull 远程库地址别名 远程分支名   #将远程仓库分支最新内容拉下来后与当前本地分支直接合并
```

## SSH免密登陆

生成公钥

```
1.进入到家目录		C:\Users\盐值不高的咸鱼  查看是否有.ssh文件
2.右键 Git Bash Here
3. ssh-keygen -t rsa -C （github邮箱）
4.添加到Github
```

## IDEA整合Github

### 1.配置Git忽略文件

1. 创建忽略规则文件 xxxx.ignore（前缀名随便起，建议是 git.ignore），这个文件的存放位置原则上在哪里都可以，为了便于让~/.gitconfig 文件引用，建议也放在用户家目录下 

2. ```bash
   # Compiled class file 
   *.class 
   # Log file 
   *.log 
    
   # BlueJ files 
   *.ctxt 
    
   # Mobile Tools for Java (J2ME) 
   .mtj.tmp/ 
    
   # Package Files # 
   *.jar 
   *.war 
   *.nar 
   *.ear 
   *.zip 
   *.tar.gz 
   *.rar 
    
   # virtual machine crash logs, see http://www.java.com/en/download/help/error_hotspot.xml 
   hs_err_pid* 
    
   .classpath 
   .project 
   .settings 
   target 
   .idea 
   *.iml
   ```

3. 在.gitconfig 文件中引用忽略配置文件（此文件在 Windows 的家目录中） 

   ```bash
   [user]
   	email = 17852846895@163.com
   	name = haodezhi
   [http]
   	sslVerify = false
   [core]
   	excludesfile = C:/Users/盐值不高的咸鱼/git.ignore 
   ```

4. **定位Git程序**

   <img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230530212345753.png" alt="image-20230530212345753" style="zoom:67%;" />

5. **设置Github账号**

   <img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230530215615248.png" alt="image-20230530215615248" style="zoom:80%;" />

6. **初始化本地库**

   <img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230530212510137.png" alt="image-20230530212510137" style="zoom:67%;" />

   <img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230530221042550.png" alt="image-20230530221042550" style="zoom:80%;" />

6. **添加到暂存区、提交到本地库**

   <img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230530212607005.png" alt="image-20230530212607005" style="zoom: 80%;" />

7. **添加到仓库**

   <img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230530215302443.png" alt="image-20230530215302443" style="zoom:80%;" />

   <img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230530215224170.png" alt="image-20230530215224170" style="zoom:67%;" />

8.**Clone仓库**

<img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230530221600328.png" alt="image-20230530221600328" style="zoom:80%;" />

## 总结

### 非IDEA

1. **直接在某个盘符下面clone(不需要init)或者是创建一个文件夹直接Git Bash Here,然后clone，千万别init**（init是只有创建空文件夹，然后第一次上传到库的时候才用）

2. 然后进入克隆的文件夹，重新右键Git Bash Here

3. ```bash
   git status		#查看状态
   git add .		#添加所有文件  或者指定文件名（可以是文件夹名）
   git commit -m""
   git push 远程地址 master
   ```

   

### IDEA配合使用

1. 配置Git忽略文件
2. 在IDEA上建立Github账号跟码云账号
3. 不管是pull还是push，都直接点击下图的俩

<img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230530221933579.png" alt="image-20230530221933579" style="zoom:80%;" />

4. 然后直接在对应的Github或者是Gitee上建立仓库

   <img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230530222219538.png" alt="image-20230530222219538" style="zoom:80%;" />

5. Clone仓库是

   <img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230530221600328.png" alt="image-20230530221600328" style="zoom: 50%;" />

6. 之后Add等都直接在主目录下右键Git--> 
7. <img src="C:\Users\盐值不高的咸鱼\AppData\Roaming\Typora\typora-user-images\image-20230530222804833.png" alt="image-20230530222804833" style="zoom:67%;" />