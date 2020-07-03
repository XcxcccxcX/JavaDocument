# linux  
  
> 作者：xcx  
> 时间：2020/4/17  09：56  
  
-----------------  

## 1. Permission denied, please try again.  

 记录一次热更新 无法连接服务器  
   
* 查看服务器端的日志文件 `vim /var/log/secure` 
  查看sshd服务systemctl  `status sshd`
    发现报错 `requirement "uid >= 1000" not met by user "root"`  
     ![image.png](https://i.loli.net/2020/04/17/kA24Kn7GYFUQHVI.png)  
     经过查询应该是权限的问题  首先将要操作的文件目录 权限设置最大 `sudo chmod -R 777 文件夹名称`

* 编辑`sshd_config`文件，输入：`vim /etc/ssh/sshd_config`
    发现 **`PermitRootLogin yes`** 是处于被注释的状态 于是查看了其他服务器 是属于没有被注释的状态 于是取消注释
     ![image.png](https://i.loli.net/2020/04/17/86M3Srwf7WEXOe9.png)

* 问题解决

## 2. 记录一次linux 上连接5.7mysql 输入密码后  
> 2020/5/13  10：21  

* Mysql错误:Ignoring query to other database
经过检查后发现 是连接mysql 是  mysql -u root -p  **u** 没有加上 加上问题解决 
