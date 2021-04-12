title: noip 2006 能量项链 energy
tags:
  - noip
  - pascal
id: 207
categories:
  - Pascal
date: 2009-11-17 23:26:29
premalink: android_edittext_no_key
thumbnailImage: http://qn-jiezhi.nospider.net/10126010,2560,1600.jpg-thumb1
metaAlignment: center
coverImage: http://qn-jiezhi.nospider.net/10052206,2560,1600.jpg-cover
coverCaption: "Android"
coverMeta: out
photos:
comments: true
---
<!--more-->
能量项链

```
rogram energy;

{20:02 }

var

    a1,a2:array[1..200]of integer;

    q:array[1..200,1..200]of longint;

         i,j,n,k,m,p:integer;

         best:longint;

begin

assign(input,'energy.in');reset(input);

assign(output,'energy.ans');rewrite(output);

readln(n);

for i:=1 to n do read(a1[i]);

readln;

for i:=1 to n-1 do a2[i]:=a1[i+1];

a2[n]:=a1[1];

m:=n*2-1;

for i:=n+1 to m do begin

         a1[i]:=a1[i-n];a2[i]:=a2[i-n]

end;

//for i:=1 to m do writeln(a1[i],' ',a2[i],' ');

fillchar(q,sizeof(q),0);

for p:=1 to n-1 do

         for i:=1 to m-1 do

                   begin

                 j:=i+p;

                 if j>m then break;

                            for k:=i to j-1 do begin

                            if q[i,j]<q[i,k]+q[k+1,j]+a1[i]*a2[k]*a2[j]

                               then q[i,j]:=q[i,k]+q[k+1,j]+a1[i]*a2[k]*a2[j];

                            end;

               end;

best:=0;

for i:=1 to n do

         if best<q[i,i+n-1]then best:=q[i,i+n-1];

writeln(best);

close(input);close(output);

end.
```