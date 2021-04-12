title: Android读取内存信息
date: 2015-06-01 11:17
tags:
- android
- memory
categories:
- Android
premalink: android_edittext_no_key
thumbnailImage: http://qn-jiezhi.nospider.net/android1119002570721cd2c8o.jpg-thumb1
metaAlignment: center
photos:
comments: true

---
通过adb命令获取设备内存信息
<!--more-->
在adb shell的情况下执行命令：
```bash
shell@maguro:/ $ cat /proc/meminfo  
MemTotal:         710960 kB  
MemFree:          136148 kB  
Buffers:            3400 kB  
Cached:           228768 kB  
SwapCached:            0 kB  
Active:           266960 kB  
Inactive:         158372 kB  
Active(anon):     193608 kB  
Inactive(anon):     1552 kB  
Active(file):      73352 kB  
Inactive(file):   156820 kB  
Unevictable:         412 kB  
Mlocked:               0 kB  
HighTotal:        598016 kB  
HighFree:          85216 kB  
LowTotal:         112944 kB  
LowFree:           50932 kB  
SwapTotal:             0 kB  
SwapFree:              0 kB  
Dirty:                 0 kB  
Writeback:             0 kB  
AnonPages:        193552 kB  
Mapped:           164744 kB  
Shmem:              1584 kB  
Slab:              19668 kB  
SReclaimable:       7920 kB  
SUnreclaim:        11748 kB  
KernelStack:        4208 kB  
PageTables:         6540 kB  
NFS_Unstable:          0 kB  
Bounce:                0 kB  
WritebackTmp:          0 kB  
CommitLimit:      355480 kB  
Committed_AS:    3875984 kB  
VmallocTotal:     778240 kB  
VmallocUsed:       55432 kB  
VmallocChunk:     686020 kB  
```
dmupsys:
```
shell@maguro:/ $ dumpsys meminfo
Applications Memory Usage (kB):
Uptime: 4699227 Realtime: 6241493

Total PSS by process:
    57336 kB: com.android.launcher (pid 625)
    55811 kB: system (pid 379)
    33484 kB: com.android.systemui (pid 463)
    23032 kB: com.baidu.appsearch (pid 941)
    17931 kB: com.android.phasebeam (pid 547)
    17042 kB: com.lbe.security:service (pid 910)
    16522 kB: com.baidu.appsearch:bdservice_v1 (pid 1239)
    11232 kB: com.tencent.mobileqq:MSF (pid 2159)
    10447 kB: com.baidu.BaiduMap:bdservice_v1 (pid 1217)
     8220 kB: com.android.phone (pid 600)
     7185 kB: com.baidu.BaiduMap:MapCoreService (pid 2223)
     6628 kB: com.tencent.mobileqq (pid 2176)
     5703 kB: com.android.inputmethod.latin (pid 574)
     5115 kB: android.process.media (pid 2673)
     4694 kB: com.android.nfc (pid 611)
     3106 kB: com.android.nfc:handover (pid 665)
     2769 kB: com.android.musicfx (pid 1736)
     2763 kB: com.android.smspush (pid 705)
        0 kB: com.qihoo.permmgr (pid 751)
        0 kB: com.baidu.BaiduMap (pid 1405)

Total PSS by OOM adjustment:
    55811 kB: System
               55811 kB: system (pid 379)
    46398 kB: Persistent
               33484 kB: com.android.systemui (pid 463)
                8220 kB: com.android.phone (pid 600)
                4694 kB: com.android.nfc (pid 611)
    57336 kB: Foreground
               57336 kB: com.android.launcher (pid 625)
    23800 kB: Visible
               17931 kB: com.android.phasebeam (pid 547)
                3106 kB: com.android.nfc:handover (pid 665)
                2763 kB: com.android.smspush (pid 705)
    62299 kB: Perceptible
               23032 kB: com.baidu.appsearch (pid 941)
               17042 kB: com.lbe.security:service (pid 910)
               16522 kB: com.baidu.appsearch:bdservice_v1 (pid 1239)
                5703 kB: com.android.inputmethod.latin (pid 574)
    15562 kB: A Services
               10447 kB: com.baidu.BaiduMap:bdservice_v1 (pid 1217)
                5115 kB: android.process.media (pid 2673)
    27814 kB: Background
               11232 kB: com.tencent.mobileqq:MSF (pid 2159)
                7185 kB: com.baidu.BaiduMap:MapCoreService (pid 2223)
                6628 kB: com.tencent.mobileqq (pid 2176)
                2769 kB: com.android.musicfx (pid 1736)
                   0 kB: com.qihoo.permmgr (pid 751)
                   0 kB: com.baidu.BaiduMap (pid 1405)

Total PSS by category:
   109517 kB: Dalvik
    75944 kB: Other dev
    39041 kB: Unknown
    31896 kB: .dex mmap
    24323 kB: .so mmap
     5692 kB: .apk mmap
     1360 kB: .ttf mmap
      655 kB: Other mmap
      540 kB: Stack
       28 kB: Ashmem
       20 kB: .jar mmap
        4 kB: Cursor
        0 kB: Native

Total PSS: 289020 kB
      KSM: 0 kB saved from shared 0 kB
           0 kB unshared; 0 kB volatile
```

指定pid信息：
```
shell@maguro:/ $ dumpsys meminfo 625
Applications Memory Usage (kB):
Uptime: 4872829 Realtime: 6415095

** MEMINFO in pid 625 [com.android.launcher] **
                         Shared  Private     Heap     Heap     Heap
                   Pss    Dirty    Dirty     Size    Alloc     Free
                ------   ------   ------   ------   ------   ------
       Native        0        0        0    16752     8417     1926
       Dalvik    15581    12884    14852    21608    19175     2433
        Stack       40        8       40
       Cursor        0        0        0
       Ashmem        0        0        0
    Other dev    30451     1412    18692
     .so mmap     1890     2652      884
    .jar mmap        0        0        0
    .apk mmap     1459        0        0
    .ttf mmap      470        0        0
    .dex mmap     1122     2832      104
   Other mmap       23        8        8
      Unknown     6305      432     6296
        TOTAL    57341    20228    40876    38360    27592     4359

 Objects
               Views:      261         ViewRootImpl:        1
         AppContexts:        9           Activities:        1
              Assets:        4        AssetManagers:        4
       Local Binders:       14        Proxy Binders:       29
    Death Recipients:        0
     OpenSSL Sockets:        0

 SQL
         MEMORY_USED:      547
  PAGECACHE_OVERFLOW:      393          MALLOC_SIZE:       62

 DATABASES
      pgsz     dbsz   Lookaside(b)          cache  Dbname
         4       68             79        37/20/6  /data/user/0/com.android.launcher/databases/launcher.db
         4      312             23       251/16/2  /data/user/0/com.android.launcher/cache/widgetpreviews.db
```