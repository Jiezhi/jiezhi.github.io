title: 用dialog给Bash穿上衣服
tags:
  - bash
  - dialog
id: 392
categories:
  - Bash
date: 2015-05-26 02:56:08
premalink: android_edittext_no_key
thumbnailImage: http://qn-jiezhi.nospider.net/Untitled.jpg-thumb1
metaAlignment: center
coverImage: http://qn-jiezhi.nospider.net/10052206,2560,1600.jpg-cover
coverCaption: "Android"
coverMeta: out
photos:
comments: true
---
bash下的dialog窗口的实现。
<!--more-->
![QQ20150526-2@2x](http://jiezhiblog.com/wp-content/uploads/2015/05/QQ20150526-2@2x-252x300.png)](http://jiezhiblog.com/wp-content/uploads/2015/05/QQ20150526-2@2x.png)

![QQ20150526-1@2x](http://jiezhiblog.com/wp-content/uploads/2015/05/QQ20150526-1@2x-300x189.png) ](http://jiezhiblog.com/wp-content/uploads/2015/05/QQ20150526-1@2x.png)

```
#!/bin/bash

temp=$(mktemp -t test.XXXXXX)
temp2=$(mktemp -t test2.XXXXXX)

function diskspace {
    df -h > $temp
    dialog --textbox $temp 20 60
}

function whoseon {
    who > $temp
    dialog --textbox $temp 20 50
}

function memusage {
    cat /proc/meminfo > $temp
    diaolog --textbox $temp 20 50
}

while [ 1 ]
do
    dialog --menu "Sys Admin Menu" 20 30 10 1 "Display disk space" 2 "Display users" 3 "Dispaly memory usage" 0 "Exi   2> $temp2
if [ $? -eq 1 ]
then
    break
fi

selection=$(cat $temp2)
case $selection in
1)
    diskspace ;;
2)
    whoseon ;;
3)
    memusage ;;
0)
    break ;;
*)
    dialog --msgbox "Sorry, invalid selection" 10 30
esac
done
rm -f $temp 2> /dev/null
rm -f $temp2 2> /dev/null
```
