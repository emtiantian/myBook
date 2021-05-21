## git 换行符问题

### 起因

在跨平台项目中，经常会出现 windows 和 linux，Macos,unix 等平台，  
在当前 windows 平台中的文件换行符为（0x0d0a）crlf,  
其他平台都是（0x0a）lf，  
在 git 项目中如果不注意就会出现 windows 拉取后不修改提交，  
仍然显示有改动的问题。

### 解决办法

git 中给出了一个 自动切换的方式（code.autoclrf=true）  
但是有 bug 不能使用(在有中文的文件中无法自动转换)  
pass（这里的 pass 就是不通过的意思）  
到底咋用 我也不知道 [pass 到底啥意思](https://www.zhihu.com/question/27302256)

最终解决办法是
git不对代码做任何改动
每次提交的时候拒绝提交包含 crlf 的代码
git config --global core.autocrlf false
git config --global core.safecrlf true
完美解决

### 参考

[git 上的办法](https://github.com/vigente/gerardus/wiki/Integrate-git-diffs-with-word-docx-files)
[详细的说明和解决办法](https://github.com/cssmagic/blog/issues/22)
[详细的说明和解决办法](https://www.cnblogs.com/flying_bat/archive/2013/09/16/3324769.html)
