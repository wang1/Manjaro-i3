# Manjaro-i3
##这是一个Manjaro -i3的配置记录和相关文件
三个配置文件
i3's configure file--> ~/.i3/config, 
i3status's configure file --> /etc/i3status.conf,
URxvt‘s configure file --> ~/.Xresources

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
10. oh-my-zsh
Greetings. Before starting, I want to inform you guys that this tutorial is copied and modified from the old tutorial I wrote on the old Manjaro forum here 15.
This tutorial will guide you to replace Bash with Zsh and using Oh My Zsh to customize Zsh to the maximum. So let’s get started. :slight_smile:

What is Bash and Zsh?
Bash (Bourne-again Shell) is a Unix shell and command language written by Brian Fox for the GNU Project as a free software replacement for Bourne Shell. Bash is also a command processor that typically runs in a text window, where the user types commands that cause actions. Source: Wikipedia.
Zsh (The Z Shell) is a Unix shell that can be used as an interactive login shell and as a powerful command interpreter for shell scripting. Zsh can be thought of as an extended Bourne shell with a large number of improvements, including some features of bash, ksh, and tcsh. Source: Wikipedia 4

Why the Switch?
Here are the advantages of Zsh over Bash:

Efficiency, so it’s noticably faster.
Improved tab completion
Improved globbing
Improved array handling
Full customisability, this is why this tutorial is made :slight_smile:
What to Install
Before jumping into the tutorial, there are two ways of installing Oh My Zsh. The first way (the easier way) is to install it from AUR, but note that the package is now flagged out-of-date 180 (the day this tutorial is written). The second way (the not so hard way) is to manually clone the repository. So here are what you need to install:

1. The AUR Way
All you need to install is oh-my-zsh-git 180. That’s all, it will pull in all the packages you need to get it working.

2. The Manual Cloning Way
Install the packages zsh and git. That’s all you need. :slight_smile:

Configuring Zsh Using AUR (Easier)
Install the package oh-my-zsh-git from AUR manually or by using your favorite AUR helper.

After installing oh-my-zsh-git from AUR, type this command into the terminal:

chsh -s $(which zsh)
This will make Zsh as your default shell (replacing Bash) for your username. It will give Check if your default shell is changed by typing in this command into the terminal:

echo $SHELL

If it shows you /bin/zsh, then you’ve just successfully replaced Bash with Zsh. Congratulations :slight_smile:

Now, optionally you can try to run zsh in the terminal, it will ask you about the .zshrc you want to create. Just choose anything because we’re going to replace it with the .zshrc Oh My Zsh provided anyway. Now that you’re sure zsh is running without any issue, you can now copy the .zshrc provided by Oh-My-Zsh into your home directory by using this command:
sudo cp /usr/share/oh-my-zsh/zshrc ~/.zshrc
You need to log out and log back in in order to complete the process of replacing Bash with Zsh.

Open up ~/.zshrc you just copied using your favorite text editor and customize it the way you want it to be (Tips: the default is actually already good to go). Here are what you can edit in the .zshrc as of the day this tutorial is made:
# Path to your oh-my-zsh installation.
export ZSH=/usr/share/oh-my-zsh/
This is what differentiate first and second way. The default directory of Oh My Zsh if you’re installing from AUR is in /usr/share/oh-my-zsh/ so you can leave this unchanged.

# Set name of the theme to load.
# Look in ~/.oh-my-zsh/themes/
# Optionally, if you set this to "random", it'll load a random theme each
# time that oh-my-zsh is loaded.
ZSH_THEME="robbyrussell"
Change the theme to whatever you like. You can find the theme names in the /usr/share/oh-my-zsh/themes/. The themes are named as “themename.zsh-theme” without the quote. So all you need to put in the ZSH_THEME value is themename without the .zsh-theme. You can just try them out one by one by opening another terminal after changing the value to see how the theme looks like.

# Uncomment the following line to use case-sensitive completion.
# CASE_SENSITIVE="true"
This is my favorite feature of Oh My Zsh. :slight_smile: Basically what this does is you can now just type in ~/de and press tab and Zsh will show you the suggestion and complete the command into ~/Desktop for you. In Bash, you need to type in ~/De to show the suggestion. This makes writing path a lot faster.

