# Flutter setup on Manjaro
> [Flutter中文网站](https://storage.flutter-io.cn/flutter_infra/releases/stable/linux/flutter_linux_v1.2.1-stable.tar.xz)
#### 1. 下载flutter(当前1.2.1)安装包, [国内镜像](https://storage.flutter-io.cn/flutter_infra/releases/stable/linux/flutter_linux_v1.2.1-stable.tar.xz), 解压到`home`目录即可(装 Flutter 会附带安装 Dart SDK，所以不需要再对 Dart 进行单独安装), 此时,`Flutter`的安装路径是`~/flutter`, `Dart`的安装路径是`～flutter/bin/cache/dart-sdk/`
> 为能在命令行使用`dart和flutter`命令，可编辑环境变量以及设置flutter的国内插件库等(在manjaro上按照官网介绍不能永久生效,而`.bashrc`是每次打开终端必须执行的), 所以我在该文件的最后添加如下（也可在系统环境变量文件`/etc/profile`中添加以对所有用户生效）

```
export PATH=~/flutter/bin:~/flutter/bin/cache/dart-sdk/bin:$PATH
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
export PUB_HOSTED_URL=https://pub.flutter-io.cn
```
#### 2. 开发Android方面的App需要`android sdk`等, 安装一个`andriod studio`算了就全部解决了. 使用`yaourt -S anroid-studio`时报告缺sdk, 手动安装后又有权限问题, 故使用了图形界面的安装方式`pacman manager`
   
   ***安装studio时,遇到磁盘空间不足的问题, 如下搞定***
   >首先去图形界面配置`AUR的biuld Directory`即可

   >```yaourt use /tmp to build package from AUR. and by default /tmp is in RAM and an available spaceof 1/2 of your RAM. you can change where yaourt build package in: /etc/yaourtrc uncomment and change path to this line #TMPDIR="/tmp" the path you give need to be writable by your user.```
#### 3. 启动`android studio`进行配置. 
   1. 显然, 直接访问不行, 设置Socks5代理；
   2. 提示缺少`android sdk`, 敲`next`将自动下载安装在`~/Android/Sdk`下
   3. 在启动界面底部选择`configure > AVD Manager`，然后选择` Create Virtual Device`, 选择Nexus5x, 然后需要下载Image, 在`X86 Images`选项卡中选择 x86 或者 x86_64 镜像, 最后注意: 在Nexus5X的配置界面中, 找到`Emulated Performance`, 设置为`Hardware-GLES2.0`进行硬件加速!
#### 4. 安装`VSCode`, 并安装插件`Flutter`(其将自动安装`Dart`的插件)
#### 5. 在VSCode中调出命令面板(`Ctrl+Shift+p`), 输入`flutter: New Project`, 其后将会说没有`flutter sdk`, 选择flutter的安装目录即可.
#### 6. 运行flutter的app需下载名为`gradle`的东东, 因为前面设置了代理导致下载不成功, 编辑`~/.gradle/gradle.properites`文件, 注释掉代理即可
#### 7. 在VSCode的菜单中选择`debug 或 F5`进行调试运行
#### 8. OK!
