# linux  
  
> 作者：xcx  
> 时间：2020/5/13  10：21  
  
-----------------  

## 记录一次linux 上连接5.7mysql 输入密码后  

* Mysql错误:Ignoring query to other database
经过检查后发现 是连接mysql 是  mysql -u root -p  **u** 没有加上 加上问题解决 