# Git和Github的练习使用
## 1 创建管理自己的库
+ 初始化库
  鼠标右击，选择Git Bash Here，输入如下代码：
  ```
  #初始化库
  git init

  #查看文件状态
  git status
  ```
  结果如下：
  ![图片加载失败](https://s1.ax1x.com/2020/08/12/axtGff.png)
+ 创建文件
   将第一次作业的md文件拷贝至该库，在Git中输入如下代码：
  ```
  #查看文件状态
  git status
  ```
  结果如下：
  ![图片加载失败](https://s1.ax1x.com/2020/08/12/axNwvD.png)
+ 暂存新增的文件
  输入代码：
  ```
  #添加文件到暂存区
  git add work.md

  #查看文件状态
  git status
  ```
   结果如下：
  ![图片加载失败](https://s1.ax1x.com/2020/08/12/axULTA.png)
+ 提交更新
    输入代码：
  ```
  #文件提交
  git commit -m "first try"
  ```
+ 上传库至Github
  在Github上建立一个空白库，输入代码：
  ```
  git remote add origin https://github.com/15616561120/-.git
  git push -u origin master
  ```
  结果如下：
  ![图片加载失败](https://s1.ax1x.com/2020/08/12/axs8bT.png)
此外，我还注意到库里面必须含有代码类文件才能上传成功。
最后，附上自己库的地址：https://github.com/15616561120/-

## 2 管理操作团队的库
+ 分支冲突