# Uncomment the following line to use hyphen-insensitive completion. Case
# sensitive completion must be off. _ and - will be interchangeable.
# HYPHEN_INSENSITIVE="true"
This is also a very nice feature. It’s disabled by default but you can enable it by uncomment it (delete the # in front of the line). What this does is there are some annoying files that use _ as part of their name (like bash_history, your new zsh_history, etc), so instead of typing _ (which require another finger to press the Shift key), you just type in - and it will show you the suggestion too. :wink:

# Uncomment the following line to disable bi-weekly auto-update checks.
DISABLE_AUTO_UPDATE="true"

# Uncomment the following line to change how often to auto-update (in days).
# export UPDATE_ZSH_DAYS=13
Whether to enable or disable auto updating of your Oh My Zsh. Reading the PKGBUILD of the oh-my-zsh-git, this is disabled by default. I wonder what’s the reason because I think it’s always good to keep this up to date but maybe they have a reason for it (although I can’t think of the reason why).

# Uncomment the following line to disable colors in ls.
# DISABLE_LS_COLORS="true"

# Uncomment the following line to disable auto-setting terminal title.
# DISABLE_AUTO_TITLE="true"
I think I don’t need to explain these two, the comments are quite obvious. :slight_smile:

# Uncomment the following line to enable command auto-correction.
# ENABLE_CORRECTION="true"
This feature will auto correct your commands just like the auto correction in your smartphone’s keyboard do. For example I want to type in conky and I mistyped it to cony. When I press enter, Zsh will ask me this: “zsh: correct ‘cony’ to ‘conky’ [nyae]?”. Very nice feature right? :slight_smile:

# Uncomment the following line if you want to disable marking untracked files
# under VCS as dirty. This makes repository status check for large repositories
# much, much faster.
# DISABLE_UNTRACKED_FILES_DIRTY="true"

# Uncomment the following line if you want to change the command execution time
# stamp shown in the history command output.
# The optional three formats: "mm/dd/yyyy"|"dd.mm.yyyy"|"yyyy-mm-dd"
# HIST_STAMPS="mm/dd/yyyy"

# Would you like to use another custom folder than $ZSH/custom?
# ZSH_CUSTOM=/path/to/new-custom-folder
These are actually not very important and most of us just don’t care, or do you? :slight_smile:

# Which plugins would you like to load? (plugins can be found in ~/.oh-my-zsh/plugins/*)
# Custom plugins may be added to ~/.oh-my-zsh/custom/plugins/
# Example format: plugins=(rails git textmate ruby lighthouse)
# Add wisely, as too many plugins slow down shell startup.
plugins=(git)
This is another nice feature of Oh My Zsh. Plugins allow us to even more customize Zsh. You can find the available plugins in the /usr/share/oh-my-zsh/plugins/. Just add in the plugin you want to use inside the () and enjoy them.

Tips: There’s actually a plugin called archlinux and it contains a very nice list of aliases for yaourt and pacman. If you want to modify the aliases the way you want them to be, just copy the archlinux folder into /usr/share/oh-my-zsh/custom/plugins/ and rename it to whatever you like, modify it and call it out. :slight_smile:

# User configuration

export PATH=$HOME/bin:/usr/local/bin:$PATH
# export MANPATH="/usr/local/man:$MANPATH"

source $ZSH/oh-my-zsh.sh

# You may need to manually set your language environment
# export LANG=en_US.UTF-8

# Preferred editor for local and remote sessions
# if [[ -n $SSH_CONNECTION ]]; then
#   export EDITOR='vim'
# else
#   export EDITOR='mvim'
# fi

# Compilation flags
# export ARCHFLAGS="-arch x86_64"

# ssh
# export SSH_KEY_PATH="~/.ssh/dsa_id"[/code]

This is the section where you may want to add in whatever you have in your .bashrc.

[code]# Set personal aliases, overriding those provided by oh-my-zsh libs,
# plugins, and themes. Aliases can be placed here, though oh-my-zsh
# users are encouraged to define aliases within the ZSH_CUSTOM folder.
# For a full list of active aliases, run `alias`.
#
# Example aliases
# alias zshconfig="mate ~/.zshrc"
# alias ohmyzsh="mate ~/.oh-my-zsh"
This is where you may want to put in your aliases. But if you want a cleaner zshrc I suggest you to just add a custom plugin for it. :slight_smile:

And that’s all about the configuration file guys, now get back to the tutorial.

Now, optionally you can also make the root account to use Oh My Zsh by typing this command in the terminal:
sudo chsh -s $(which zsh) root && sudo ln -s ~/.zshrc /root/.zshrc
What the command above doing is to change the root default shell into Zsh and symlink the Zsh configuration file that you just edited into the home folder of root account. Now, type in sudo su, enter your root password and see if your root account is using Zsh (you can actually just see if the command prompt is now using the theme you’ve chosen) by using this command:

echo $SHELL
Congratulations, you have just successfully replaced Bash with Zsh. :slight_smile:

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
