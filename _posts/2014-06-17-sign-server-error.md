---
layout: post
title: "Sign Server Error"
description: "BVM Error"
category: AMD
tags: [AMD,BVM,Sign]
---
{% include JB/setup %}
## Sign Server Error ##

> **> 1.Issues Description**

- Your BIOS request (req01120) failed to build. To view the build log, to go: http://bvm-dev/BVMBIOS/req01120

--------------------------------------------------------------------------------------------------
> **> 2.Issues Investigation**

- check the error.log&reqForm.xml here is the content 

 - error.log

```
PSP contents have been embed to "F:\bvm\BDB\BVMBIOS\req15091\T15091.rom.sign"
Failed to sign the rom
```

 - reqForm.xml
{% highlight xml linenos %}
<opt date="6/16/2014 18:3:16" request_id="01119" requester="WilliamC.Ng@amd.com">
  <Description>test</Description>
  <Processor name="Mullins - Family 16h">
    <Platform name="Rev ML Larne Insyde">
      <CodeBase>-u F:\bvm-dev\BDB\UserBIOS\wilng1402959770\VLM5083N.rom</CodeBase>
      <IsBlob></IsBlob>
      <PRE_PSP_VALUE>0x1</PRE_PSP_VALUE>
      <PSP>12#F:\bvm-dev\BDB\BVMBIOS\req01119\12\trustlets.bin</PSP>
{% endhighlight %}

----------------------------------------------------------------------------------------------------

> **> 3.Issues Debug**
the sign tool cause this issue: **\trunk\EXE\SpClient**

 - ***there are there main sign server:(in the config file)***

 *.EXE\SpClient\config\spring.xml 
 
{% highlight xml linenos %}
    <bean id="config" class="com.amd.kgs.sp.client.SPClientConfig" >
        <property name="serverUrls">
            <list>
                <value>https://atlkds-proxy01.amd.com/pspsp</value>
                <value>https://cybkdsweb02/pspsp</value>
                <value>https://atlkdsappv02/pspsp</value>
            </list>
        </property>
{% endhighlight %}

 - ***the sign tool will automatically try all the sign server in the config file***
 - ***In fact, the sign procedure has succeed but try the second server***
 - ***the status return by the SPClient is wrong***

> **> Issues Solution**

 - delete the first sign server in the config file  --> BVM
 - change the logic of the sign tool to judge the failure of signing rom --> Ju,David