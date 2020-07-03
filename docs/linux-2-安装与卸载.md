# linux  
  
> 作者：xcx  
> 时间：2020/5/13  10：30  
  
-----------------  
环境: centos7 + yum安装的nginx


1. 检查nginx 服务是否在运行 `ps -ef | grep nginx`

2. 停止nginx 

3. 查找、删除Nginx相关文件  
    * 查看Nginx相关文件：`whereis nginx`
    * find查找相关文件 `find / -name nginx`
    * 依次删除find查找到的所有目录：`rm -rf /usr/sbin/nginx`

4. 再使用yum清理 `yum remove nginx`  依赖关系解决

      ok nginx 卸载完成！

    `sudo yum install -y nginx`   阿里云服务 安装nginx 


 **原贴**: [卸载nginx](https://www.jianshu.com/p/c1ce9eec5fb2)


