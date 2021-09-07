# Manjaro -i3的配置记录和相关文件
### 配置文件及其位置
1. i3's configure file--> ~/.i3/config, 
2. i3status's configure file --> /etc/i3status.conf,
3. URxvt‘s configure file --> ~/.Xresources
3. morc_menu的配置文件 --> ~/.config/morc_menu/morc_menu_v1.conf，可设置宽度等
4. 輸入法等配置文件--> ~/.profile
5. 引導菜單grub的配置文件 --> /etc/default/grub
6. 桌面组件conky，默认是i3配置文件中启动/usr/bin/start_conky_maia脚本，该脚本加载了/usr/share/conky目录下的两个配置文件，分别是右上角的conky_maia和conky1.10_shrotcuts_maia
7. $mod+9将lock screen，这是通过执行/usr/bin/blurlock脚本实现的，查看该脚本可见：先屏幕快照->模糊化->删除快照->执行i3lock，可以调整模糊的程度
----
### 安装过程
安装前，进入本本的win10，lenovo将自动对bios进行升级。使用dd/rufus制作启动盘并进行以下bios设置：
0. [arch linux 针对X1C6th的详细说明]（https://wiki.archlinux.org/index.php/Lenovo_ThinkPad_X1_Carbon_(Gen_6)）
1. 在X1C6th（i7cpu8650/16G/512G）安装manjaro-i3 17.1.11, 因该本本预安装win10，需在bios中设置`Security -> Secure Boot - Set to "Disabled"`才能从U盘启动
2. X1C 的默认 BIOS 配置下 Thunderbolt BIOS Assist Mode 是 Disable 的，这会导致 Linux 在 s2idle 下的能耗特别高（温度平均46，平均功率约8.5w）。故需要进 BIOS 将其设置为 Enable，如此则温度降至40以下，功率也骤降至4-6w左右，办公续航轻松上11h
3. 另外将memory card reader、fingerprint、camera, bluetooth, wireless wan等禁用以节约功耗
4. enable S3 support, make sure you have at least BIOS version 1.30 installed. Then, go into the BIOS configuration, and `Config -> Power -> Sleep State - Set to "Linux"`。To check whether S3 is recognized and usable by Linux, run:`dmesg | grep -i "acpi: (supports"`
-----
安装后，因X1C分辨率为2560×1440，字体太小，故进行整体界面放大：
1. 打开~/.Xresources，修改`Xft.dpi`为140，值越大，字体越大，重新登录生效。bar的字体在`～/.i3/config`中可调节，右上角的字体在`/usr/share/conky/conky_maia`，右下角的字体在`usr/share/conky/conky1.10_shortcuts_maia`文件中调整
1. 设置更新。联网后，使用右下角的更新图标，使用preferences设置镜像位置为china，并刷新列表（自动找到最快的源），启用AUR（Arch User respoisity），然后进行更新
1. URvxt终端字体。（antialias为平滑，需要一个中文字体）
```sudo yaourt -S ttf-monaco wqy-microhei (或者install it by GUI)```
编辑~/.Xresource文件，对应位置修改如下
```
URxvt.font:xft:Monaco:pixelsize=16:antialias=true,xft:WenQuanYi Micro Hei Mono:pixelsize=16:antialias=true
URxvt.boldFont:xft:Monaco:pixelsize=16:Bold:antialias=true,xft:WenQuanYi Micro Hei Mono:pixelsize=16:Bold:antialias=true
```
执行xrdb -load ~/.Xresources生效(maybe need reboot)

4. 安装中文输入法
```sudo pacman -S fcitx
sudo pacman -S fcitx-im     ----全部安装，保证图形界面可用
sudo pacman -S fcitx-configtool   ----配置工具
sudo pacman -S googlepinyin    ----但不能进行模糊音设置
```
然后打开`～/.xprofile`文件，添加一下内容：
```export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
fcitx -d -r
```
注销用户重新登录，点击右下角图标进行配置（设置shift切换和< >翻页等）

5. GUI install proxychains-ng, then edit /etc/proxychains.conf file, replace "sock4..." at the end
5. `sudo pacman -S remmina freerdp`，远程登录工具remmina，支持各种协议（需另外安装freerdp），logout/reboot生效
5. google-chrome/firefox/nitrogen壁纸/shadowsocks/stardict/wps-office/ttf-wps-fonts/visual-studio-code-bin
6. morc_menu中动Arandr可进行双显示器设置，拖动即可
7. This did not work out of the box, but was easily fixed by doing the following (Assuming you are using GRUB):
Add `acpi.ec_no_wakeup=1` To your kernel parameters (/etc/default/grub -> GRUB_CMDLINE_LINUX_DEFAULT)

