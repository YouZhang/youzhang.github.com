---
layout: post
title: "Diff and Patch"
description: ""
category: SHU
tags: [diff,patch]
---
{% include JB/setup %}

> Note : Please replace "pygments : true" with "highlighter : pygments" in _config.yml

> 作为程序员，了解diff&patch命令是非常必要的。比如说我们发现某个项目有bug代码，而自己又没有svn的提交权限，那么此时最合适的解决方法就是用diff命令做一个补丁发给项目成员。项目成员通过patch命令可以立刻知道你的意图。有人会说直接传一个新文件不是更简单？不要忘了，一个patch文件尺寸更小传输更快，而且可以明显的看到都做了哪些修改。
> 保证当前目录是demo名录：

{% highlight bat linenos%}
mkdir demo
cd demo
{% endhighlight %}


先模拟一个项目目录old：

{% highlight bat linenos%}
mkdir -p old/a/b
vi old/a/b/foo.txt
old_line_1
old_line_2
{% endhighlight %}

- 假设我们发现项目old有bug代码，下面我们先拷贝一个新目录new，并在此修改bug代码：

{% highlight bat linenos%}
cp -r old new
vi new/a/b/foo.txt
new_line_1
new_line_2
{% endhighlight %}

- 保证old和new两个目录都在当前目录下，下面就可以使用diff命令了，不要使用绝对路径，而应该使用相对路径，至于原因，看到文章结尾你就清楚

{% highlight bat linenos%}
LC_ALL=C TZ=UTC0 diff -Naur old new > foo.patch
{% endhighlight %}

- 如果不在意字符集，时差等问题，也可以省略LC_ALL=C TZ=UTC0环境变量：
{% highlight bat linenos%}
diff -Naur old new > foo.patch
{% endhighlight %}

- 其中-Naur参数属于固定打法，不管是对一个文件，还是对一个目录，在使用这个参数基本就可以了。

大概浏览一下补丁文件：
{% highlight bat linenos%}
cat foo.patch
diff -Naur old/a/b/foo.txt new/a/b/foo.txt
{% endhighlight %}

{% highlight bat linenos%}
--- old/a/b/foo.txt     2009-12-07 20:40:07.000000000 +0800
+++ new/a/b/foo.txt     2009-12-07 20:41:51.000000000 +0800
@@ -1,2 +1,2 @@
-old_line_1
-old_line_2
+new_line_1
+new_line_2
{% endhighlight %}

- 加减号后面的内容是有用的内容，其他的内容是方便你查阅的相关信息内容，补丁制作完成。

> 此时的文件目录结构大概如下所示：

```
demo
|-- old
|   `-- a
|       `-- b
|           `-- foo.txt
|-- new
|   `-- a
|       `-- b
|           `-- foo.txt
`-- foo.patch
```

> 下面看看如何使用patch来应用补丁，要注意的是当前目录是demo，试试下面命令：

{% highlight bat linenos%}
patch -p0 < foo.patch
patching file old/a/b/foo.txt
{% endhighlight %}

**这里唯一需要说明的是p0的含义，因为在foo.patch补丁文件里的路径信息是这样的：**

--- old/a/b/foo.txt

```
p表示跳过几级目录，因为是在demo目录下使用的patch命令，old目录就在demo目录下，所以不必跳过任何目录，而应该使用old/a/b/foo.txt完整路径，所以此时使用的是p0。
```

> 查看一下目标文件，你会发现内容已经修改成新的了：


{% highlight bat linenos%}
cat old/a/b/foo.txt
new_line_1
new_line_2
{% endhighlight %}

- 此时如果你再次使用patch命令，系统会问你是否想还原：

{% highlight bat linenos%}
patch -p0 < foo.patch
patching file old/a/b/foo.txt
Reversed (or previously applied) patch detected!  Assume -R? [n] y
{% endhighlight %}

- 查看一下目标文件，你会发现内容已经还原成旧的了：

{% highlight bat linenos%}
cat old/a/b/foo.txt
old_line_1
old_line_2
{% endhighlight %}

- 如果你想严格指定是应用补丁可以使用下面命令（就是增加N参数）：

{% highlight bat linenos%}
patch -Np0 < foo.patch
{% endhighlight %}

- 如果你想严格指定是还原补丁可以使用下面命令（就是增加R参数）：

{% highlight bat linenos%}
patch -Rp0 < foo.patch
{% endhighlight %}

**注释：在本例中，每次应用补丁后，自己还原补丁，以备后用继续试验，我就不多说了。**

> 看到这里如果你对patch的p参数还不太清楚的话，接着往下看，我们改变一下当前路径：


```
cd old
```

- 此时就应该是p1，而不是p0了，引用foo.patch文件的路径也要相对变一下，因为当前目录已经是old了：

{% highlight bat linenos%}
patch -p1 < ../foo.patch
patching file a/b/foo.txt
{% endhighlight %}

- 因为此时我们是在old下使用patch命令，和a子目录平级，而补丁文件foo.patch里的路径声明是：

```
old/a/b/foo.txt
```
也就是说第一个斜线左边的old/部分已经没用了，这就是p1的含义！

继续往深度变换路径，依次测试使用p2,p3参数：

{% highlight bat linenos%}
cd a

patch -p2 < ../../foo.patch
patching file b/foo.txt

cd b

patch -p3 < ../../../foo.patch
patching file foo.txt 
{% endhighlight %}
在本例中，p3已经是最深目录了，此时可以省略p参数：
```
patch < ../../../foo.patch
patching file foo.txt
```
也就是说，不使用p参数的时候，patch命令会忽略任何目录，直接使用文件。

下面接着文章前面说的为什么使用diff命令时最好不要使用绝对路径，而应该使用相对路径？

答：如果你在使用diff的时候使用的是绝对路径，那么补丁文件里的文件路径信息会类似下面的样子：
```
/a/b/c/d/e/f/g/bar.txt
```
如此一来，当别人想应用你的补丁时，因为目录结构肯定有差异，所以就不得不费力判断到底使用p几。这样一来就很容易出错，相反，如果使用相对路径的话，大多数时候，p0或者p1就足够了，不易出错。

跟着本文的步骤操作一下，肯定能掌握diff&patch用法，基本上使用diff时就是"diff -Naur FROM TO"（FROM, TO为变量）这样的固定打法，然后在使用patch的时候，先看看补丁文件的大致内容，结合当前目录以确定需要跳过的目录数，然后套用"patch -pN < patch.file"（N为变量）即可。