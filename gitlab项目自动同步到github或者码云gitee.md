> 初始条件，先自行建好 gitlab 的代码仓库和码云的代码仓库，ssh 配好。

1.  打开 gitlab 项目，在左侧菜单栏选择`Settings->Repository`

    gitlab 项目

2.  选择 Push to a remote repository，打开之后可以看到相关的配置信息。


    配置

3.  勾选 Remote mirror repository

4.  填写 Git repository URL：这个 URL 地址就是你在码云或者 github 上面创建的项目的 ssh 地址，不过需要在地址里加上你的码云或者 github 的账号密码。  
    比如：  
    我在码云的账号：`userName`  
    密码：`password`  
    项目地址：`https://gitee.com/FutaoSmile/SpringCloud-ServiceRegisterCenter.git`

**那么这个地址应该相应的改成这样：  
`https://userName:password@gitee.com/FutaoSmile/SpringCloud-ServiceRegisterCenter.git`  
密码和地址之间有一个`@`符号。**

5.  测试：提交一次 push 到 gitlab 之后，gitlab 会帮我们自动 push 代码到我们所配置的远程仓库。
