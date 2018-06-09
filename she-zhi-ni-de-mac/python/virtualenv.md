# Virtualenv

`virtualenv`是一个用来隔绝各种python环境的工具。对于一个`python`工程会有很多依赖包，当你不想全局安装这些依赖包的时候，`virtualenv`的威力就来了。他会为每个工程创建单独的环境，这些环境互不影响，也不会污染全局。你甚至可以选择用哪个版本的`python`。**强烈建议**在开发`python`应用时，使用`virtualenv`。

## 安装

安装`virtualenv`非常简单：

```bash
pip install virtualenv
```

## 使用

假设你有一个项目`my-project`，你可以为它生成一个虚拟环境：

```
$ cd my-project/
$ virtualenv venv
```

如果你想在虚拟环境中继承全局安装的包：

```
$ virtualenv venv --system-site-packages
```

运行完这个命令，会在工程目录下生成venv/文件夹，初始化的环境里有python编译器和pip等。你需要在使用环境前先激活它（每个终端实例中都需要运行这个命令）

```
$ source venv/bin/activate
```

激活后，你会看到终端的标识符前多了一个`(venv)`如果想在环境里装依赖，你可以这么做：

```
$ pip install <package-name>
```

依赖会安装在venv文件夹中，不会污染全局。离开这个虚拟环境命令：

```
$ deactivate
```

**重要**：记住在.gitignore中添加venv

最佳实践是，只把几个很大的包（如Numpy）和你常用的包装成全局的，其他的都用virtualenv安装。

## Virtualenvwrapper

这是一个virtualenv的拓展，可以让同时开发多个项目多个环境更容易，具体内容可参考[这个文档](https://virtualenvwrapper.readthedocs.io/en/latest/install.html)。



