# 项目启动方式



## 源码获取

1. clone源码

2. yarn install 安装全部依赖（建议用yarn，因为我有yarn.lock）



## 本地调试

有以下三种方式：



### 直接运行

```bash
node index.js init test
```



### 使用准备好的scripts命令

```bash
yarn dev init test
```



### 在本地link成可执行文件



```bash
npm link


uview-cli init test
```



> npm link命令需要管理员权限，在mac和linux上使用sudo运行。



# 项目依赖

1. chalk 用于修改控制台中字符串的样式

2. commandar 用来解析命令行的参数

3. compressing 用来压缩和解压文件

4. download-git-repo 用来从git仓库中clone源码

5. handlebars 模板引擎，暂时没用到

6. inquirer 用来和用户交互，引导用户一步一步走下去，暂时没用到

7. log-symbols 用来往终端中输出一些符合，比如success，error之类的

8. ora 提供交互上下文

9. replace-in-file 另一个模板引擎，暂时没用到



# 工作方式



## 命令行参数

在index.js中指定了都会接受哪些命令

```js
v1.command('init <name>').action(async name => {
  await inquirer.prompt([])
  await InitProject(name)
})
```

以上命令指定了会接受init命令，并且需要一个必填的参数name，之后在action中可以指定处理逻辑。inquirer.prompt([]) 就是用来询问用户的一些细节，这里留空表示不需要询问用户任何东西。



注意这个文件有两个注意的地方，一个是头顶的

```js
#!/usr/bin/env node
```

这是**必须的**，因为最后我们的代码是要链接成可执行的脚本文件，必须告诉终端用什么解释器执行这个脚本。它只需要在这个入口文件中写但建议每个文件都加上。



还有一个是最后的

```js
program.parse(process.argv)
```



这也是必须的，用来告诉进程可以接受命令行参数了，去掉这句话就没效果，上面的命令行白指定。参数指的是输入流，命令行属于输入流的一种（在计算机中一切皆可抽象为文件流的概念）。



## 具体处理逻辑

具体处理逻辑写在cmd文件夹中，此文件注释清晰可自行阅读，这里说一下大致流程。



### 初始化项目

原本是计划从git仓库拉取源码的，但这个过程相当不可靠，有的用户就死活连不上网络。所以采用我们预先准备文件然后直接解压的方式。嵌入压缩文件可以简单的调用reset命令完成，注意不需要接受任何参数，只需要目录名叫project即可，



工具会自动把文件夹转换为.zip格式，然后把.zip转成BASE64字符串存储起来，在用户调用init命令的时候把BASE64还原成.zip，再解压，整个过程不超过1秒，



### 重置模板项目

因为读取路径就叫project，输出文件也就叫project.zip，所以这部分的逻辑非常简单。



# 工具函数

一些常用的工具函数封装在utils文件夹下。



# 项目约定

为了简化开发流程，做了一个简单的约定来跳过繁琐的配置化流程：



1. 如果你要设置模板项目（调用reset命令），文件目录必须叫project。里面内容可以随意自定义不做任何限制，只对文件夹的名字做限制且必须放在根路径下（和index.js）同级。因此reset命令**不需要接受任何参数**

2. 没有2了。



# 工具的使用

1. 初始化项目：uview-cli init <项目名字>

2. 项目模板路径：uview-cli reset

