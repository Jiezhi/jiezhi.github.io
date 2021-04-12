title: noip 2006 金明的预算方案 budget
tags:
  - noip
  - pascal
id: 205
categories:
  - Pascal
date: 2009-11-17 23:25:13
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
金明的预算方案

```
program budget;
 
{20:03 }
 
type node=record
 
            u:integer;
 
       v,p:array[0..2]of integer;
 
     end;
 
var w:array[1..60]of node;
 
    f:array[0..60,0..2000]of longint;
 
    vx,px,qx,g:array[1..60]of integer;
 
    m,n,i,k,j:integer;
 
begin
 
assign(input,'budget.in');reset(input);
 
assign(output,'budget.out');rewrite(output);
 
readln(n,m);
 
k:=0;
 
for i:=1 to m do begin
 
      readln(px[i],vx[i],qx[i]);
 
      if qx[i]=0 then begin
 
                   inc(k);g[i]:=k;
 
                   with w[k] do begin
 
                   u:=0;
 
                   v[0]:=vx[i];p[0]:=px[i];
 
                   for j:=1 to 2 do begin
 
                            v[j]:=0;p[j]:=0;
 
                   end;
 
                   end;
 
         end;
 
    end;
 
for i:=1 to m do begin
 
         if qx[i]<>0 then
 
         with w[g[qx[i]]] do begin
 
                   inc(u);v[u]:=vx[i];p[u]:=px[i];
 
         end;
 
end;
 
for i:=1 to k do
 
         with w[i] do begin
 
                   write('w[',i,']:');
 
         for j:=0 to 2 do write('(',v[j],',',p[j],') ');
 
                   writeln;
 
end;
 
         
 
fillchar(f,sizeof(f),0);
 
         
 
for i:=1 to k do
 
         for j:=1 to n do
 
                   with w[i] do begin
 
             f[i,j]:=f[i-1,j];
 
                   
 
             if (f[i,j]< (f[i-1,j-p[0]]+p[0]*v[0]))and(j>=p[0]) then
 
                            f[i,j]:=f[i-1,j-p[0]]+p[0]*v[0];
 
                   
 
                   if (f[i,j]< (f[i-1,j-p[0]-p[1]]+p[0]*v[0]+v[1]*p[1]))and(j>=p[0]+p[1]) then
 
                            f[i,j]:=f[i-1,j-p[0]-p[1]]+p[0]*v[0]+v[1]*p[1];
 
                   
 
                   if (f[i,j]< (f[i-1,j-p[0]-p[2]]+p[0]*v[0]+p[2]*v[2]))and(j>=p[0]+p[2]) then
 
                            f[i,j]:=f[i-1,j-p[0]-p[2]]+p[0]*v[0]+p[2]*v[2];
 
                   
 
                   if (f[i,j]< (f[i-1,j-p[0]-p[1]-p[2]]+p[0]*v[0]+v[1]*p[1]+p[2]*v[2]))and(j>=p[0]+p[1]+p[2]) then
 
                            f[i,j]:=f[i-1,j-p[0]-p[1]-p[2]]+p[0]*v[0]+v[1]*p[1]+p[2]*v[2];
 
                   writeln('f[',i,',',j,']:',f[i,j]);
 
         end;
 
         writeln(f[k,n]);
 
         close(input);close(output);
 
end.

```
