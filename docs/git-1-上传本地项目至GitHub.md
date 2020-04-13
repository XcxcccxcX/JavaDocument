# 本地项目上传至Github

> 作者： Xcx  
> 时间： 2020/4/13 10：37  
-------------------
&nbsp;

1. 首先在GitHub上创建一个**repository**  
     ![image.png](https://i.loli.net/2020/04/13/G5XHJqPdYCWsgT1.png)  
&nbsp;

2. 获取该新建的repository的地址 复制备用  [我的仓库地址](git@github.com:XcxcccxcX/JavaDocument.git)   `git@github.com:XcxcccxcX/JavaDocument.git`  
    ![image.png](https://i.loli.net/2020/04/13/cEhSFWYLnsgoI2P.png)  
&nbsp;

3. 打开本地项目所在位置 首先安装git （这里暂时只是针对git）  
    右键该项目文件夹 整个文件夹  右键会出现 `git bash here` 点击  
    ![image.png](https://i.loli.net/2020/04/13/VMzUqkLX56N8pE4.png)  
&nbsp;

4. 克隆下刚刚创建的项目  git clone + 刚刚复制的链接
    `git clone git@github.com:XcxcccxcX/JavaDocument.git`  
    ![image.png](https://i.loli.net/2020/04/13/ckrfH8PMnDyxERO.png)  
&nbsp;

5. 可能需要GitHub账号密码 这里跳过  然后本地会出现该项目clone下来的一个文件夹  里面有一个 .git 文件 然后将本地的文件全部复制到该文件夹中  
    这个时候就需要将本地的这些文件 按照下面的操作顺序 提交到GitHub上就算任务完成了  
    `git add .` :     git add . 会把本地所有untrack的文件都加入暂存区 并且会根据.gitignore做过滤  
    `git commit -m "该次提交的注释"` :   将暂存区里的改动给提交到本地的版本库  
    `git push` :     将本地库中的推送至远程库  
&nbsp;
