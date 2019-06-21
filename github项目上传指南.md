## 使用SSH密钥连接Github

1. 看看是否存在SSH密钥(keys)
```
如果，不存在此目录，则进行第二步操作，否则，你本机已经存在ssh公钥和私钥，可以略过第二步，直接进入第三步操作。
$ cd ~/.ssh
```
  
2. 创建一对新的SSH密钥(keys)
```
$ ssh-keygen -t rsa -C "your_email@example.com"
这将按照你提供的邮箱地址，创建一对密钥
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/you/.ssh/id_rsa): [Press enter]
接着，根据提示，你需要输入密码和确认密码,其实可以不用密码，就是到输密码的地方，都直接回车
输入完成之后，屏幕会显示:
Your identification has been saved in /c/Users/you/.ssh/id_rsa.
Your public key has been saved in /c/Users/you/.ssh/id_rsa.pub.
The key fingerprint is:
01:0f:f4:3b:ca:85:d6:17:a1:7d:f0:68:9d:f0:a2:db your_email@example.com
```
  
3. 在GitHub账户中添加你的公钥
```
运行如下命令，将公钥的内容复制到系统粘贴板(clipboard)中
$ clip < ~/.ssh/id_rsa.pub

github账户中添加公钥
(1) 登陆GitHub,进入你的Account Settings
(2) 在左边菜单，点击”SSH Keys”
(3) 点击”Add SSH key”按钮
(4) 粘贴你的密钥到key输入框中
(5) 点击”Add Key”按钮
(6) 再弹出窗口，输入你的GitHub密码，点击确认按钮,完成
```

4. 测试
```
输入如下命令，看到警告信息输入yes即可，显示Hi username! You’ve successfully authenticated, but GitHub does not provide shell access. 成功
$ ssh -T git@github.com
```



## 创建与更新项目

1. 去github上创建自己的Repository
2. 第二步创建readme.md $ echo "# Test" >> README.md
3. 第三步：建立git仓库 $ git init
4. 第四步：将项目的所有文件添加到仓库中 $ git add *
5. 第五步：添加readme.md文件 $ git add README.md
6. 第六步：提交到仓库 $ git commit -m "注释语句"
7. 第七步：将本地的仓库关联到GitHub，后面的https改成刚刚自己的地址，上面的红框处
    $ git remote add origin https://github.com/Springzhen/MachingLearning-learning-review.git
8. 第八步：上传github之前pull一下,更新下载的项目的文件
    $ git pull origin master
9. 第九步：上传代码到GitHub远程仓库
    $ git push -u origin master

更新代码
1. 第一步：查看当前的git仓库状态，可以使用git status
    $ git status
2. 第二步：更新全部
    $ git add *
3. 第三步：接着输入git commit -m "更新说明"
    $ git commit -m "更新说明"
4. 第四步：此操作目的是把本地仓库push到github上面，此步骤需要你输入帐号和密码
    $ git push -u origin master

## 删除github中已有文件或者文件夹

- 本地仓库和远程仓库同时删除
```
# 将本地需要删除的文件删除
rm file
# 把本地仓库的文件上传到缓存
git add *
# 把第一步上传到缓存的东西上传到本地仓库，其中'comment'是操作标识，内容随便填
git commit -m 'comment' 
# push 代码到远程仓库
git push origin master

```
完成本地与远程仓库文件删除操作


- 只删除远程仓库，不删除本地仓库
```
# 将远程仓库里面的项目拉下来，并查看文件列表
git pull origin master
dir
# 执行删除指定文件命令,如删除文件file1,file2
git rm -r --cached file1 file2
# 将执行删除的缓存上传到本地仓
git commit -m 'del'
# push 代码到远程仓库
git push -u origin master

```
完成远程仓库文件删除操作
    


## gitlab 分支管理

- 分支保护
Respository -> Branches -> project settings -> 分支保护





## reference：
- [https://blog.csdn.net/weixin_42152081/article/details/80635777](https://blog.csdn.net/weixin_42152081/article/details/80635777)
- [https://blog.csdn.net/qq_32846595/article/details/71149312](https://blog.csdn.net/qq_32846595/article/details/71149312)
- [https://www.cnblogs.com/zlxbky/p/7727895.html](https://www.cnblogs.com/zlxbky/p/7727895.html)
- [http://www.xuanfengge.com/using-ssh-key-link-github-photo-tour.html](http://www.xuanfengge.com/using-ssh-key-link-github-photo-tour.html)