Then edit `/etc/systemd/logind.conf` and make sure you have an uncommented line like this: `HandleLidSwitch=suspend`

Now when you close the lid, the machine will sleep. When you open it, you'll just have to press the button to get it to come back. This might be annoying for some users, but that's how I'm used to waking up my machine.

10. 设置网络流量显示。新建如下内容文件`～/.i3/net-speed.sh`, 添加可执行权限`chmod +x ~/.i3/net-speed.sh`:
```
#!/bin/sh

# Authors:
# - Moritz Warning <moritzwarning@web.de> (2016)
# - Zhong Jianxin <azuwis@gmail.com> (2014)
#
# See file LICENSE at the project root directory for license information.
#
# i3status.conf should contain:
# general {
#   output_format = i3bar
# }
#
# i3 config looks like this:
# bar {
#   status_command exec ～/.i3/net-speed.sh
# }
#
# Single interface:
# ifaces="eth0"
#
# Multiple interfaces:
# ifaces="eth0 wlan0"
#

# Auto detect interfaces
ifaces=$(ls /sys/class/net | grep -E '^(eth|wlan|enp|wlp)')

last_time=0
last_rx=0
last_tx=0
rate=""

readable() {
  local bytes=$1
  local kib=$(( bytes >> 10 ))
  if [ $kib -lt 0 ]; then
    echo "? K"
  elif [ $kib -gt 1024 ]; then
    local mib_int=$(( kib >> 10 ))
    local mib_dec=$(( kib % 1024 * 976 / 10000 ))
    if [ "$mib_dec" -lt 10 ]; then
      mib_dec="0${mib_dec}"
    fi
    echo "${mib_int}.${mib_dec} M"
  else
    echo "${kib} K"
  fi
}

update_rate() {
  local time=$(date +%s)
  local rx=0 tx=0 tmp_rx tmp_tx

  for iface in $ifaces; do
    read tmp_rx < "/sys/class/net/${iface}/statistics/rx_bytes"
    read tmp_tx < "/sys/class/net/${iface}/statistics/tx_bytes"
    rx=$(( rx + tmp_rx ))
    tx=$(( tx + tmp_tx ))
  done

  local interval=$(( $time - $last_time ))
  if [ $interval -gt 0 ]; then
    rate="$(readable $(( (rx - last_rx) / interval )))↓ $(readable $(( (tx - last_tx) / interval )))↑"
  else
    rate=""
  fi

  last_time=$time
  last_rx=$rx
  last_tx=$tx
}

i3status | (read line && echo "$line" && read line && echo "$line" && read line && echo "$line" && update_rate && while :
do
  read line
  update_rate
  echo ",[{\"full_text\":\"${rate}\" },${line#,\[}" || exit 1
done)
```
注意：按该文件前面介绍修改`/etc/i3status.conf`和`～/.i3/config`两个文件

11. 安装oracle vbox后如果出现无virtualbox kernel driver，可使用先查看内核版本`uname -r`，然后`sudo pacman -S linux414-virtualbox-host-modules`，如果出现404错误，则refresh database
    但是, Vbox窗口非常难以调节, 我改用了VM. 通过GUI方式安装失败, 手动安装过程如下: 
    ```
     sudo mkdir /etc/init.d #VM需要, 确认有该目录
     sudo pacman -S linux414-headers  #安装header文件, 对应当前的Linux版本, 可用uname查看
     #官网下载VM Pro Workstration后
     chmod +x VMware-Player-VERSION-ID.ARCH.bundle  #添加可执行
     sudo ./VMware-Player-VERSION-ID.ARCH.bundle  #安装
     yaourt -S vmware-systemd-services  #安装服务
     sudo systemctl start vmware  #开始系统服务
     sudo systemctl enable vmware #设置开机启动
     vmware & #终端启动程序
    ```

12. 安装nginx，完成后使用`systemctl start/stop/restart nginx`， `systemctl enable nginx`设置开机启动，配置文件在`/etc/nginx/nginx.conf`。如果出现forbiden，则修改配置文件中`user qige`，qige为网站目录的所有者或nginx的运行者

13. 使用arandr设置双显示器后，重启就失效了。在arandr中将设置保存为`normal.sh`，添加执行权限，然后在`~/.i3/config`中写入`exec ~/.screenlayou/normal.sh`

