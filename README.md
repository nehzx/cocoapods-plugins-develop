# cocoapods 插件开发

之所以会写这篇文章是因为过程比较曲折。虽然最后也没有使用cocoapods 插件的方式来实现自己想要的功能。但是还是记录一下，给需要的人。

### 第一步：开发环境配置

官方的文档很简单 **[CocoaPods + Plugins
](https://guides.cocoapods.org/plugins/setting-up-plugins.html)** 我猜测可能是因为觉得比较简单就没怎么写相应的文档吧。但是对新手相当不友好。我写这篇文章的时候也在想是一个新手不需要，大牛不屑看的文章。就当记录一下自己的心路历程吧。

想要好好的开发专注于写代码一个比较好的开发环境是必不可少的。这里直接用ide 比较方便调试。

* 下载IDE

下载[rubymine](https://www.jetbrains.com/ruby/download/) 我自己比较懒是通过[TOOLBOX](https://www.jetbrains.com/toolbox-app/)安装的, 主要是方便管理。特别是使用Windows装了软件以后卸载总感觉没有卸载干净。[激活](https://www.nehza.com/345.html)的话参考另外一篇文章

* 安装ruby 

这里我也不写了安装ruby 的方式我这里也贴一个[🔗链接](https://www.nehza.com/347.html)

* 配置环境

1、 创建工作目录

```bash
mkdir cocoapods-plugins-develop
```

2、 创建插件

```bash
# 进入目录
cd cocoapods-plugins-develop

# 创建插件
pod plugins create  nehza # 最后是插件名称。
```

这里创建完插件以后会自动带上cocoapods 的前缀。

3、 创建一个空项目用来编译调试

> Xcode -> New -> Project -> iOS -> App -> 输入名称点击完成

在项目中创建一给 `podfile` 文件

```bash
pod init
```
在`podfile`文件中添加插件 `plugin 'cocoapods-xxx'` 添加在 `target` 下。


4、 添加一个 `Gemfile` 文件。这里我写成一个脚本直接执行吧。省去创建然后在添加内容的步骤,把下面的脚本整个粘贴到终端中执行。执行路径在创建的项目工作目录中。

```bash
cat > Gemfile <<EOF
source 'https://gems.ruby-china.com' # 替换为国内源
gem 'cocoapods', path: './CocoaPods' # 本地cocoapods
gem 'cocoapods-xxx', path: './cocoapods-xxx' # 本地插件位置
group :debug do
	gem 'ruby-debug-ide'
	gem 'debase'
end
EOF
```
5、这里进行贴图讲解了。

打开`RubyMine` 按照图上点击open 打开创建的工作目录。 打开后看到里面有三个文件夹和一个`Gemfile`文件。
![](https://image.nehza.com/image/open-project.png)
![](https://image.nehza.com/image/project-info.png)

配置ruby 环境。点击偏好设置到图上位置。找到ruby到环境目录。完成以后可以根据IDE提示安装依赖
![](https://image.nehza.com/image/ruby-env.png)

接下来就是配置启动项了。点击编辑跳转到如图。
![](https://image.nehza.com/image/edit-start.png)
![](https://image.nehza.com/image/add-start.png)

根据上图操作来到配置也没。这里有两个需要注意的地方。

* 如果在输入pod 时候没有提示的话那么有可能你的ruby 配置没有成功。 
* 工作目录需要更改为你demo的项目也就是`podfile`文件所在的目录。
![](https://image.nehza.com/image/pod-env.png)
![](https://image.nehza.com/image/start-finish.png)

最后就是这里钩上✅
![](https://image.nehza.com/image/budler.png)

### 第二步：插件开发

cocoapods 这里的开发分为两种模式 

* hooks 已有的命令增加功能
* 新增命令来扩展新功能。

我这里贴下插件目录来说明一下

```bash
├── Gemfile  # 依赖配置类似于 podfile
├── LICENSE.txt
├── README.md
├── Rakefile # 校验文件
├── cocoapods-unp.gemspec # 类似于podspec
├── lib
│   ├── cocoapods-unp
│   │   ├── command
│   │   │   └── unp.rb # 这个就是我们需要开发的命令 run方法就是执行的位置。
│   │   ├── command.rb
│   │   └── gem_version.rb
│   ├── cocoapods-unp.rb
│   ├── cocoapods_plugin.rb # 这里是加载时候自动执行。可以在这里hooks 已有的命令及方法
│   └── provider_hook.rb
└── spec
    ├── command
    │   └── unp_spec.rb
    └── spec_helper.rb
```


### 第三步：插件编译及安装


最后反而是最没有什么技术含量的，这里也就是两条命令

**编译**

```bash
gem build cocoapods-xxx.gemspec
```


**安装**


```bash
gem install cocoapods-xxxx-0.0.1.gem

```