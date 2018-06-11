## git换行符问题
### 起因
在跨平台项目中，经常会出现windows和linux，Macos,unix 等平台，                
在当前windows平台中的文件换行符为（0x0d0a）crlf,   
其他平台都是（0x0a）lf，   
在git项目中如果不注意就会出现windows拉取后不修改提交，   
仍然显示有改动的问题。
### 解决办法
git中给出了一个 自动切换的方式（code.autoclrf=true）    
但是有bug不能使用(在有中文的文件中无法自动转换)    
pass（这里的pass 就是不通过的意思）    
到底咋用 我也不知道  [pass到底啥意思](https://www.zhihu.com/question/27302256)  

最终解决办法是
编译器不对代码做任何改动
每次提交的时候拒绝提交包含crlf的代码
git config --global core.autocrlf false
git config --global core.safecrlf true
完美解决

### 参考
[git上的办法](https://github.com/vigente/gerardus/wiki/Integrate-git-diffs-with-word-docx-files)
[详细的说明和解决办法](https://github.com/cssmagic/blog/issues/22)
[详细的说明和解决办法](https://www.cnblogs.com/flying_bat/archive/2013/09/16/3324769.html)