# Manjaro-i3
##这是一个Manjaro -i3的配置记录和相关文件
三个配置文件
i3's configure file--> ~/.i3/config, 
i3status's configure file --> /etc/i3status.conf,
URxvt‘s configure file --> ~/.Xresources
桌面组件conky，默认是i3配置文件中启动/usr/bin/start_conky_maia脚本，该脚本加载了/usr/share/conky目录下的两个配置文件，分别是右上角的conky_maia和conky1.10_shrotcuts_maia

Refrences:
-1. http://www.php-master.com/post/258672.html
0. http://www.cnblogs.com/vachester/p/5649813.html
1. https://dev.to/lobo_tuerto/you-need-to-know-about-i3-363c  
#fonts configure
2. https://ohmyarch.github.io/2017/01/15/Linux下终极字体配置方案
#开源图标字体，直接复制粘贴使用
https://fontawesome.com/cheatsheet
#Manjaro-i3的配置
3. https://www.jianshu.com/p/cf14660d8af2
4. sudo pacman -S remmina freerdp，远程登录工具remmina，支持各种协议（需另外安装freerdp），logout/reboot生效
5. google-chrome/fcitx/nitrogen壁纸/shadowsocks（ss-qt5运行后可配置开机启动及最小化）/stardict/wps-office/ttf-wps-fonts/visual-studio-code-bin

morc_menu中动Arandr可进行双显示器设置，拖动即可
6. 
安装中文输入法
以安装搜狗为例

sudo pacman -S fcitx-sogoupinyin
sudo pacman -S fcitx-im
sudo pacman -S fcitx-configtool
设置中文输入法环境变量，否则中文输入法无法启动

sudo nano ~/.xprofile
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
保存成功后，在终端输入fcitx启动服务。

以上是否需要这么复杂还可考虑？
我使用了google，默认的拼音难用，但似乎google不能配置，已经设置fcitx自动运行，且使用morc_menu的fcitx config设置了shift切换和<>翻页、模糊音等。

=======================

====================

#
Manjaro linux 安装笔记
7. https://www.jianshu.com/p/6e9eb98c0494
配置manjaro
8. http://jimolonely.github.io/2017/12/27/linux/001-linux-manjaro/
================URvxt终端字体====================================
##antialias为平滑，需要一个中文字体
9. sudo pacman -S ttf-monaco wqy-microhei && vim ~/.Xresource
URxvt.font:xft:Monaco:pixelsize=16:antialias=true,xft:WenQuanYi Micro Hei Mono:pixelsize=16:antialias=true
URxvt.boldFont:xft:Monaco:pixelsize=16:Bold:antialias=true,xft:WenQuanYi Micro Hei Mono:pixelsize=16:Bold:antialias=true

执行xrdb -load ~/.Xresources生效
======================================================


======================================

######################################################################
#                            oh-my-i3
# Date    : 12/12/2015
# Author  : Allen_Qiu
# Version : v1.1
#
# 依赖:conky feh (可选推荐:xcompmgr freetype-infinality)
# 部分特性可能需要新版本支持，请更新至最新版本或自行修改相应部分
# 更多内容请参考:
# i3 User’s Guide: http://i3wm.org/docs/userguide.html
# oh-my-i3 github: https://github.com/ID1258/oh-my-i3.git
#
######################################################################

# => 开机自启（根据需要取消相应的注释#）
# 壁纸须先安装feh，并在此指定路径
exec --no-startup-id feh --bg-scale "$HOME/oh-my-i3/wallpaper.jpg"
# 设置透明须先安装xcompmgr，并在此指定软件和透明度（默认0.75），sleep保证transset在其所设置的软件之后启动，根据情况调节大小
#exec --no-startup-id xcompmgr &
#exec --no-startup-id sleep .2 && exec transset -n i3bar 0.75
# 建议另外指定一个脚本来启动通用的开机自启项目
exec --no-startup-id $HOME/bin/RC
# 总有一些奇葩无法正常用脚本启动
exec --no-startup-id kuaipan4uk &

# => 设定mod键与工作区名
set $mod  Mod4
set $WS1  1:?8?5
set $WS2  2:?8?3
set $WS3  3:?8?8
set $WS4  4:?8?5
set $WS5  5:?8?5
set $WS6  6:?8?7
set $WS7  7:?8?7
set $WS8  8:?8?1
set $WS9  9:?8?5
set $WS0 10:?8?4

# => 工作区切换&智能启动（添加智能启动脚本~/bin/st 自动且不重复打开工作区相应程序）
# 自动切换到新打开的窗口(需4.12版本支持)
#focus_on_window_activation focus
# 重复切换到当前工作区时会返回上一个所在工作区，有可能造成工作区错位
#workspace_auto_back_and_forth yes
# 注释部分请依据个人喜好更改并取消注释，且把i3-sensible-terminal替换为常用term
bindsym $mod+1 workspace $WS1, exec --no-startup-id ~/bin/st xfce4-terminal
bindsym $mod+2 workspace $WS2, exec --no-startup-id ~/bin/st google-chrome
bindsym $mod+3 workspace $WS3, exec --no-startup-id WizNote > /dev/null 2>&1
bindsym $mod+4 workspace $WS4, exec --no-startup-id ~/bin/st smplayer
bindsym $mod+5 workspace $WS5, exec --no-startup-id ~/bin/st thunar
bindsym $mod+6 workspace $WS6, exec --no-startup-id ~/bin/st virtualbox
bindsym $mod+7 workspace $WS7
bindsym $mod+8 workspace $WS8
bindsym $mod+9 workspace $WS9
bindsym $mod+0 workspace $WS0

