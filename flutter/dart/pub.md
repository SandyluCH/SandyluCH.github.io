## Pub包管理器
配置文件名： pubspec.yaml

Dart应用依赖于包。如果你的Dart应用使用一个或者多个包，那么你的应用本身一定是一个应用包。

### pubspec.yaml文件
该文件包含了包的元数据，如包的名称。最简单的pubspec只有name元数据。
* pubspec.yml必须位于应用的顶层，即根目录。
如果应用引用了其他包， 那么必须在pubspec中设置依赖如：
````
name: hello1
description: A web app that uses AngularDart Components

environment:
  sdk: '>=2.2.0 <3.0.0'

dependencies:
  angular: ^5.2.0
  angular_components: ^0.11.0

dev_dependencies:
  angular_test: ^2.2.0
  build_runner: ^1.1.2
  build_test: ^0.10.3
  build_web_compilers: ^1.0.0
  pedantic: ^1.0.0
  test: ^1.5.1

````

配置好pubspec后， 可以在应用的根目录运行pub get命令来下载安装包。该命令会确定你应用依赖的包，
并将包存入系统缓存中。在Mac和Linux中，缓存目录默认是~/.pub-cache。 在Window中，位于``AppData\Roaming\Pub\Cache ````。 可以通过设置 PUB_CACHE 环境变量对缓存目录进行修改。