title: 利用国内镜像源下载、编译Android源码
tags:
- android
- aosp
- 源码
id: 187
categories:
- Android
date: 2015-04-15 21:18:31
premalink: android_edittext_no_key
thumbnailImage: http://qn-jiezhi.nospider.net/10126010,2560,1600.jpg-thumb1
metaAlignment: center
coverImage: http://qn-jiezhi.nospider.net/10052206,2560,1600.jpg-cover
coverCaption: "Android"
coverMeta: out
photos:
comments: true

------

在国内下载Android可是不太容易，不过从Google断断续续地下载了几天源码后发现清华大学有个TUNA镜像源可以下载Android源码，甚是方便。

参考网站：

http://source.android.com/index.html

https://aosp.tuna.tsinghua.edu.cn/

一.环境准备：

现在Android源码的下载和编译在Linux和Mac OS上都可以了，但Mac OS上设置略微复杂点，所以我选择了Ubuntu 14.04 64位的虚拟机。（硬盘建议50G以上，编译的时候给虚拟机加大CPU和内存。）

编译Gingerbread (2.3.x) 及其以上的源码需要64位的系统，以下的可以在32位系统上编译。

1.Java下载和配置

Java 7：适用最新版的源码：

```
$ sudo apt-get update
$ sudo apt-get install openjdk-7-jdk
```

如果系统上有多个Java版本，可以设置默认的：

```
$ sudo update-alternatives --config java
$ sudo update-alternatives --config javac
```

Java 6: 适用于Gingerbread（2.3）~ KitKat（4.4）

Java 5:适用于 Cupcake（1.5）~ Froyo（2.2）

如果Java安装失败可到Java官网下载后自行安装，略去不表。

2.其它依赖包：
​{% codeblock %}
$ sudo apt-get install bison g++-multilib git gperf libxml2-utils make zlib1g-dev:i386 zip
{% endcodeblock %}

但在编译过程中发现还需要两个包，所以也提前安装好吧：

{% codeblock %}
$ sudo apt-get install flex libswitch-perl
{% endcodeblock %}

如果是Ubuntu 12.04：

{% codeblock %}
$ sudo apt-get install git gnupg flex bison gperf build-essential \
  zip curl libc6-dev libncurses5-dev:i386 x11proto-core-dev \
  libx11-dev:i386 libreadline6-dev:i386 libgl1-mesa-glx:i386 \
  libgl1-mesa-dev g++-multilib mingw32 tofrodos \
  python-markdown libxml2-utils xsltproc zlib1g-dev:i386
$ sudo ln -s /usr/lib/i386-linux-gnu/mesa/libGL.so.1 /usr/lib/i386-linux-gnu/libGL.so
{% endcodeblock %}

如果是Ubuntu 10.04 -- 11.10：

{% codeblock %}
$ sudo apt-get install git gnupg flex bison gperf build-essential \
  zip curl zlib1g-dev libc6-dev lib32ncurses5-dev ia32-libs \
  x11proto-core-dev libx11-dev lib32readline5-dev lib32z-dev \
  libgl1-mesa-dev g++-multilib mingw32 tofrodos python-markdown \
  libxml2-utils xsltproc
{% endcodeblock %}

11.10：

{% codeblock %}
$ sudo apt-get install libx11-dev:i386
{% endcodeblock %}

10.04：

{% codeblock %}
$ sudo ln -s /usr/lib32/mesa/libGL.so.1 /usr/lib32/mesa/libGL.so
{% endcodeblock %}

二、下载源码

1.下载repo：

repo是Google基于Git推出的一款版本管理工具，用python写的。

先配置目录：

{% codeblock %}
$ mkdir ~/bin
$ PATH=~/bin:$PATH
{% endcodeblock %}

下载repo并赋予其可执行权限：

{% codeblock %}
$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
{% endcodeblock %}

