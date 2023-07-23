---
title: 无折腾，不linux：关于Ventoy-Linux，Kubuntu和系统配置的心得
date: 2023-07-17 23:37:58
tags: [折腾不止,ubuntu,kubuntu,linux to go,ventoy]
cover: cover.jpg
---

## *本文正在施工中*

# 前言

一切的一切都要从两年前开始讲起。

2021年暑假，我知晓了wintogo这种系统形式的存在。心血来潮，在网上购入了一块三星的U盘。

那时候对wtg的性能要求没什么概念，也并没有也不知道选择带有wtg认证的设备。这是一个后来令我十分痛苦的决定。

之后不短的一段时间，我在学校机房使用的一直是自己的wtg。期间windows11推出，就升级到了win11。但是，由于U盘孱弱的4k读写性能（另一方面，我一直怀疑我盘的4k性能尤其糟糕，不知道与前期格盘过多是否有关），恼人的迟滞和卡顿一直伴随在从开机到关机的每一个过程中，即使关闭windows update、使用内存盘也难以缓解，windows较大的系统体积也使64G的有限空间更加捉襟见肘。考虑到没有必要~~也没有钱~~再买一块新的固态硬盘，在一次失手（这就牵扯到了另一个有关于学校大屏的狗血故事，有空可以写写）搞炸了自己的wintogo之后，我决定投入linux的怀抱。

但这其实并不是我第一次接触linux。在学校机房时，我就在另一位dalao的帮助下，和老师一起给两三台电脑安装过ubuntu 22.04，并在其上部署了oj。当时就吃了不少苦头，走了不少弯路。于是，在开始我的折腾之路的时候，我非常自信，以为这一次能够快速拿下，之后就被打脸了。人是不长记性的动物，尤其是我这种，记性尤其差。当时配置的很多细节现在都忘了，结果还是要从头再来，文档也要重翻一边，何况又出现了巨量的新问题。所以，这次我决定写点东西记下来，一方面保证下次不再走弯路，另一方面大概也可以让后来人也少走一点弯路。总之，让我们开始吧！

# Ventoy的配置

## Ventoy本体的安装和配置

Ventoy的使用体验还是非常愉快的。有清晰的中文资料，安装配置也不算复杂，只是当时配置安全启动证书的过程让我十分摸不到头脑。殊不知，安全启动这个噩梦还会在后来继续折磨我。

