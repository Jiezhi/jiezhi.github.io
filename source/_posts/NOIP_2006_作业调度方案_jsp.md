title: noip 2006 作业调度方案 jsp
tags:
  - noip
  - pascal
id: 211
categories:
  - Pascal
date: 2009-11-17 23:29:29
premalink: android_edittext_no_key
thumbnailImage: http://qn-jiezhi.nospider.net/10126010,2560,1600.jpg-thumb1
metaAlignment: center
coverImage: http://qn-jiezhi.nospider.net/10052206,2560,1600.jpg-cover
coverCaption: "Android"
coverMeta: out
photos:
comments: true
---
作业调度方案

本来想用记录p=record u,b,e:integer;   end; （u为第几道工序，b为开始时间，e为结束时间）

来实现的，直接排还是挺方便的，但有些地方不好实现就没做下去……
<!--more-->
直接排：
```
for I:=1 to m*n do begin

j:=1;

while p[I,j].e<>0 do inc(j);{p[I,j].e=0意味着p[I,j]已被安排过了，下一道工序}

p[I,j].b:=p[I-1,j].e;{在同一个机器上的上一道工序结束时间}

p[I,j].e:=p[I,j].b+t[I,j];{t[I,j]为该道工序所占时间}

end;
```
```
program jsp;

const nn=20;

var f:array[1..nn,0..nn*nn]of boolean;//each machine condition;

    t:array[1..nn,1..nn]of integer;//cover time;

       e:array[1..nn,0..nn]of integer;//end time;

       a:array[1..nn*nn]of integer;//following;

       b:array[1..nn,1..nn]of integer;//belong to machine

       g:array[1..nn*nn]of integer;//dealing with;

       flag:boolean;

       i,j,m,n,k,best,nt,nm:integer;

begin

assign(input,&#39;jsp.in&#39;);reset(input);

assign(output,&#39;&#39;);rewrite(output);

readln(m,n);

fillchar(a,sizeof(a),0);

fillchar(e,sizeof(e),0);

fillchar(f,sizeof(f),true);

fillchar(g,sizeof(g),0);

for i:=1 to m*n do read(a[i]);readln;

for i:=1 to n do begin

       for j:=1 to m do

              read(b[i,j]);

end;

for i:=1 to n do begin

       for j:=1 to m do

              read(t[i,j]);

       readln;

end;

for i:=1 to m*n do begin

       inc(g[a[i]]);

      nt:=t[a[i],g[a[i]]];

      nm:=b[a[i],g[a[i]]];{nt：needtime；nm：machine}

      for j:=e[a[i],g[a[i]]-1]to 400 do begin{从该工件的上一个工序结束时间开始搜索}

              flag:=true;

             for k:=j to j+nt do

                     if k&amp;lt;=400 then

                            flag:=flag and f[nm,k];

              if flag then begin{如果搜索成功}

                     e[a[i],g[a[i]]]:=j+nt;{记下结束时间}

                    for k:=j+1 to j+nt-1 do f[nm,k]:=false;

                     break;{减少搜索}

              end;

       end;

end;

       best:=0;

       for i:=1 to n do {从每道工序入手，但如果从每个机器入手呢？}

              if best&amp;lt;e[i,m]then best:=e[i,m];

       writeln(best);

       close(input);close(output);

end.
```