14. 安装 rofi 代替 dmenu    安装 xfce4-terminal 代替urxvt   设置 conky 主题 关闭 i3status   (暂未执行)
    
15. chrome 支持命令行配置代理，在无法访问chrome 商店下载 SwitchyOmega 前，可以使用 google-chrome-stable --proxy-server="socks5://127.0.0.1:1080" www.google.com 命令启动进行登录谷歌账号(你需要先设置好ss)

16. TIM，先GUI安装deepin-wine-tim(按说明是不需要安装的，但估计AppImage缺少一些wine的库文件)，然后下载其AppImage文件（http://yun.tzmm.com.cn/index.php/s/5hJNzt2VR9aIEF2） AppImage 是一种把应用打包成单一文件的格式，只需要赋予可执行权限即可使用。(开始还能使用，后来只能sudo .TIM-x86_64.AppImage在终端使用了)

17. Tor-Browser，去官网下载压缩文件，解压后直接执行即可（AUR仓库由于GFW限制不能成功）

18. WPS Office安装后，对应的启动命令是（wps/et/wpp)，但由于版权原因，缺少中文字体，下载simsun/simfang/simhei/simkai等中文字体，`sudo mkdir /usr/share/fonts/WindowsFonts`，然后拷贝以上字体文件到该目录，然后`chmod 755 /usr/share/fonts/WindowsFonts/*`，最后重建字体缓存`fc-cache -f`。因为是英文系统故对其它程序没有影响，否则由于在系统中加入了宋体，而部分应用程序又将宋体作为默认字体，显示难看需要进一步调整。

19. 使用系统菜单对鼠标左右调整，但重启后失效，所以使用命令行进行。将以下命令添加到`～/.i3/config`文件的自启动部分：
`xinput list    #查看当前鼠标的id，如id为11,则
 xinput set-button-map 11 3 2 1   #调换，即可生效。改为1 2 3则换回
`

20. 系统默认浏览器设置为chrome，可从`mod+z-->settings-->preferred applications`中选择，如果不成功，也可编辑`.profile`文件，加入`export BROWSER=/usr/bin/google-chrome-stable`，同时编辑`~/.congfig/mimeapps.list`文件，修改为`google-chrome.desktop`

21. Manjaro默认已安装OpenSSH Server, 只需运行`systemctl enable/start/restart sshd.service`即可(开机运行/即可启动/重启)
配置文件`/etc/ssh/sshd_config`，建议取消password登录，只允许密钥，不允许root登录，否则查看日志（`journalctl /usr/bin/sshd | grep Failed`）会发现太多的登录尝试了

22. 安装wireshark后运行不会有interface, 因为wireshark做了权限隔离, 也不推荐使用root运行, 需要将当前用户添加到wireshark组.`gpasswd -a yourusername wireshark`(似乎需要重启才生效?)

23. 在一次较久未升级后进行系统升级,中间在编译某软件(似乎是mongodb)太久了, 我终止了升级过程, 随后无论如何都不能更新, 出现`invalid crypto engine`等错误, 然后查询发现可以如下进行修正:
```
sudo rm -fr /etc/pacman.d/gnupg # 如果以下命令失败则先执行此命令
sudo pacman-key --init
sudo pacman-key --populate archlinux manjaro
sudo pacman-key --refresh-keys
sudo rm /var/cache/pacman/pkg/*
sudo pacman -Syu

```

但以上命令需要libreadline.so.7文件, 执行以下操作即可: `sudo ln -s /usr/lib/libreadline.so.8 /usr/lib/libreadline.so.7`
另, 更新镜像排名, 选择国内比较快的官方镜像源, 勾选相应的镜像站 :
```
sudo pacman-mirrors -i -c China -m rank   #
```
24. GoLang开发配置. GUI安装go即可, 然后打开vscode, 安装go插件；新建一个hello.go文件, 将弹出安装相关工具对话框, 但由于有墙且VScode不支持sock5代理, 故安装不能成功. 进入`~/go`目录, 新建`golang.org/x`目录, 执行`git clone https://github.com/golang/tools.git`和`git clone https://github.com/golang/lint.git`命令, 然后在vscode中调出命令面板, 输入`Go: Install/Update Tools`安装即可

