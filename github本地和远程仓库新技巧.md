# github 本地仓库和远程仓库技巧（vscode）

1. 本地新建一个项目文件

2. 初始化 git

3. 发布到云上

4. 选择私有还是公有

5. 下一步测试下框架 CLI 生成后如何由本地添加到 GitHub 上

6. 通过 rm -rf .git 命令删除 git 文件后就可以使用上述步骤

7. 但是我发现一个问题，就是使用代理后无法使用 vscode 推送远程库，只有关上后才可以

8. 只有两个方法同时存在使用，一个是在 GitHub 上先建立一个库但是不添加文件，在本地添加 git 远程库，好处就是可以两个文件名不一致，另一个就是用本地建立，然后推送到远程，问题是得提前删除原项目的 git 信息初始化后，关上代理后再推送

9. 两个看着用吧，很是无语这个无奈的有别扭的选择，我们可是还有很长的路要走，很多东西要学
