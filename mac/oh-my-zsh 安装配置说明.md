# oh-my-zsh 安装配置说明

- 安装oh-my-zsh

``` shell
# via curl
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
# via wget
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"


# 想要卸载oh-my-zsh 输入以下命令
uninstall_oh_my_zsh
```

- 更改zsh主题

```shell
vim ~/.zshrc
# 修改配置文件中的 " ZSH_THEME ",例如设置为随机主题
ZSH_THEME  = "random"   

# 配置文件 ~/.zshrc  模板的路径
~/.oh-my-zsh/templates/zshrc.zsh-template  
```

- 安装插件

  - 语法高亮

  - ```shell
    # 执行下面指令自动安装
    git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
    ```

  - 自动补全

  - ```shell
    # 执行下面指令自动安装
    git clone https://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
    ```

  - 解压

  - ```shell
    #oh-my-zsh 本身提供 extract
    #使用方式 x + 要解压的文件 
    ```

- 启用插件

- 在 ~/.zshrc 中找到 

- ```shell
  plugins=(
  # git命令
    git,
  #语法高亮
  zsh-syntax-highlighting,
  #一键解压x
  extract,
  # 自动补全
  zsh-autosuggestions,
  #跳转 这个需要安装
  #z
  #vim 增强
  #tvi-mode
  )
  ```

  