25. Cisco Packet Tracer安装：
```
 A. Download Packet Tracer 7.2.1 from official web site or https://www.sysnettechsolutions.com/en/ciscopackettracer/download-cisco-packet-tracer-7-2/
 B. git clone https://aur.archlinux.org/packettracer.git
 C. 将上面的安装文件复制到pachettracer目录，并进入该目录
 D. 运行命令即可安装：makepkg -sic 
 E. Start packet tracer by executing packettracer or packetraceer.sh file
``` 
26. 截屏：
```
Screenshot of whole screen - <Print>
Screenshot of focused container - mod+<Print>
Screenshot of selection - mod+<Shift>+<Print>
```

```
import i3.jpg //然后就让你选截屏的区域
sleep 5;import 213.png    //等待5秒截屏
import -frame    123.jpg     //截取鼠标点击的那个窗口
import -windows root   123.png  //截取全屏幕
```
后来添加了`deepin-screenshot`截图软件，可简单对图片进行标注等，在`～/i3/config`文件中添加了快捷方式

27. 在安装软件的过程中，如果某种原因未成功，再次运行时会出现等待另一个包管理器退出之类的信息，是因为有锁文件，删除即可（以前我会去进程中kill或重启）`sudo rm /var/lib/pacman/db.lock`

28. 当前使用了socks5代理, 但某些软件只支持http代理如vscode, 所以使用了privoxy进行转换
```
A. 安装privoxy
B. 在/etc/privoxy/config文件最后添加一行(注意最后的.): forward-socks5 127.0.0.1:1080 .
C. 在默认端口8118启动privoxy: systemctl start privoxy
D. 在vscode中配置http代理即可
```
29. 因需对pdf进行批注, 仓库安装FoxitReader报校验错, 因此去Foxit官网下载-->解压-->执行安装即可, 完毕后morc_menu菜单-->Office中即有FoxitReader

30. X1C按了键盘的静音键后, 再次开启则没有声音了. 执行`pavocontrol`进行调整