要使用Ventoy安装虚拟磁盘系统，可以参考[这篇文档](https://www.ventoy.net/cn/plugin_vtoyboot.html)。

需要注意的是，ventoy官方文档中提到，virtualbox中生成的虚拟硬盘文件必须是固定大小（即预先分配全部空间），对于一个较大的虚拟磁盘，这的确有一定的不便，但是，如果使用动态大小，根据另外一位同学的经验，可能会导致虚拟磁盘文件的实际大小不受控制地增加，但具体情况需要进一步实践来验证。

ventoy只支持vdi和vhd文件，不支持vhdx。如果你使用vdi作为虚拟磁盘格式，且需要复制虚拟磁盘文件，请**使用virtualbox自带的VBoxManage工具，否则将会在注册虚拟磁盘时报错。**在windows中它会位于virtualbox的根目录下。如果你不幸已经使用这种方式复制了一个超大容量的静态大小虚拟磁盘，为了节省时间，可以直接重设新磁盘的uuid，达到一样的效果。

要复制，可以在virtualbox的根目录下使用：

```powershell
VBoxManager clonevdi [原文件路径] [新文件路径]
```

要重新分配uuid。可以输入：

```powershell
VBoxManage internalcommands sethduuid [文件路径]
```

来改变虚拟磁盘的uuid。

令人无语的是，当你直接复制vdi文件并尝试注册复制出的文件时，virtual box并不会向你指出导致错误的具体原因（当然也可能是因为日志标注过于抽象导致我看不懂），只能凭借自行搜索来解决。

## 发行版大乱斗

在使用萝卜头论坛的wtg制作工具的时候，对系统的选择是有限的，并且更换系统就需要再次格盘，而Ventoy的存在，让整个过程得以更为灵活自由。我先后尝试了Manjaro，fedora和ubuntu。需要注意，在virtualbox中配置Manjaro时，需要分配比较高的内存，否则可能会导致开机卡死或黑屏。~~当时折腾了好长时间也没找出原因~~。

Ventoy默认将U盘的主分区格式化为exFAT格式，这个分区可以手动格式化为NTFS，不过安装linux不建议改变，不确定是否会影响。

还有一点，每次遇到系统内核或驱动更新时，都要重新安装vtoyboost插件。不知道为什么，fedora在我的一次开机时突然进入自动更新，~~之后就成功把我的引导干碎了~~，需要注意。

## 安全启动

> so fuck you, Microsoft.

Arch系系统默认不支持安全启动。要使用安全启动，可以参考这两篇文章:

[2023 archlinux 启用 Secure Boot (安全启动) 的最简单方法 -- sbctl](https://zhuanlan.zhihu.com/p/616940828?utm_id=0)

[在Arch Linux中配置安全启动（Secure Boot)](https://zhuanlan.zhihu.com/p/620568516)

虽然fedora和ubuntu（debian）通过shim理论上支持安全启动，但在我的设备（联想小新Pro16 2021ACH锐龙版）上，始终无法在安全启动启用下成功引导系统。但令人诧异的是，Ventoy的选择菜单是可以正常使用的。

# 一些系统配置的细节

## KDE的安装

我一开始打算装Manjaro的原因，按照我尚浅薄的linux使用经验，说是完全因为喜爱pacman，那是一点也不诚实的。另外的重要的原因，是Manjaro有社区维护的KDE版本，而在之前和gnome的接触，使我萌生了换换口味的想法。于是，我试图在ubuntu上手动安装KDE。是的，那个时候我还不知道Kubuntu的存在。

对于ubuntu用户来说，可以使用

```bash
sudo apt-get install kubuntu-desktop
```

来安装KDE桌面，理论上来说，这与直接安装kubuntu没有区别。

## 配置静态ip

在机房使用时，

我可以确定的是，gnome下的普通ubuntu并没有这个问题，这很大可能是KDE的问题。

通过搜索发现，这似乎是kubuntu的一个老bug。在很早的时候，就有人在网络上提出这个问题。解决方法是直接使用命令行配置。

ubuntu从17.01开始使用netplan（而非/etc/network/interfaces）配置网络设置。总体流程可参考：[Ubuntu Server20.04 静态ip配置（netplan）](https://blog.csdn.net/qq_45949008/article/details/118862854)

网卡的名称可以利用`ip a`查询。

应用配置之前，可以使用`sudo netplan try`测试配置是否存在问题。之后该配置可以在网络设置中正常显示。

最简配置可以是这样的：

```yaml
# 01-network-manager-all.yaml
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    enp2s0:		#网卡名称
      dhcp4: no		#关闭dhcp
      addresses: [10.160.219.43/24]		#注意这里/后要填写子网掩码。如点分十进制255.255.255.0对应为24
      gateway4: 10.160.219.254
      nameservers:
        addresses: [8.8.8.8]
```

/24的24指的是这个掩码由连续的24个1组成，按照点分二进制，表示为11111111.11111111.11111111.11111111.00000000，即255.255.255.0。有关掩码的一些的问题，有时间以后单独写。

## 有关科学上网

不知道为什么，在我的系统中qv2ray配置后切换节点将造成剧烈卡顿，切换后也无法正常工作，几分钟后报错崩溃。目前qv2ray已经停止维护，不再推荐使用。可以选择[qv2rayA](https://github.com/v2rayA/v2rayA)作为替代。

clash方面，clash内核可以在linux下正常运行，注意配置开机启动即可。另外，在windows下非常流行的[clash for windows](https://github.com/Fndroid/clash_for_windows_pkg/releases) GUI终端在linux上同样可用。是的，你没看错。linux下还可以使用[clashy](https://github.com/SpongeNobody/Clashy)

KDE的全局代理设置不一定可以在所有情况下使用，可以借助CFW的tun模式，或在软件中单独设置代理。clash的http(s)代理端口默认为7890，socks为7891。

作为一个倒霉蛋，使用cfw时还碰到了这样两个问题。

如果你的代理端口一直显示0且无法更改，可能是因为一些意外导致开启了多个clash内核。这个时候需要

```bash
sudo killall clash
```

再重启cfw客户端。

如果你在一切配置都成功的时候仍然节点全部time out，记得低头看一下表是不是对的。

如上文所说，我的ubuntu至今没有解决安全启动问题，导致目前我在家中在ubuntu与win之间切换只能频繁地开关安全启动（我的win上还有bitlocker……）。而在这个过程中，我的win可能出现时间错误，可能是bios时间不对导致的。

## 关于rime
配置rime真的花了很长的时间，吐血。如果觉得麻烦，感觉也可以试试搜狗的linux版本。

### rime的安装

首先，建议使用fcitx5来安装rime。命令行输入

```bash
sudo apt install fcitx5-rime
```

即可，理论上不需要安装其他组件了。

安装完成之后，需要将fcitx5配置为默认输入法。命令行输入

```bash
im-config
```

进行配置。

另外，虽然我没有遇到什么问题，但可以按照[Ubuntu22.04安装Fcitx5中文输入法（详细）](https://zhuanlan.zhihu.com/p/508797663)的方式注册输入法。

如果你在安装ubuntu时一起安装的vscode有不能使用中文输入法的问题，删掉重新去官网下载重装即可。

### 输入方案管理

linux下，可以使用[rime/plum](https://github.com/rime/plum)管理输入方案。安装fcitx5-rime后即可自带一个朙月拼音输入法，默认是繁体，可以按`ctrl` + `~`键打开设置切换为简体。按照指引使用

```bash
curl -fsSL https://raw.githubusercontent.com/rime/plum/master/rime-install | bash -s -- :preset
```

后，会自动在当前目录下载plum工具和自带方案，并安装在rime中。如果你像我以上是一个双拼用户，可以在plum目录下运行

```bash
rime-install double-pinyin
```

来安装双拼方案。

### 词库和自定义

注意，对于fcitx5-rime，要使用的所有词库文件、配置文件、方案，都**应当放置在~/.local/share/fcitx5/rime/处，而不是任何其他位置。**如果出现任何找不到文件的问题，可以手动复制到这里。

在上述目录下手动建立`方案.custom.yaml`文件（如小鹤双拼的配置文件显示为`double_pinyin_flypy.schema.yaml`，对应的配置文件应命名为`double_pinyin_flypy.custom.yaml`），配置以下内容：

```yaml
patch:
# 指定自定义词库位置
    "translator/dictionary": luna_pinyin  #这里填写词库名称
    "menu/page_size": 9		#候选词数量，最大为9
```

注意，词库名称**不带有任何后缀**

好像横显和竖显也可以在此配置，不过从fcitx5的配置选项中同样可以调整。

对于词库配置，直接使用其它输入法的词库是一种比较省事的配置方法。可以使用[这个软件](https://github.com/studyzy/imewlconverter)来转换qq、搜狗等输入法的词库。不过这个工具需要.net环境支持，然后我就不出意外的在linux上配置.net时出了岔子……最后回到win下转换好的。

对于转换好的词库文件，应当以.dict.yaml为后缀，且需要在文件开头手动添加类似这样的文件头：

```yaml
---
name: sogo
version: "2016.03.02"
sort: by_weight
use_preset_vocabulary: true
import_tables:
    - luna_pinyin
    - zhwiki
...
```

其中，词库名称必须和文件名（去掉后缀）保持一致。

词库之间支持import，这样只需在方案配置中使用一个词库，并在那个词库的文件头的`import_tables`中加入要引入的其它词库即可。

还可以在[felixonmars/fcitx5-pinyin-zhwiki](https://github.com/studyzy/imewlconverter)找到利用维基百科制作的词库，但不建议只使用这一个词库。

对rime作出任何改动后，需要右键fcitx重新部署输入法。如果出现问题，可以查阅相关日志。

fcitx支持自定义皮肤。我的皮肤是deepin论坛里找的，源地址找不到了，但是网上应该挺多的。下载后将文件夹拖动到`.local/share/fcitx5/themes/`，之后即可在fcitx5设置中的附加组件-经典用户设置中设置主题。

# 结语

总结一下：

1. 所有的更新操作都要慎重
2. 有问题多搜索，就是别用百度。
3. 搞事情之前一定要想好后果和挽救措施
4. 善用官方文档，但是不要死磕官方文档。英文不好的情况下，csdn是很司马，但是内容还是很有价值的
5. 先解决网络链接问题，不然啥都解决不了。包括各种国内镜像源和科学上网，不然但凡遇到需要github的步骤都要疯狂看人品。后期使用ubuntu配置hexo和github page更是这样，装node.js差点没把我逼疯……（对了，不要用sudo n来更新nodejs，而是用nvm，不然可能会变得不幸）
