---
layout: post
title: "Allada Configuration"
description: ""
category: ALU
tags: [Allada,Jenkins,Server]
---
{% include JB/setup %}
Shanghai Allada Server Setup and Configure Manual
1.       Allada Introduction
1.1   global architecture
1.2   allada function
2.       Allada Server Setup
     2.1   prerequisite
	•	RAID5 configuration
		o	Tips: you must configure RAID5 before starting to install OS
		o	Tips: the configuration detail -> please see. http://blog.chinaunix.net/uid-20473651-id-1942802.html 
	•	Windows Server 2008 R2 installation
		o	following the setup wizard to install OS.
		o	if you can't find Ethernet adapter in Device Manager after finishing installation,then you should follow the next step to install drivers.
	•	Drivers installation
		o	Download URL: http://www8.hp.com/us/en/support-search.html
		o	input the machine type or series number to search your drivers. e.g: DL380 G8
	•	Network configuration
		o	Configure IP by following the screen shot below
		o	  
		o	Update Configuration -> CMD: ipconfig /renew to enable your IP cofiguration.
		o	Enable Remote : Computer -> Properties -> Advanced system settings -> remote -> select enable connections from...
		 
		o	Test configuration by "ping & remote" your server with another machine. 
		o	Please store all the software you installed into a folder named "Allada Software" for maintaining
     2.2   software package and tips
          2.2.1 install all the package below step by step
		•	Microsoft .NET Framework Service Pack
			o	Server Manager -> Features -> Add Features -> select .NET Framework Features
			o	 
		•	Microsoft .NET framework 4.0 Client Profile
	o	Download link: http://www.microsoft.com/en-us/download/details.aspx?id=24872
•	Microsoft Visual C++ x86
o	 Download link: http://www.microsoft.com/en-us/download/details.aspx?id=5555
o	 Tips: You must restart your server after install the next package.
•	Microsoft .Net Framework 4.5 
o	Download link: \\172.24.186.218\software\software\dotNetFx45_Full_setup.exe 
•	MySQL Connector 6.6.6
o	Download link: http://dev.mysql.com/downloads/connector/net/5.0.html#downloads
o	In order to download this package, you should register a account.
•	Jenkins
o	Download link: http://jenkins-ci.org/
o	 
o	You can see the Jenkins Server started by visiting http://localhost:8080 after finishing installation
•	FTP Server/Client
o	Download link : https://filezilla-project.org/
•	JDK
o	It is a dependence for Jenkins server.
o	In order to download this package,you should have a oracle account.
o	link : http://www.oracle.com/technetwork/java/javase/downloads/index.html 
3.       Jenkins Configuration
      3.1   Jenkins environment configure
•	 Add your JDK installation path to system PATH
o	e.g:  
o	CMD: type "java -version" to check whether the system variables works
•	Start Jenkins sever with administrator account
o	Create two new batch files named with add "startJenkins.bat" & "endJenkins.bat"
o	Add the following script into these batch files.
o	user: administrator
o	password: asb#1234
- startJenkins.bat
{% highlight bat linenos %}
@echo off
cd C:\Users\Administrator\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup
if "%1" == "h" goto begin 
mshta vbscript:createobject("wscript.shell").run("%~nx0 h",0)(window.close)&&exit
:begin
cd /d E:\Program Files\Jenkins
java -jar jenkins.war --argumentsRealm.passwd.administrator=asb#1234 --argumentsRealm.roles.administrator=admin >> F:\Jenkins_log\jenkins.log
{% endhighlight %}

- endJenkins.bat
{% highlight bat linenos %}
REM end Jenkins service
taskkill /f /im java.exe
{% endhighlight %}

•	Add Jenkins service to the startup item
o	copy the startJenkins.bat to the path: C:\Users\Administrator\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup
•	configure the global security
o	login with your admin account.
o	select Manage Jenkins -> Configure Global Security
o	set the items by following picture
o	 
•	Install plugin for Jenkins : Parameterized Trigger plugin
o	download link: http://mirror.bit.edu.cn/jenkins/plugins/parameterized-trigger/2.25/parameterized-trigger.hpi 
o	Manage Jenkins -> Manage Plugins ->  Advanced -> Upload Plugin
o	select parameterized-trigger.hpi you download and then upload, it will be automatically installed
o	Please check the Installed to make sure the plugin have been correct installed
3.2   Jenkins global environment configure
3.3   Jenkins job configure
4.       Allada Environment Configuration
4.1   ftp configure
4.2   directory configure
4.3   commonloadpara.xml configure
5.       Configure Altes to connect Allada