当然由于众所周知的原因，我们往往无法把repo下到本地。这里提供一个我下载并修改好的下载链接：[http://jiezhiblog.com/wp-content/uploads/2015/04/repo1.txt](http://jiezhiblog.com/wp-content/uploads/2015/04/repo1.txt)

（wp的限制，只能把repo重命名为repo.txt，下载后改回repo即可）

下载放到~/bin目录下，并修改权限即可：

{% codeblock %}
chmod a+x repo
{% endcodeblock %}

2.初始化repo

进入放置源码的目录，如：

{% codeblock %}
$ mkdir ~/android/android 4.3_r1
$ cd ~/android/android 4.3_r1
{% endcodeblock %}

关键的来了，如果想体验好点，建议国内的从清华大学的镜像源下载：

{% codeblock %}
$ repo init -u git://aosp.tuna.tsinghua.edu.cn/android/platform/manifest
{% endcodeblock %}

或者指定要下载的分支：

{% codeblock %}
$ repo init -u git://aosp.tuna.tsinghua.edu.cn/android/platform/manifest -b android-4.3_r1
{% endcodeblock %}

完成后即可和服务器同步了：

{% codeblock %}
$ repo sync
{% endcodeblock %}

该服务器限制了每个IP并发数，也就是你可以使用：

{% codeblock %}
$ repo sync -j4
{% endcodeblock %}

设置并发数为4.，给别人留有余地。

现在应该在从服务器下载源码了，但是如果中途有中断了，继续执行**repo sync**即可。

**如何让repo自动在断开后自动下载：**

新建**autorepo.sh**:

{% codeblock %}
#!/bin/sh
repo sync
while [ $? -ne 0 ]
do
repo sync
done
{% endcodeblock %}

然后执行即可：

{% codeblock %}
$ sh autorepo.sh
{% endcodeblock %}

如果你之前已经下载了部分AOSP的代码的话，切换到TUNA服务器也很方便，如官网所示：

> 如果你之前已经通过某种途径获得了 AOSP 的源码(或者你只是 init 这一步完成后)， 你希望以后通过 TUNA 同步 AOSP 部分的代码，只需要将 .repo/manifest.xml 把其中的 aosp 这个 remote 的 fetch 从 https://android.googlesource.com 改为 git://aosp.tuna.tsinghua.edu.cn/android/

```diff<manifest>    <remote  name="aosp"-           fetch="https://android.googlesource.com"+           fetch="git://aosp.tuna.tsinghua.edu.cn/android/"            review="android-review.googlesource.com" />    <remote  name="github"```

**这个方法也可以用来在同步 Cyanogenmod 代码的时候从 TUNA 同步部分代码**

三、编译：

当源码下载后，可以看到目录里多出很多源码文件夹：

{% codeblock %}
[aosp@myserver android4.1_r1]$ ls -hl
total 100K
drwxrwxr-x   3 aosp aosp 4.0K Apr  7 11:27 abi
drwxrwxr-x   9 aosp aosp 4.0K Apr  7 11:27 bionic
drwxrwxr-x   5 aosp aosp 4.0K Apr  7 11:27 bootable
drwxrwxr-x   7 aosp aosp 4.0K Apr  7 11:27 build
drwxrwxr-x  11 aosp aosp 4.0K Apr  7 11:27 cts
drwxrwxr-x  18 aosp aosp 4.0K Apr  7 11:27 dalvik
drwxrwxr-x  19 aosp aosp 4.0K Apr  7 11:27 development
drwxrwxr-x  10 aosp aosp 4.0K Apr  7 11:27 device
drwxrwxr-x   3 aosp aosp 4.0K Apr  7 11:27 docs
drwxrwxr-x 144 aosp aosp 4.0K Apr  7 11:29 external
drwxrwxr-x  14 aosp aosp 4.0K Apr  7 11:32 frameworks
drwxrwxr-x  10 aosp aosp 4.0K Apr  7 11:32 gdk
drwxrwxr-x  10 aosp aosp 4.0K Apr  7 11:32 hardware
drwxrwxr-x  11 aosp aosp 4.0K Apr  7 11:32 libcore
drwxrwxr-x   4 aosp aosp 4.0K Apr  7 11:32 libnativehelper
-r--r--r--   1 aosp aosp   87 Apr  7 11:27 Makefile
drwxrwxr-x   8 aosp aosp 4.0K Apr  7 11:32 ndk
drwxrwxr-x   4 aosp aosp 4.0K Apr  7 14:28 out
drwxrwxr-x   7 aosp aosp 4.0K Apr  7 11:33 packages
drwxrwxr-x   4 aosp aosp 4.0K Apr  7 11:33 pdk
drwxrwxr-x  12 aosp aosp 4.0K Apr  7 11:33 prebuilt
drwxrwxr-x   9 aosp aosp 4.0K Apr  7 11:34 prebuilts
drwxrwxr-x  48 aosp aosp 4.0K Apr  7 11:34 sdk
drwxrwxr-x   9 aosp aosp 4.0K Apr  7 11:34 system
{% endcodeblock %}

如果你准备在模拟器里运行，则按照如下步骤编译即可，如果你想刷到手机上则还有一些任务要做（见第四步）。



0.其中由于配置问题，我的代码是放在服务器上编译的:

由于.repo这个隐藏文件夹里的文件占用空间很大，所以在压缩的时候将其排除：

{% codeblock %}
$ tar -zcvf android4.3_r1.tar.gz android4.3_r1/ --exclude .repo
{% endcodeblock %}

然后利用scp命令将压缩好的文件上传到服务器：

{% codeblock %}
$ scp android4.3_r1.tar.gz username@host:/home/android/
{% endcodeblock %}

其中username和host是你用户名和服务器地址。

解压：

{% codeblock %}
$ tar -zxvf android4.3_r1.tar.gz /
{% endcodeblock %}

1.初始化：

{% codeblock %}
$ source build/envsetup.sh
{% endcodeblock %}

2.使用**lunch**命令选择编译目标，如：

{% codeblock %}
$ lunch aosp_arm-eng
{% endcodeblock %}

或者直接lunch，会让你选择编译目标的。

其中参数说明：

| BUILD NAME | DEVICE | NOTES |
|--------|--------|--------|
|     aosp_arm|	ARM emulator	|包括所有语言、APP和输入法的配置|
|aosp_maguro	|maguro|	运行在Galaxy Nexus GSM/HSPA+ ("maguro")上|
|aosp_panda|	panda	| 运行在 PandaBoard ("panda")上   |

|BUILDTYPE|USE|
|---------|---|
|user	|limited access; suited for production（有权限限制，适合产品级）|
|userdebug	|preferred for debugging（适合调试）
|eng	|development configuration with additional debugging tools（有额外的调试工具）|

4.3.编译


{% codeblock %}
$ make -j4
{% endcodeblock %}
**-jN**表示用N个线程来编译，如果你是配置是2CPU，每个CPU有4核，每核可跑俩线程，那么你可以make -j16乃至-j32，这样速度将大大加快。.

4.成功

在android4.3_r1/out/target/product/generic目录下可以看到如下文件：

{% codeblock %}
[aosp@myserver generic]$ ls -lh
total 205M
-rw-rw-r--  1 aosp aosp    7 Apr  7 14:49 android-info.txt
-rw-rw-r--  1 aosp aosp  25K Apr  7 14:48 clean_steps.mk
drwxrwxr-x  4 aosp aosp 4.0K Apr  7 15:19 data
drwxrwxr-x  3 aosp aosp 4.0K Apr  7 15:18 dex_bootjars
-rw-rw-r--  1 aosp aosp  47K Apr  7 15:27 installed-files.txt
drwxrwxr-x 14 aosp aosp 4.0K Apr  7 15:27 obj
-rw-rw-r--  1 aosp aosp  557 Apr  7 14:48 previous_build_config.mk
-rw-rw-r--  1 aosp aosp 163K Apr  7 15:18 ramdisk.img
drwxrwxr-x  8 aosp aosp 4.0K Apr  7 15:18 root
drwxrwxr-x  5 aosp aosp 4.0K Apr  7 15:18 symbols
drwxrwxr-x 12 aosp aosp 4.0K Apr  7 15:27 system
-rw-------  1 aosp aosp 204M Apr  7 15:27 system.img
drwxrwxr-x  3 aosp aosp 4.0K Apr  7 15:05 test
-rw-------  1 aosp aosp  99K Apr  7 15:19 userdata.img
{% endcodeblock %}

四、刷机

1.模拟器的话，其实直接运行**emulator**即可运行：

由于不涉及内核，我的做法是把ramdisk.img、system.img和userdata.img复制到sdk/system-images/android-18/default/armeabi-v7a/目录下替换掉原来的文件。（可以把原来的先备份）
然后新建对应的API虚拟机，运行即可。


2.真机

真机我是用Google三太子Galaxy Nexus&nbsp;[maguro] (GSM/HSPA+)做的实验，毕竟亲儿子，驱动方面都很好配置。

a.在第三步编译之前，先把驱动配置好：


{% codeblock %}
$ cd ~/android/anddroid-4.3_r1

$ wget https://dl.google.com/dl/android/aosp/broadcom-maguro-jwr66y-5fa7715b.tgz

$ tar -zxvf broadcom-maguro-jwr66y-5fa7715b.tgz

$ ./extract-broadcom-maguro.sh # (view the license and then type "I ACCEPT")

...

$ wget https://dl.google.com/dl/android/aosp/imgtec-maguro-jwr66y-b0a4a1ef.tgz

$ tar -zxvf imgtec-maguro-jwr66y-b0a4a1ef.tgz

$ ./extract-imgtec-maguro.sh # (view the license and then type "I ACCEPT")

...

$ wget https://dl.google.com/dl/android/aosp/invensense-maguro-jwr66y-e0d2e531.tgz

$ tar -zxvf invensense-maguro-jwr66y-e0d2e531.tgz

$ ./extract-invensense-maguro.sh # (view the license and then type "I ACCEPT")

...

$ wget https://dl.google.com/dl/android/aosp/nxp-maguro-jwr66y-d8ac2804.tgz

$ tar -zxvf nxp-maguro-jwr66y-d8ac2804.tgz

$ ./extract-nxp-maguro.sh # (view the license and then type "I ACCEPT")

...

$ wget https://dl.google.com/dl/android/aosp/samsung-maguro-jwr66y-fb8f93b6.tgz

$ tar -zxvf samsung-maguro-jwr66y-fb8f93b6.tgz

$ ./extract-samsung-maguro.sh # (view the license and then type "I ACCEPT")

...

$ wget https://dl.google.com/dl/android/aosp/widevine-maguro-jwr66y-c49927ce.tgz

$ tar -zxvf widevine-maguro-jwr66y-c49927ce.tgz

$ ./extract-widevine-maguro.sh # (view the license and then type "I ACCEPT")
{% endcodeblock %}

然后按照第三步编译即可。

b.连接手机，打开USB调试，进入bootloader模式：

{% codeblock %}
$ adb reboot bootloader
{% endcodeblock %}

如果bootloader被锁住的话，先解锁：
{% codeblock %}
$ fastboot oem unlock
{% endcodeblock %}

然后进入system.img等文件的目录：

{% codeblock %}
$ fastboot flash boot boot.img

$ fastboot flash system system.img

$ fastboot flash userdata userdata.img
{% endcodeblock %}

然后重启即可。
