# 使用NVM 管理不同版本的 node 与 npm

####一.卸载已安装到全局的 node/npm

如果之前是在官网下载的 node 安装包，运行后会自动安装在全局目录，其中

node 命令在 `/usr/local/bin/node` ，npm 命令在全局 `node_modules` 目录中，具体路径为 `/usr/local/lib/node_modules/npm`

安装 nvm 之后最好先删除下已安装的 node 和全局 node 模块：

```shell
npm ls -g --depth=0 #查看已经安装在全局的模块，以便删除这些全局模块后再按照不同的 node 版本重新进行全局安装
sudo rm -rf /usr/local/lib/node_modules #删除全局 node_modules 目录
sudo rm /usr/local/bin/node #删除 node
cd  /usr/local/bin && ls -l | grep "../lib/node_modules/" | awk '{print $9}'| xargs rm #删除全局 node 模块注册的软链
```
####二、安装 nvm


```shell
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.29.0/install.sh | bash
```
完成后nvm就被安装在了 `~/.nvm` 下啦，接下来就需要配一下环境变量了，如果你也使用了 zsh 的话，就需要在 `~/.zshrc` 这个配置文件中配置，否则就找找看 `~/.bash_profile` 或者 `~/.profile` 吧。

```
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" # This loads nvm
```

这一步的作用是每次新打开一个bash，nvm都会被自动添加到环境变量中了。


```
完成后输入 source ~/.zshrc 重新启动一下配置。
```
#### 三、安装切换各版本 node/npm
首先我们可以使用 nvm ls-remote 看一下node有哪些版本可以安装：

```
 ~  nvm ls-remote
            v0.1.14
            v0.1.15
            v0.1.16
            ...
```

**使用nvm轻松切换node版本**

nvm的工作原理：
按照我上述安装方法的话，nvm会将各个版本的node安装在 `~/.nvm/versions/node` 目录下，我们可以打开这个目录看看有些什么东西：


```
➜  ~  ls -a ~/.nvm/versions/node
.      ..     v4.1.0 v5.5.0
```
事实上 v4.1.0 和 v5.5.0 这两个目录分别存放node的binary档，nvm会在 `$PATH` 前面安插指定版本的目录，透过这种方式在使用node命令时就会用指定版本的node来运行了。

可以确认实际的 `$PATH` 看看：


```
➜  ~  echo $PATH
/Users/***/.nvm/versions/node/v7.7.3/bin:...
```
由于刚刚我们通过nvm安装node，会自动把最后安装的版本设为当前使用的版本，因此上述路径结尾会是 `.../v7.7.3/bin` （还可通过 nvm ls 命令查看当前已安装的所有node版本）。

接下来我们可以使用 nvm use <version> 切换版本：

```
➜  ~  nvm use v4.1.0
Now using node v4.1.0 (npm v2.14.3)
➜  ~  node -v
v4.1.0
➜  ~  nvm use v7.7.3
Now using node v7.7.3 (npm v4.1.2)
➜  ~  node -v
v7.7.3
```
轻轻松松完成切换！

不过问题来了，这时如果你新打开一个bash，输入 `nvm current `会发现显示为 null ，这是因为使用 `nvm use` 命令只会在当前bash里生效，重新打开一个bash你会发现 $PATH 的值已经不包含刚才的node目录了，要解决这个问题也很简单，使用 `nvm alias default <version>` 命令来指定一个默认的node版本就ok了，这里我使用了 `v4.1.0` 作为默认版本：


```
➜  ~  nvm alias default v4.1.0
default -> v4.1.0
```

此时再打开一个bash输入 nvm current 就会显示为 v4.1.0 了。


#### 四、使用 .nvmrc 文件配置项目所使用的 node 版本

如果你的默认 node 版本（通过` nvm alias` 命令设置的）与项目所需的版本不同，则可在项目根目录或其任意父级目录中创建 `.nvmrc` 文件，在文件中指定使用的 node 版本号，例如：

```
cd <项目根目录>  #进入项目根目录
echo 4 > .nvmrc #添加 .nvmrc 文件
nvm use #无需指定版本号，会自动使用 .nvmrc 文件中配置的版本
node -v #查看 node 是否切换为对应版本
```
#### 五, nrm 快速切换 NPM 源
 **安装**
 
```
npm install -g nrm
```

**源列表**

```
nrm ls    
```

**使用淘宝源**

```
nrm use taobao    
```
**测速**

```
nrm test 
```

本文参考:
  http://www.cnblogs.com/kaiye/p/4937191.html
  https://segmentfault.com/a/1190000004404505?utm_source=tuicool&utm_medium=referral