# => 快捷启动
bindsym $mod+Return exec --no-startup-id xfce4-terminal
bindsym $mod+r      exec --no-startup-id dmenu_run -b -fn 'Monaco-12' -nb '#333333' -nf '#FFFFFF' -sb '#111111' -sf '#3399FF'
bindsym $mod+e      exec --no-startup-id thunar

# => 自定义窗口（支持定义边框类型，窗口布局，大小调整，自动归类工作区等等，多个定义用,隔开）
for_window [class="(?i)thunar"] move container to workspace $WS5, workspace $WS5, layout tabbed

# => 窗口边框类型（边框类型有normal正常/none无边框/pixel 1 自定义宽度）
# 默认普通窗口的边框类型
new_window none
# 默认浮动窗口的边框类型
new_float normal
# 取消工作区边缘的边框
hide_edge_borders both
# 在三种边框类型中切换
bindsym $mod+b border toggle

# => 新建窗口的分割方向
bindsym $mod+h split h
bindsym $mod+v split v

# => 移动窗口
bindsym $mod+Left  move left
bindsym $mod+Down  move down
bindsym $mod+Up    move up
bindsym $mod+Right move right

# => 调整窗口大小
bindsym $mod+Shift+Left  resize shrink width  10 px or 1 ppt
bindsym $mod+Shift+Down  resize grow   height 10 px or 1 ppt
bindsym $mod+Shift+Up    resize shrink height 10 px or 1 ppt
bindsym $mod+Shift+Right resize grow   width  10 px or 1 ppt

# => 关闭窗口
bindsym mod1+F4 kill

# => 焦点切换
# 焦点跟随鼠标移动
focus_follows_mouse yes
# 焦点切换到父窗口
bindsym $mod+q focus parent
# 焦点切换回子窗口
bindsym $mod+Shift+q focus child
# 焦点切换到浮动窗口
bindsym $mod+Shift+space focus mode_toggle
#
bindsym $mod+w focus up
bindsym $mod+s focus down
bindsym $mod+a focus left
bindsym $mod+d focus right

# => 布局切换
# 切换到堆叠布局
# bindsym $mod+z layout stacking
# 切换到标签布局
# bindsym $mod+x layout tabbed
# 切换到平铺布局（竖直/水平）
# bindsym $mod+c layout toggle split
# 在所有布局中轮回切换
bindsym $mod+x layout toggle all
# 窗口切换到全屏
bindsym $mod+f fullscreen toggle
# 窗口切换到浮动
bindsym $mod+space floating toggle
# 窗口切换为粘滞
bindsym $mod+g sticky toggle

# => 移动窗口到另一个工作区
bindsym $mod+mod1+Left  move container to workspace prev, workspace prev
bindsym $mod+mod1+Right move container to workspace next, workspace next
bindsym $mod+mod1+1 move container to workspace $WS1, workspace $WS1
bindsym $mod+mod1+2 move container to workspace $WS2, workspace $WS2
bindsym $mod+mod1+3 move container to workspace $WS3, workspace $WS3
bindsym $mod+mod1+4 move container to workspace $WS4, workspace $WS4
bindsym $mod+mod1+5 move container to workspace $WS5, workspace $WS5
bindsym $mod+mod1+6 move container to workspace $WS6, workspace $WS6
bindsym $mod+mod1+7 move container to workspace $WS7, workspace $WS7
bindsym $mod+mod1+8 move container to workspace $WS8, workspace $WS8
bindsym $mod+mod1+9 move container to workspace $WS9, workspace $WS9
bindsym $mod+mod1+0 move container to workspace $WS0, workspace $WS0

# => 暂存窗口（额外的可隐藏浮动窗口，取消浮动还原成普通窗口）
# 转换普通窗口为暂存窗口
bindsym $mod+Shift+minus move scratchpad
# 呼出/隐藏暂存窗口
bindsym $mod+minus scratchpad show

# => 重新载入（更改配置文件后只须重载即可生效，不包含自启部分）
bindsym mod1+Shift+r restart

# => 电源管理（Pause Break键呼出)
set $mode_system 系统:锁屏(L) 注销(O) 关机(S) 重启(R) 取消(Esc)
bindsym Pause mode "$mode_system"
mode "$mode_system" {
    bindsym l exec i3lock -c '#333333', exec sleep .1 && exec xset dpms force off, mode "default"
    bindsym o exec i3-msg exit
    bindsym s exec systemctl poweroff
    bindsym r exec systemctl reboot
    bindsym Escape mode "default"
}

############################# 主题相关 ###############################

# 字体
font pango:Monaco 10

# 窗口颜色                边框    背景    文字    提示
client.focused          #333333 #333333 #FFFFFF #333333
client.focused_inactive #999999 #999999 #FFFFFF #3399FF
client.unfocused        #999999 #999999 #FFFFFF #3399FF
client.urgent           #990000 #990000 #FFFFFF #990000
client.placeholder      #000000 #000000 #FFFFFF #000000
client.background       #FFFFFF

# i3bar
bindsym $mod+m bar mode toggle
bar {
    # i3bar调用
    status_command conky -c $HOME/.config/i3/conkyrc_bar
    # 显示位置
    #position top
    # 是否隐藏
    #mode hide
    # 显示/隐藏切换键
    #modifier $mod
    # 拆解工作区名（隐藏前面的1:2:3:……）
    strip_workspace_numbers yes
    # 定义分隔符（适用于i3status）
    #separator_symbol " "
    colors {
        background #333333
        statusline #FFFFFF
        #eparator  #3399FF
        # 工作区颜色         边框    背景    文字
        focused_workspace  #111111 #111111 #FFFFFF
        active_workspace   #FFFFFF #FFFFFF #FFFFFF
        inactive_workspace #333333 #333333 #FFFFFF
        urgent_workspace   #990000 #990000 #FFFFFF
        #binding_mode       #990000 #990000 #FFFFFF
    }
}
