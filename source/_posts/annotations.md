title: 使用注解改进Android代码
author: Jiezhi.G
email: Jiezhi.G@gmail.com
premalink: fix_gravatar
thumbnailImage: >-
  http://qn-jiezhi.nospider.net/pet-0e24cfd3754db9af5e2f7119f1160964.jpg-thumb1
metaAlignment: center
comments: true
date: 2017-05-07 20:38:31
url:
tags:
categories:
photos:
---
注解有很多用途。 但是，在这里我们将讨论如何使用注解来改进我们的android编码。
<!--more-->
![](https://cdn-images-1.medium.com/max/800/1*gxjdLI-WsXSPWPvAyBAMaQ.png)
## 注解即元数据
而元数据是提供有关其他数据的信息的一组数据。

注解有很多用途。 但是，在这里我们将讨论如何使用注解来改进我们的android编码。

官方Android已经提供了支持注解，你可以添加如下依赖关系来导入注解：

> compile ‘com.android.support:support-annotations:x.x.x’

每个人都想成为一个好的程序员，每天我也在努力地提高自己。

> 任何人都可以编写计算机可以理解的代码，而好的程序员编写人可以理解的代码。 - Martin Fowler

## 让我们一起探索下一些有用的注解

### 空类型注解
**@Nullable**和**@NonNull**注解用于检查给定的变量、参数甚至是返回值是否为空。

**@Nullable**：它表示一个变量、参数或返回值可以为空。

**@NonNUll**：它表示不能为空的变量、参数或返回值。

例如：
```java
@NonNull
public View getView(@Nullable String s1, @NonNull String s2) {
  // s1 可以为空
  // s2 不可以为空
  // 该方法必须返回一个不为空的view
}
```

所以当我们尝试这样调用该方法时：

```java
View view = getView("Amit", null);
```

在代码检测期间，Android Studio会警告你s2的值不能为空。

## 资源注解
我们都知道，Android对资源的引用（如drawable和string资源）都是传递int类型的，因此我们必须验证资源类型。假设代码中希望传入特定类型的资源（例如Drawables），这里可以传入引用类型的**int**值，但实际上却传入了另外类型的资源，例如R.string资源：

```java
public void setText(@StringRes int resId) {
  // resId 必须为string类型的id
  // resId 不能为常规的int值
}
```

所以，当你像如下方式调用该方法时：

```java
textView.setText(56);
```

在代码检测过程中，当传入非string资源的参数时，注解将会生成一个警告。

## 线程注解

线程注解检查方法是否在其期望的线程被调用。

支持的注解有：

**@MainThread**
**@UiThread**
**@WorkerThread**
**@BinderThread**
**@AnyThread**

如果你添加如下的注解：
```java
@WorkerThread
public void doSomething(){
  // 该方法一定要从worker线程调用
}
```

如果你从非worker线程调用该方法时，你将会得到一个警告。

## 值限定注解
有时候，我们必须对参数做一些约束，所以使用**@IntRange**，**@FloatRange**和**@Size**注解来验证传入参数的值。

当调用该方法的人可能会传递错误的值（超出指定的范围）时，该注解是非常有用的。

在下面的例子中，@IntRange注解确保传递的整数值必须在0到255之间。

```java
public void setAlpha(@IntRange(from=0,to=255) int alpha) {}
```

## 权限注解
使用**@RequiresPermission**注解来验证调用者的权限。

以下示例注解**setWallpaper()**方法来确保方法的调用者具有**permission.SET_WALLPAPERS**权限：

```java
@RequiresPermission(Manifest.permission.SET_WALLPAPER)
public abstract void setWallpaper(Bitmap bitmap) throws IOException;
```

如果在调用该方法时没有在manifest文件中添加需要的权限，代码检测器将会给你一个警告。
