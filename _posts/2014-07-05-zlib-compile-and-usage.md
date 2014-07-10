---
layout: post
title: "ZLib Compile and Usage"
description: "the auotoTool to calculate the max min average data rate"
category: SHU 
tags: [Zlib,SHU]
---
{% include JB/setup %}

> Compile

把zlib 1.2.8解压到zlib/zlib-1.2.8

在deflate.c文件中把deflate_copyright改成一个static变量。

在zlib目录底下创建并用Visual Studio 2008命令行运行如下bat即可生成Debug版本：

{% highlight bat linenos%}
@Echo off

set LIBDIR=%CD%\..

:: create build directory
mkdir build
cd build

cmake -G "NMake Makefiles" ..\zlib-1.2.8 ^
-DCMAKE_INSTALL_PREFIX=%LIBDIR%\zlib\install ^
-DCMAKE_C_FLAGS_DEBUG="/D_DEBUG /MTd /Zi /Ob0 /Od /RTC1" ^
-DCMAKE_BUILD_TYPE=Debug

nmake
nmake install

cd ..

mkdir elvic\lib
mkdir elvic\include
copy install\lib\zlibd.lib elvic\lib\zlibd.lib
copy install\lib\zlibd.lib elvic\lib\libz_d.lib
copy install\lib\zlibstaticd.lib elvic\lib\libz_st_d.lib
copy install\bin\zlibd.dll elvic\lib\zlibd.dll
copy install\include\*.h elvic\include\
{% endhighlight %}
 

生成Release版本请用如下bat：
{% highlight bat linenos%}
@Echo off

set LIBDIR=%CD%\..

:: create build directory
mkdir build
cd build

cmake -G "NMake Makefiles" ..\zlib-1.2.8 ^
-DCMAKE_INSTALL_PREFIX=%LIBDIR%\zlib\install ^
-DCMAKE_C_FLAGS_RELEASE="/MT /O2 /Ob2 /D NDEBUG" ^
-DCMAKE_BUILD_TYPE=Release

nmake
nmake install

cd ..

mkdir elvic\lib
mkdir elvic\include
copy install\lib\zlib.lib elvic\lib\zlib.lib
copy install\lib\zlib.lib elvic\lib\libz.lib
copy install\lib\zlibstatic.lib elvic\lib\libz_st.lib
copy install\bin\zlib.dll elvic\lib\zlib.dll
copy install\include\*.h elvic\include\
{% endhighlight %}

build会生成到zlib/elvic目录中。

> Usage

zlib编译好后，会生成zlib.lib和zlib.dll两个文件，这正是我们需要的。


下面还是用之前的测试程序：
新建一个VC++6工程，加入如下的代码，并将zlib.dll拷贝到项目目录下：


{% highlight cpp linenos%}
#include <stdio.h>  
#include "../zlib/include/zlib.h"  
#pragma comment(lib, "../zlib/lib/zlib1.lib")  
#progma runtime_checks("scu",off)  
int main()  
{  
  /* 原始数据 */  
  unsigned char strSrc[] = "hello world! aaaaa bbbbb ccccc ddddd 中文测试 yes";  
  unsigned char buf[1024] = {0};  
  unsigned char strDst[1024] = {0};  
  unsigned long srcLen = sizeof(strSrc);  
  unsigned long bufLen = sizeof(buf);  
  unsigned long dstLen = sizeof(strDst);  
  
  printf("Src string:%s\nLength:%ld\n", strSrc, srcLen);  
    
  /* 压缩 */  
  compress(buf, &bufLen, strSrc, srcLen);  
  printf("After Compressed Length:%ld\n", bufLen);  
  
  /* 解压缩 */  
  uncompress(strDst, &dstLen, buf, bufLen);  
  printf("After UnCompressed Length:%ld\n",dstLen);  
  
  printf("UnCompressed String:%s\n",strDst);  
    
  return 0;  
}  
{% endhighlight %}

和之前的程序类似，只多了#pragma comment，这里需要填入zlib.lib的正确路径

>有时用VS2008调用VC6编写的DLL动态库，会出现Run-Time check Failure#0错误，网上搜索一般的解决方法都是说
__stdcall和__cdecl函数声明不一致所造成，但是，有时候，即使声明同样的函数调用方式，也还会出现RUN-TIME错误，
这是，可以试试在代码中加入#pragma runtime_checks( "scu", off )（#pragma将vs的运行时检查全部关闭）.
具体说明：[link][1]


  [1]: http://blog.csdn.net/lights_joy/archive/2009/09/16/4558099.aspx