31. 在看了v2ray的介绍后, 入手了一个v2ray机场(https://mk.nyuyu.net/aff.php?aff=742). 
  A. (请使用B方法)github上找到名为shadowray for v2ray客户端的一个python包, 可实现订阅, 还不错. 步骤如下:
```
sudo pip install shadowray // 安装
sudo vim /usr/lib/python3.7/site-packages/shadowray/subscribe/parse.py  //修改第50行, 将aid和level固定设置为2和0, 否则后面会出错 
shadowray --autoconfig  //会自动下载v2ray核心文件, 并在新建~/.shadowray目录中放置其相关文件. 升级则github下载v2ray-core文件解压到该目录即可
shadowray --subscribe-add 'haha,你的订阅url'  //添加订阅地址
shadowray --subscribe-update --port 1080  //生成服务器配置文件(resource/servers.json), 并指定本地端口(默认为1082)
shadowray --list  //查看当前服务器线路信息
shadowray --start 1 --daemon  //选择1号线路并后台运行
shadowray --stop  //终止后台进程
```
----
 B. github上找到一个可订阅并测速以及自动生成配置文件, 自动启动服务并方便切换的脚本, 果断用上:
 ```
 sudo pacman -S v2ray //安装v2ray, 并自动以服务方式后台启动, 注意如果没有启动成功, 留意v2ray的路径, 一般在/usr/bin下, 否则修改v2ray.service文件
 git clone https://github.com/akirarika/v2sub.git
 sudo cp v2sub/v2sub /usr/bin/    //拷贝到系统路径目录中
 sudo v2sub   //输入一次订阅地址即可, 然后选择线路, 
 ```
 
 因机场传输协议升级为WebSocket, 故修改`v2sub`源代码, 与`outbound->settings`平级输入:
 ```
 "streamSettings": {
   "network": "ws",
   "wsSettings": {
     "connectionReuse": True
   }
 }
 ```
 
 > 20191122, 脚本地址更新为`git clone https://github.com/xbblog95/v2sub.git`，是上一个脚本的更新，自动支持ws， 且支持测速即`sudo v2sub speed`。但请注意因其有其它需要的文件，故需要在其目录中运行或创建链接`sudo ln -s ~/v2sub/v2sub /usr/bin/v2sub`
 > 
 > 20210312, 使用了一个(基于Web的v2ray)[https://github.com/v2rayA/v2rayA]的客户端，支持`socks5/http`等，`yay v2ray`安装，`systemctl enable v2raya`后`localhost:2017`浏览即可(后来发现任没能自动启动，查看日志`journalctl -u v2raya`看到原因是系统时间不对，NTP对时即可)
> 20210801，使用`proxychains`全局代理`yay`时总是网络不可连接等问题，然后发现`v2raya`设置中居然有一个透明代理，即可全局代理Linux的流量，`proxychains`可以退出了！
 > `vultr服务器`https://github.com/Alvin9999/new-pac/wiki/%E8%87%AA%E5%BB%BAv2ray%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%95%99%E7%A8%8B
 
> 每每解决了问题时, 才发现它在不起眼处!!!
> X1C安装了v2ray或shadowray都不能使用, 花了两天时间, 各种考虑, 几乎放弃, 才发现是manjaro的时钟没有同步. 而v2ray要求客户机与服务器的时间不能相差90s, 这就是悲剧之源!
> mod+ctrl+b 调出菜单, 安装timeset, 设置时间同步即可!
------------
以下为独立安装v2ray需要的手动配置, 使用shadowray后可不理会

`sudo pacman -S v2ray`

如果出现缺少文件, `sudo cp ~/.shadowray/v2ray/geo*.* /usr/bin/`, 然后配置`/etc/v2rayconfig.json文件的outbound部分， 添加如下vmess小节`:
```
{
  "protocol":"vmess",
  "settings": {
        "vnext": [
          {
            "address": "8.8.8.8", // 服务器的 IP
            "port": 443, // 服务器的端口
            "users": [
              {
                // id 就是 UUID，相当于用户密码
                "id": "7d4c4078-e129-416b-a483-cf5713a96a66",
                "alterId": 2,
                "security": "chacha20-poly1305"
              }
            ]
          }
        ]
      }
}
```
32. 蓝牙鼠标及无线网卡折腾记

**缘由:** PC机的无线鼠标按键不灵活, JD入手了一个100+的名为MS comfort的鼠标, 到货后发现是蓝牙的, 而PC无蓝牙适配器, 折腾开始

**折腾**

* JD上买个蓝牙适配器, 按照`arch bluetooth wiki`的介绍开始在PC上安装`bluez bluez-utils blueman`等, 总之命令行也罢, GUI也罢, 就是连接不成功, 期间重新启动还卡在`A stoped job is running, wait...`位置, 导致还不能正常重启!

  使用的命令如下:
  ```
  bluetoothctl  # 进入蓝牙配置
  power on      # 打开适配器
  scan on       # 进入扫描模式, 可扫描到新的蓝牙设备, 拷贝其mac
  trust mac
  pair mac
  connect mac
  ```
> 放弃, MS的鼠标不给Linux使用? :), 该蓝牙鼠标送与XXX, 在其WIN10上瞬间工作, :(

* 事情本来就此了. 某天清理机器, 觉得有关蓝牙的程序不需要了, 故搜索`bluetooth`, 将与之相关的删除. 期间`Manjaro`给了以下警告, 无非是可能有些程序需要(wireshark之类), 直接无视.

* 麻烦开始. PC上的USB无线网卡马上显示`Down`状态. **不能上网了** (删除期间真没涉及wireless方面的东东啊!)

* 重启1..., 2..., 3..., 仍然不能工作, 第一反应:无线网卡坏了(已用了9年)

* 拔出网卡, 插到X1C, 正常工作, 那就开始进行手动配置吧. **此时此处没有X1C是不可想象的! 我需要参考资料!**

* 以下的命令多费周章:
  ```
  ip addr 或 iw dev     # 查看网络设备接口名称, 以下称为wlp0s
  ip link show wlp0s    # 查看其状态是否up
  sudo ip link set wlp0s up  # up该接口
  iw wlp0s link         # 是否有连接
  sudo iw wlp0s scan    # 扫描AP, 找出要连接的SSID, 以下称为hahaha(WPA2加密)
  su                    # 切换为root
  wpa_passphrase hahaha >> /etc/wpa_supplicant/wpa.conf # 生成wpa连接配置文件, 该命令需输入wifi口令
  su XXXX   # 切换回一般用户
  sudo wpa_supplicant -i wlp0s -c /etc/wpa_supplicant/wpa.conf  # 连接指定AP
  iw wlp0s link         # 应看到连接成功
  sudo dhclient wlp0s   # 获取网络配置
  ```
  > sudo ip addr add 192.168.1.2/24 dev wls0s         # 指定IP
  > ip route add default via 192.168.1.1 dev wls0s    # 添加网关
  > /etc/resolv.conf为DNS配置

* 现在可以联网, 马上安装`networkmanager, nm-connection-editor, network-manager-applet`, 重启

**恢复如初**

33. 下载`kali`虚拟机时， chrome下载总是半途中断。安装了另外一个多线程下载工具`Axel`， 10分钟搞定。

```bash
yay -S axel
axel YOUR-URL -n 10 # 以10个线程下载文件在当前目录
```
34. 在Nova的提醒下，将vscode与github用SSH通过密钥关联起来。
```bash
# 生成SSH密钥
ssh-keygen    # 默认在～/.ssh下生成密钥对（id_ras,id_ras.pub)，可以无需密码保护
# 添加SSH Key到Github：登入github，修改帐号的setting中的ssk key，添加id_ras.pub公钥的内容即可
# 多个环境如学校/家/公司，添加多个公钥即可
#SSH连接测试：为了保险起见，我们应当测试一下是否能够通过SSH访问Github
ssh -T git@github.com # 看到如下信息即可：You've successfully authenticated, but GitHub does not provide shell access.
# 进入某仓库，获取ssh地址如：git@github.com:wang1/course.git
# 完善git配置信息
git config --global user.name "wang1"           #引号内替换成自己Github注册用户名
git config --global user.email "wang1@gmail.com"
# 关联本地和远程仓库
git remote add origin git@github.com:wang1/course.git
# 如果本地仓库已关联，可修改
git remote set-url origin git@github.com:wang1/course.git
# 查看远程关联情况
git remote -v
```
至此，在vscode中同步仓库就无需每次输入用户和密码了

35. 系统已内置了`webp`图片转换工具，生成以下脚本自动将某目录下的`.jpg`和`.png`进行转换，文件名不变

```bash
# input/output in the same directory
for file in ./*
do
  if [ -f "$file" ] # only for file
  then
    cwebp "$file" -o "${file%.*}.webp" # default quality is 75
  fi
done
```

36. ~~Synergy，一套键鼠控制两台主机的开源软件[计算序列号](http://cpp.sh/3mjw3)~~

疫情期间进了个带鱼屏，连接到在办公室用的一台老PC后分辨率上不去（买了各种转换头）。
怎么办？换主机？
然后做了决定，X1C连接带鱼屏运行Manjaro，老PC连接一个显示器运行Win10（这样也不需要Win10的虚拟机了）。
但这样最大的问题是：桌面的键盘鼠标有两套且还不能在两个屏幕间滑动！
解决：Synergy，在局域网中控制多台主机且跨平台的利器
键盘鼠标需连接在Manjaro即X1C上，毕竟这是主要使用环境（Lan下，共享的键鼠有一定延迟）
- 服务器端安装及配置：
```bash
yay synergy   # 安装
synergy       # 运行，选择服务器端，图形方式选择屏幕排列位置以及客户端名称如Win10（需要与客户机一致），保存配置文件为~/.synergy/lib.conf
exec --no-strartup-id sysnergys -c ~/.synergy/lib.conf  # 写到 ~/.i3/config中自启动
```
- 客户机安装及配置：
下载Windows版的 [Synergy](https://share.weiyun.com/5EacE47)，安装即可
运行synergy，选择客户机，设置服务器IP和客户端名称（需与服务器端一致），点击开始测试是否OK

- 自动运行的解决
如此两台主机可由一套键鼠进行控制，效果还不错。但如何彻底在客户机摆脱键鼠？比如Win10需要登录，比如synergy需要用鼠标点击运行？
首先，设置Win10自动登录：
`win+R`出现运行窗口后输入`control userpassword2`命令，调出用户帐号界面，将“要使用本计算机，用户必须输入用户名和密码”前面的勾√取消掉，确认后需两次输入口令即可实现自动登录
其次，让synergy自动运行：
开始打算新建服务来实现，各种原因没成功，使用的计划任务。搜索`任务计划`运行即可，选择`新建基本任务`，选择`当用户登录时`触发，选择要运行的程序`synergyc.exe`(注意是客户端程序)，配置参数`-n Win10 10.1.230.73`(客户机屏幕名称和服务器IP)
最后取消控制面板`用户账户`中计算机唤醒时需要登录的选项。
保证Manjaro中的服务器端成功运行，重新登录Win10试试

感觉不错！

37. Barrier 基于Synergy的开源免费软件，[下载地址](https://github.com/debauchee/barrier)
使用Synergy的过程中，第一次连接客户机即Win10始终不能控制，需得再次运行Synergy一次。终于另外搜到一个更简单的Barrier！
- 服务器安装配置
```bash
yay barrier   # 安装
barrier       # 运行，选择服务器端，图形方式选择屏幕排列位置以及客户端名称如Win10（需要与客户机一致），注意在setting中去掉SSL加密！！！（选择了SSL加密通信后发生抖动）
exec --no-strartup-id barrier  # 写到 ~/.i3/config中自启动
```
- 客户机安装及配置：
下载Windows版的 [Barrier](https://github.com/debauchee/barrier/releases)，安装即可
运行Barrier，选择客户机，过程中建议安装Bonjour，设置服务器IP和客户端名称（需与服务器端一致），去掉auto config，注意在setting中去掉SSL加密！！！点击开始，测试是否OK
如果OK了，其它无需做什么，Barrier自动以守护进程运行

彻底摆脱了！

38. OpenVPN 两个客户端
直接将两个证书文件夹复制到OpenVPN的`config`目录即可

39. VS Code远程开发
目前使用X1C进行开发全栈开发，无论前端和后台都是热更新，那么笔记本的CPU有些吃紧，风扇经常狂转，很是心疼。
刚好得到一个VPS(16核，32G），也听说Code推出了remote方式，看了文档，非常好，正是我要的，编译/运行等都由VPS进行强大的支持，笔记本这边就是个IO设备了。下面是配置过程：

```bash
服务器配置好openssh server
客户机vscode中安装remote-ssh插件
完毕后左侧将有remote图标，点击添加remote host即可
客户机如果没生成密钥对则ssh-keygen生成即可（参加34条）
然后复制公钥到remote host。ssh-copy-id -i ～/.ssh/id_rsa.pub someone-on-remote-host@remote-host
ssh测试一下
服务器那边安装开发环境即可
```
再也不担心笔记本吃不消了，关键速度很快！

40. 升级Manjaro的内核
浏览器滚动发生拖尾，突然觉得是不是该把414的内核升级了，查了一下当前稳定版是513。ok，马上进行。
mod+z调出菜单，setting->manjaro setting，选择内核安装即可，完毕后卸载当前运行的内核，重启。
好像有新的感觉，哈哈哈。

41. 在大数据中心制作Manjaro镜像
管理机不能外网，本地先制作，然后上传到管理机
```bash
yay docker  # 安装docker
sudo systemctl start docker # 启动docker
sudo docker pull manjarolinux/build-unstable:latest # 拉回image
sudo docker images # display image
sudo docker run -t -i manjaro/build--unstable /bin/bash # 运行镜像的shell
... # 在shell中各种更新和安装相关软件，如
sudo pacman-mirrors -i -c China -m rank等，注意提示符中的id，如：c92ff0b9a966，那是容器id，我们提交时需要
exit  # 退出shell
sudo docker commit -m="add ssh" -a="wangyong" c92ff0b9a966 manjaro:1902 # 提交刚才那个容器镜像的副本
sudo docker images  # 再次查看，现在就有新的image了
sudo docker save -o manjaro.tar manjaro # 将image保存为tar文件
sudo scp manjaro.tar root@x.x.x.x:/XXX/YYY  # 上传到大数据中心管理机
```
登录管理机，从文件导入image
```bash
docker load < manjaro.tar
docker images # 查看image
docker tag 899bd984 manjaro:1902  # 修改image的tag等
# 修改数据库
```
42. 使用Google Code Lab进行教程撰写

安装
```bash
wget https://github.com/googlecodelabs/tools/releases/download/v2.2.0/claat-linux-amd64
sudo mv claat-linux-amd64 /usr/bin/claat
sudo chmod +x /usr/bin/claat
```
使用MD格式

按Google Code Lab预制的样式（ [格式参考](https://medium.com/@mariopce/tutorial-how-to-make-tutorials-using-google-code-labs-gangdam-style-d62b35476816)）。

将`md`转换为`html`，然后运作一个内置的`Web`服务器供查看

```bash
claat export test.md
claat serve -addr 0.0.0.0：9090  # 默认localhost：9090
```

使用-GoogleDoc格式

也可直接在 doc.google.com 上按规定格式进行撰写，然后使用以下命令即可（注意：因为要读取google文档，需科学上网）
```bash
claat export googledocID
# 第一次需得到授权，会给出的 url，复制该 url，访问后得到 code，粘贴即可。以后不会再出现
```

43. 内网穿透的免费工具--Zerotier
该工具可以免费的将各个计算机在 Internet 中组建为一个 LAN，也就实现了内网穿透。
先在 https://my.zerotier.com/ 上以google帐号登录，然后生成 NetworkID，复制
然后在 Manjaro 上：
```bash
sudo yay zerotier-one # 安装
sudo systemctl enable zerotier-one
sudo systemctl start zerotier-one
sudo zerotier-cli join networkID  # 加入
ip a # 可查看到新的IP地址
```
最后在https://my.zerotier.com/上将该加入的计算机授权即可（Auth处打勾)
其它机器如法炮制，Windows直接下载客户端安装加入即可，可选开机启动。
因使用公用国外中转PLANET服务器，所以搭建自己的MOON服务器，申请了TC的免费服务器，选Ubuntu系统，配置如下：
```
curl -s https://install.zerotier.com/ | sudo bash   # 安装
sudo zerotier-cli join <network id> # 加入并在控制台授权
sudo zerotier-idtool initmoon /var/lib/zerotier-one/identity.public >> moon.json  # 通过 identity.public 生成一个 moon.json 文件
编辑刚生成的 moon.json 文件，在"stableEndpoints"写入 TC 服务器的IP : [ "TC的IP/9993"]，注意有""号，默认端口9993
sudo zerotier-idtool genmoon moon.json  # 生成签名文件 000000xxxxxxxxxx.moon，其中的xxxxxxxxxx实际就是该Moon节点的id
sudo mkdir /var/lib/zerotier-one/moons.d
sudo cp 000000xxxxxxxxxx.moon /var/lib/zerotier-one/moons.d
重启电脑。至此，VPS 上（moon 服务器）配置完成。
```
然后在需要使用该中转节点的客户机上运行：`sudo zerotier-cli orbit xxxxxxxxxx xxxxxxxxxx`（其中的xxxxxxxxxx是该Moon节点的id，注意Windows下需使用管理员PowerShell）;
重启`zerotier-one`服务或计算机，使用`sudo zerotier-cli listpeers`查看，出现带**ip地址**的`MOON`节点表示成功(需要一定延时`MOON`才可用，可`ping`该Moon节点IP加快出现的速度)。
如此速度可接受。


44. 一台电脑同时连接内网和外网问题

目前在`Win10`中通过，主要思路是添加路由，也注意删除多余的默认路由，因为当前有两个网关`route print`后使用·route delete 0.0.0.0 mask 0.0.0.0 X.X.X.X`。
```
route add 0.0.0.0 mask 0.0.0.0 10.1.1.1   # 默认，假设10.1.1.1是外网网关
route add 192.168.5.0 mask 255.255.255.0 10.2.2.2   # 假设10.2.2.2是内网网关
```

45. 安装ROS2-galactic在Manjaro
因ROS只提供了Ubuntu的安装包，因此需要从源码进行编译安装。安装过程需要科学上网，所以将v2raya调整为透明代理即系统全局代理

```
1. yay ros2-galactic
2. 编译安装过程中出现 yaml_cpp_vendor 包的"CMake Error: Unknown argument -std=c++14 -w"编译错误，经过大量查询，发现需要更新源文件。待其下载完毕安装前，打开
/home/qige/.cache/yay/ros2-galactic/ros2/src/.../yaml_cpp_vendor/CMakeLists.txt文件，替换
<     list(APPEND YAML_C_FLAGS "-w")
<     list(APPEND YAML_CXX_FLAGS "-std=c++14 -w")
---
>     set(YAML_C_FLAGS "${YAML_C_FLAGS} -w")
>     set(YAML_CXX_FLAGS "${YAML_CXX_FLAGS} -std=c++14 -w")
3. 注意编译安装过程中需保持科学上网，X1C上用了90m！
4. 安装完毕，设置运行环境变量（打开terminal时略有延迟，以后不用时可删除）
echo "source /opt/ros2/galactic/setup.bash" >> ~/.bashrc
echo "export ROS_DOMAIN_ID=42" >> ~/.bashrc
```
> 参见 [官网配置](https://docs.ros.org/en/galactic/Tutorials/Configuring-ROS2-Environment.html)


----
### Refrences:
> 1. http://www.php-master.com/post/258672.html
> 1. http://www.cnblogs.com/vachester/p/5649813.html
> 1. https://dev.to/lobo_tuerto/you-need-to-know-about-i3-363c  (fonts configure)
> 1. https://ohmyarch.github.io/2017/01/15/Linux下终极字体配置方案 
> 1. https://fontawesome.com/cheatsheet (开源图标字体，直接复制粘贴使用)
> 1. https://www.jianshu.com/p/cf14660d8af2 (#Manjaro-i3的配置)
> 1. https://www.jianshu.com/p/6e9eb98c0494 (Manjaro linux 安装笔记)
> 1. http://jimolonely.github.io/2017/12/27/linux/001-linux-manjaro/ (配置manjaro)

----
