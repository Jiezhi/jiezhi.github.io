title: NOIP 2004 合并果子 fruit
tags:
  - noip
id: 197
categories:
  - Pascal
date: 2009-11-17 23:15:02
premalink: android_edittext_no_key
thumbnailImage: http://qn-jiezhi.nospider.net/pet-761a1e28219e08e1f1c5b491fd7aa3b3.jpg-thumb1
metaAlignment: center
coverImage: http://qn-jiezhi.nospider.net/10052206,2560,1600.jpg-cover
coverCaption: "Android"
coverMeta: out
photos:
comments: true
---

<!--more-->
首先快排是要的，下面就是计算最小的体力耗费值了，其中我用到了冒泡排序，具体见下：


```
program fruit;

var a:array [0..10001]of longint;

       i,j,k,n:integer;

          t,s:longint;

          flag:boolean;

procedure fastsort(l,r:integer);

var lx,rx,pi:integer;

begin

lx:=l;rx:=r;pi:=a[l];

while lx<rx do begin

    while a[rx]>pi do dec(rx);

    if lx<rx then begin

           t:=a[rx];a[rx]:=a[lx];a[lx]:=t;inc(lx);

    end;

    while a[lx]<pi do inc(lx);

    if lx<rx then begin

           t:=a[rx];a[rx]:=a[lx];a[lx]:=t;dec(rx);

    end;

end;

       if l<lx-1 then fastsort(l,lx-1);

       if r>rx+1 then fastsort(rx+1,r);

end;

begin

assign(input,'fruit.in');reset(input);

assign(output,'fruit.ans');rewrite(output);

readln(n);

for i:=1 to n do read(a[i]);

fastsort(1,n);

s:=0;a[0]:=0;

for i:=1 to n do begin

       s:=s+a[i]+a[i-1];a[i]:=a[i-1]+a[i];

              for k:=i to n-1 do

                     if a[k]>a[k+1] then begin

                            t:=a[k];a[k]:=a[k+1];a[k+1]:=t;

                     end;

end;

       dec(s,a[1]);

       writeln(s);

close(input);close(output);

end.
```

其中还有许多可以优化的地方，如：可以用t:=a[i];a[i]:=a[n];a[n]:=t;来减少后面的排序次数，又数组已是有序的了故还可在冒泡排序上优化，可置一布尔型变量flag来提前结束排序.

但十个测试点都过了就不做了.
