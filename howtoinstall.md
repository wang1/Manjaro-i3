# 基于Manjaro的Lammps多机并行运算环境搭建与配置
https://zhuanlan.zhihu.com/p/34555369 谈到了cmake及其它方法，可以参考
## 前提

多台Manjaro系统的多核计算机（目前一台56核，另一台72核），OpenSSH必需

## 机器A安装、配置、运行

首先需要安装并行环境的Lammps，步骤如下：

```bash
# 1. 在用户目录git得到PKGBUILD文件并自动生成lammps目录
git clone https://aur.archlinux.org/lammps.git
# 2. 进入lammps目录，可看到PKGBUILD文件
cd lammps
# 3. 构建package（将先下载Lammps安装包lammps-3Mar2020.tar.gz，大约120M，速度很慢，用了proxychain）
# 所以可以将预先得到的PKGBUILD和lammps-3Mar2020.tar.gz(下载地址https://lammps.sandia.gov/tars/)文件直接复制到lammps目录中
makepkg -s
# 4. 安装lammps。本步骤完成后可以输入 mpirun 运行，测试是否成功
makepkg -i  
# 5. 切换到lammps源文件目录
cd src/lammps-stable_3Mar2020/src
# 6. 生成可执行并行主程序lmp_mpi，以16核编译，具体的编译核数以机器为准，越多越快
# 完成后将生成可执行文件lmp_mpi，可ls查看一下
make -j16 mpi
# 7. 将可执行程序lmp_mpi复制到/usr/bin/可保证一直可用
sudo cp lmp_mpi /usr/bin/
# 8. 配置运行核数。假设我们要运行的程序在~/myAtoms目录，那么在该目录新建文件hostfile，内容如下
localhost slots=48
# 9. 测试是否可以运行程序，在~/myAtoms目录以48核运行，输出和log也在同一目录
mpirun --hostfile hostfile -np 48 lmp_mpi -i input.file
# 10. 编译需要的包
# （在src中编译了所有的包：make yes-all，然后make mpi（每次安装或反安装包后都需重新编译mpi）编译报错，因为有些包还需要其它文件，因此需要把这些包去掉make no-all。然后先编译标准包试试make yes-standard）
# 运行丁勇杰所给的程序报缺某些包。查看包名如：molecule，那么再次进入src/lammps-stable_3Mar2020/src目录编译安装该包，同时重新生成lmp_mpi即可
make yes-molecule
make mpi  # 一定切记重新生成！！！！！！！！
sudo cp lmp_mpi /usr/bin/   # 不要忘记复制
# 11. 再次到运行，有输出即OK，可停止
```

完成以上步骤，则机器A的lammps配置OK。
其它机器以此重复

## 多机并行

注意亮点，`SSH`互通，`hostfile`配置为多机即可。以下以两台计算机AB为例，假设A是主控机，ip为`10.1.1.1`，B为协作机，ip为`10.1.1.2`：

### SSH互通

1. 在计算机A上生成密钥：`ssh-keygen`，默认将免密在`~/.ssh/`下生成公钥文件`id_rsa.pub`
2. 拷贝公钥到计算机B：`ssh-copy-id ~/.ssh/id_rsa.pub admin@10.1.1.2`
3. ssh验证是否能免密访问计算机B
4. 在计算机B上重复上面的过程，保证免密互通

### 运行配置文件hostfile

前面我们生成的hostfile是针对单机，下面给出多机运行的配置。
在主控机计算机A的`~/myAtoms`目录生成`hostfile`文件，内容如下：

```bash
10.1.1.1 slots=48 max-slots=48
10.1.1.2 slots=48 max-slots=48
```

各运行机的ip地址及核数。
核数根据具体情况调整即可。但似乎协作机的核数不能超过主控机！！！！！！！
以上配置表明两机都以48核共96核运行。

（不需要！！！）将计算机A的运行程序目录如：`～/myAtom`上传到其它各机同样的目录
然后在主控机上运行：
`mpirun --hostfile hostfile -np 96 lmp_mpi -i input.file`

多机并行对网络连接要求非常高。目前在测试中...

### OK


生成hostfile，否则将报资源不够错误。是否有效待验证
According to https://www.open-mpi.org/faq/?category=running#oversubscribing you could oversubscribe your node using a hostfile. Before proceeding, be careful that this way you can severely degrade the performance of the node. Also, if the system you use to run the application is using a queue system, this may not be valid.

First create a hostfile (named hostfile) containing

localhost slots=48

head slots=3 max-slots=3
node1 slots=4 max-slots=4
node2 slots=4 max-slots=4
node3 slots=4 max-slots=4

The simply run your application like

mpirun --hostfile hostfile -np 25 python -c "print 'hey'"

运行丁所给的程序报缺某些包。在src中编译了所有的包：make yes-all，然后make mpi（每次安装或反安装包后都需重新编译mpi）编译报错，因为有些包还需要其它文件，因此需要把这些包去掉make no-all。然后先编译标准包试试make yes-standard
或者哪个包缺编译哪个包，现在OK。
=======================================

To install, you will need to have the git package installed. You may use any of the above names in-place of lammps.

$ git clone https://aur.archlinux.org/lammps.git  # 得到PKGBUILD文件
$ cd lammps
$ makepkg -s	# 将下载Lammps安装包，速度很慢，用了proxychain
$ makepkg -i	# 安装

To update, you may repeat the above, or change into the cloned directory, and execute the following, after which, if there are any changes, you may use makepkg as above.
