我们平时的工作和学习中，如果要安装一个工具，比如我们打包用的nsh-git-push-tag，有时候安装完了之后，我们在命令行输入nsh-git-push-tag, 有时候会报commond not found这个错误，甚至我们在安装webpack，npm，git等命令的时候都会出现这样的错误，那么这个问题怎么解决呢？

拿nsh-git-push-tag举例

- 第一步，在命令行输入
```
which nsh-git-push-tag
```
把bin之前的路径记录下来
/Users/liyang/.npm-global/bin
- 第二步
```
vim ~/.bash-profile
```

在 export PATH这句后面用冒号拼上刚才的地址
```
export TAG=/Users/liyang/.npm-global/bin
export PATH=${PATH}:/usr/local/mongodb/bin:$TSC:$TAG
```
保存然后退出  （ESC --> :wq）
- 第三步 通过source命令，使其生效
```
source ~/.bash_profile
```

然后查看一下是否成功
```
echo $PATH
```
如果有刚才地址，说明添加成功了

如果你的命令行工具用的是iterm2，并且安装了 zsh 和 oh-my-zsh，等到第二天你会发现你昨天设置好的环境配置不见了
这个时候修改 zsh 的配置文件,保证每次都执行~/.bash_profile
### 解决方案
- 1.进入zsh配置文件
```
vim ~/.zshrc
```

- 2.在末尾添加

```
source ~/.bash_profile
```

- 3.保存退出

- 4.使配置生效
```
source ~/.zshrc
```
这样以后都能用了
