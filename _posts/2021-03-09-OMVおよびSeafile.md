---
layout: post
title: 技術-OMV docker上安装Seafile
date: 2021-03-09 
tag: 技術
---

<p>今年年初，在一位朋友的指导下，终于安装成功了NAS，通过OMV Docker,在上面装了开源的Seafile NAS。下面记录一下安装过程中踩过的坑。</p>

### 1 安装OMV以及Portainer
<p>可以参考以下链接：<a href="https://post.smzdm.com/p/av7z2564/" target="_blank">OMV安装：系统安装设置及一些功能的开启</a></p>
<p>此处需要特别注意两点<Br/>
1. 安装的时候需要2块U盘，1块U盘是保存安装文件，另外一块是OMV系统盘。因为系统也占用不了多少空间，所以我用的是32G的<Br/> 
2. 语言选择不要选中文，我起初选的是中文，后来到系统盘启动的时候出错。<Br/>
3. 关于安装盘的烧录。3月14号因为手欠，安装transmisson出错，于是命令行下，apt-get dist-upgrade执行，到最后installtion完成，然后界面不动，<Br/> 
&nbsp;&nbsp;我采取了断电重启，然后就是找不到了启动文件了，error: file /boot/grub/i386/normal.md not found，按照网上的指引，进入rescure mode,<Br/>
&nbsp;&nbsp;找到我的linux系统盘，set root=(hd0,msdos1),set prefix=(hd0,msdos1)/grub,之后，insmod /grub/normal.mod，仍然找不到normal.mod,<Br/>
&nbsp;&nbsp;看了其他的方法，要做debian的光盘启动盘，进入repair模式，太复杂，索性重装吧。重装的时候，先用一个新工具不行，因为安装进行到设置网络，DHCP<Br/>
&nbsp;&nbsp;的时候，因为之前openwrt的lan口应该是设置了静态IP，这块就卡死了。后来换了DiskGenius格式化U盘为Fat32之后，再用UltraIso,烧录后，<Br/>
&nbsp;&nbsp;到了相同的界面，它设置不了DHCP，但是它会跳出失败界面，告诉DHCP获取失败，请设置静态IP或者本次就不设置，终于完成了OMV的再次安装。<Br/>
&nbsp;&nbsp;OMV安装好了之后，我在另外一台PC上，敲入IP：80，出不来OMV的登录界面，后来大约等了半小时，登录画面才出来，因为我静态IP的地址是设置正确的，这个地方需要耐心等一下。<Br/>
4. OMV安装好了之后，要进行Painter和Extras的安装，用wget进行安装，有时候因为网络问题，会导致通过OMV进行下载很慢，此时可以采用别的机器下载好安装包，<Br/>
&nbsp;&nbsp;再通过Winscp工具上传到OMV进行安装。<Br/></p>

### 2 安装Seafile
<p>可以参考以下官网链接：<a href="https://cloud.seafile.com/published/seafile-manual-cn/docker/pro-edition/%E7%94%A8Docker%E9%83%A8%E7%BD%B2Seafile.md" target="_blank">用 Docker 部署 Seafile 专业版</a></p>
<p>此处需要特别注意两点<Br/>
1. 刚开始的时候选择的是开源版，后来安装好了之后，老是出现Bad GateWay的错误，我就卸载了然后安装了专业版。专业版限制在于只能开3个账号。但是专业版有全局搜索功能。<Br/>  
2. 关于docker-compose.yml文件，有2点要注意。
   1. ports:下的 - "80:80"，这个的意思是说Seafile内部使用的80端口，但是因为这个端口经常被其他程序所使用，所以你可能<Br/>  
&nbsp;&nbsp;需要把它转换为其他的端口，比如我想用2323，就这样去改，"2323:80"。
   2. `- /opt/seafile-data:/shared` 和 `/opt/seafile-mysql/db:/var/lib/mysql`  这个设置，<Br/>
&nbsp;&nbsp;需要把/opt/seafile-data这个替换为你设置的Seafile data的存储位置。我用的我之前的一台旧电脑的数据盘，应该是Fat32的，在Omv上开头路径应是/Srv开头的。<Br/></p>
  
### 3 安装Plex
可以参考以下官网链接：<Br/>
<a href="https://linux.cn/article-5932-1.html" target="_blank">如何安装家庭媒体中心 Plex Media Server</a>(这种方法为命令行方式)<Br/>
<a href="https://www.kanzhun.com/jiaocheng/522729.html" target="_blank">OMV搭建系列教程[9] – 安装Plex流媒体服务器</a>(这种方法为Omv 插件方式)<Br/>

<p>此处需要特别注意两点<Br/>
1. 因为我的OMV-Extras插件中心，不知道为何找不到Plex支持，也缺少了很多其他服务支持，最后采用的是命令行方式安装的Plex。<Br/>  
&nbsp;&nbsp;其中用__wget https://downloads.plex.tv/plex-media-server/0.9.12.3.1173-937aac3/plexmediaserver_0.9.12.3.1173-937aac3_amd64.deb__ 先下载的时候，<Br/>
&nbsp;&nbsp;因为网速太慢，我直接通过浏览器下载完安装包，然后将固件通过Winscp上传到安装路径，执行下面的命令__dpkg -i plexmediaserver_0.9.12.3.1173-937aac3_amd64.deb__来开始debian包的安装。<Br/>
2. 安装过程较简单，但是不知道是不是因为电脑比较老旧了，所以影片加载很慢，只能播放大小很小的片段，超过1G的连预览都看不了。<Br/></p>  


### 4 安装OpenWrt
可以参考以下官网链接<Br/>
<a href="https://blog.csdn.net/qingwufeiyang12346/article/details/88753985" target="_blank">在PHICOMM K2路由器中装入OpenWrt操作系统</a><Br/>
<a href="https://openwrt.org/toh/views/toh_fwdownload" target="_blank">PHICOMM Table of Hardware: Firmware downloads</a>(下载对应硬件的固件)<Br/>

<p>1. Step1 先用Breed Web恢复控制台把固件刷入路由器。<Br/>  
2. Step2 我用的是斐讯K2路由器，路由器默认IP为192.168.2.1，刷机成功后，路由器默认IP为192.168.1.1，将本机IP设置为192.168.1.X段。<Br/></p>

### 5 全局注意事项
1. 注意事项：因为我安装OMV在前，安装OpenWrt在后，之前安装OMV的时候选择的是静态IP，192.168.18.X段的，导致OpenWrt安装好之后，因为是不同网段，找不到OMV的机器了。<Br/>
&nbsp;&nbsp;这个时候，我采取的方法是，把联通的旧路由器再接上去（从桥接改为拨号方式），用PC登录OMV界面改成了动态IP。然后再把路由器换为装好OpenWrt的斐讯，OMV就可以正常识别了。<Br/>
2. 注意事项：在OpenWrt中做端口映射的时候，需要把内部端口号映射成别的号（尽量是大于10000的号，一般网络嗅探端口号截止于10000以内），目的是为了防止别人直接用嗅探<Br/>
&nbsp;&nbsp;工具扫描开放的机器端口号,这个时候，就需要把之前Seafile中设置过的__2323:80__中的2323映射成外部访问端口号例如11111。<Br/>
