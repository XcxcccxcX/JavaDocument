# maven  
  
> 作者：xcx  
> 时间：2020/5/11  15：00  
  
-----------------  

### maven 无法clean clean 报错
*Failed to execute goal org.apache.maven.plugins:maven-clean-plugin:2.5:clean (default-clean) on project aaa: Failed to clean project: Failed to delete  \\ \target*

我的原因是 在idea的Terminal中 进入了target的目录 所以无法clean  因为使用git 使用的是 Terminal  然后我去骚操作进入了target 导致出现了这个问题