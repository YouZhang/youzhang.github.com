---
layout: post
title: "New Program Support_Amur"
description: "BTS support Amur"
category: AMD
tags: [BTS,Amur]
---
{% include JB/setup %}

## In order to modify the database safely, you should export RegDefSDK_bts to local (.xsl file.) ##
	

	• !!!Notice: please backup the DB before all your work.

		1. Enter the folder: SVNRoot/RegDefSDK/mysql_dba/  (putty)
		2. Run: ./backup_database.sh [program name].  Such as ./backup_database.sh  RegDefSDK_bts
	• Add Entrance For New Program
		1. create empty database for Mullins (MySQL) -> (RegDefSDK_AM) using dbvisualizer
			§ Server: btsinventor 
			§ User: sa
			§ Password: sa
		2. modify BTS inventor source code -> use putty 
			○ server: btsinventor
			○ user: schen
			○ password: letmein
		2.1 modify file:  ~\SVNRoot\RegDefSDK\config\database.yml added Amur section
		
![add Amur][1]


		2.2 added new file ~\SVNRoot\RegDefSDK\config\environments\amur.rb in environment folder (copy from kryptos.rb and rename)
		2.3 added new entry in ~\SVNRoot\RegDefSDK\app\controllers\home_controller.rb,
        - add new program to program_list otherwise, the website will not show the program. 

![Add entry][2]
			
		3. modify /opt/nginx/conf/nginx.conf
			i. Added new port in this  file (by Putty)

![Add new port][3]

		4. create link RegDefSDK_ML point to RegDefSDK in folder  /home/schen/SVNRoot/
			i. ln -s /home/schen/SVNRoot/RegDefSDK   RegDefSDK_ML
		5.  restart nginx by:   ./etc/init.d/nginx restart
		6.  run script:   ./run_migrate.sh  mullins    (~/SVNRoot/RegDefSDK)   --> it will create the empty tables and the data will be set by "export" later
		7.  add entrance for new Program in BTSInventor -> edit RegDefSDK_common::ip_blocks -> e.g. : add 9020 port for Nolan & FCH			
		8. Add program_id for new program RegDefSDK_bts::bts_programs  -> e.g. Kabini -> 1 

![Database][4]		

> DB Modification

	• the Database Model you should know
		○ device　---device_type_id---> device_pattern
		○ device_pattern -> access method::script_id
		○ script_id -> script(read Register) 
		○ Shunt items ---include exclude pattern---> export the lua package we need
	• the Tables you may edit:
		○ RegDefSDK_common::ip_blocks -> entrance of btsinventor
		○ RegDefSDK_bts::bts_programs  -> id for all programs
		○ RegDefSDK_bts::device type -> known device types of all programs
		○ RegDefSDK_bts::devices -> devices of all programs by BKDG
		○ RegDefSDK_bts::access method -> access method script_id for all devices & device::register
		○ RegDefSDK_bts::scripts -> read register lua function for all device types
		○ RegDefSDK_bts::platform variables -> some variables write in BKDG
			§ Ddr4Mode#dct0 -> D18F2x78_dct0[DramType] == 2
			
	• List all the devices&register instances by looking up BKDG
		o List all Core&GPU&NB Devices
			§ Add the device information to the table according to the picture below
			§ Tip: all the devices in this part may have the device type id (Known)
			§ Tip: if the device has existed in the “device_types table”,then add DeviceTypeID to your list 
			§ Tip: add the index/data registers pair to Special Instance. -> E.g : D0F0x60/D0F0x64
			§ Tip: some device may not have the device ID, you can use D18F2 deviceID instead. E.g : MISC GPIO
			§ Rule: mark the unknown device type with “n”

![list all core devices][5]			
			
		o List all FCH Devices
				§ Add devices information to the table according to the picture below.
				§ Tips: a lot of the devices in this part may be unknown.
				§ Rule: mark all the root device in red. -> E.g : SATA Controller
				§ Rule: mark all the ignored device( no need to test ) in gray.  -> E.g : Legacy Block Configuration Registers(IO)
				§ Rule: mark all the new devices you want to add in yellow
				§ Rule: mark the unknown device type with “n” 

![list all FCH devices][6]
				
	• Modify Database offline
		o Create new files named with “newDeviceTypes.xls” & “FCHDevices.xls” & “OtherDevices.xls” 
		o Add new device_types (the content you marked in yellow) into “newDeviceTypes.xls” 
			§ E.g: it shows how to add new device type to table by the picture below
		o Add new “access_methods” & “script” for new device_type according to BKDG. E.g : MISCx*
			§ Script Rule: the read method may need to wirte some register, so it is important to restore the original register value.
			§ Add script according to BKDG Info -> this is a difficult part in New Program Support., see the picture below
			§ Tip: if you have some trouble in using BTS function like btsSetBitField, you can see the existed scripts for reference.
			
![Add Access Methods][7]				
			
		o Add core&GPU&NB devices to “OtherDevices.xls” 
			§ Tip: there is a easy way to add devices -> copy a existed program to modify
			§ Tip: you can simply change the device ID to add known devices 
			
![Add new device tip][8]				
			
		o Add FCH Devices to “FCHDevices.xls”
			§ Follow the tips mentioned in previous step to add all FCH devices.
		o Classify the export errors & Add Platform Variables to “PV.xls”
			§ you can run export code after you finish all the step above -> \\reg_def_sdk\client\python\exportlua 
			§ You will see the errors “Error: Cannot resolve symbol:”.
			§ Classify all the errors according to the picture below, all the symbol should be solved according to BKDG
			§ Tips : you can skip the "FUSE. Metal Bump SKIPRESET BOZO See" error
			
![Skip fuse][9]				
			
			§ Add the Platform Variables to “PV.xls” and commit your change after making sure all the contents are correct.
			§ when database updated, re-export the lua package again
	• Commit your change to database
		o Append RegDefSDK_bts::devices_types with “newDeviceTypes.xls” data, make sure the id must not be repeated.
		o Append RegDefSDK_bts::devices with “FCHDevices.xls” & “otherDevices.xls”data, make sure the id must not be repeated.
		o Append “RegDefSDK_bts::Platform_Variables” with “PV.xls” data, make sure all the contents are correct.
	•  modify file:  \Source\btsapp\bts\res\PrefabFiles\common
		o Unzip the common package with 7-zip
		o Calculate family ID according the following method
		
![cal family id][10]			
		
		§ Tip : You can get Fn0000_0001 EAX by using SimNow : "CPUID 1"
		§ Calculate FCH familyID : [deviceID,vendorID,revisionID]
		§ !!!Notice:   if(expectFamilyId == (existSBFamilyId & 0xFFFFFFFFF0))         //Remove minor revision 
		§  E.g. Amur : DeviceID ->  790Bh vendorID ->  1022h revisionID  51h  ==>  790B102251 (519876649553)  => remove the minior revison  ==>  790B102200(519876649472)
	o Edit DeviceFamilyIds.lua::get_program_folder(deviceID) to make bts support new program  according to picture below. -> Nolan support 
		
![Add family ID][11]			
		
	o Edit program_revision.lua::get_processor_revision()  (socket type + revision ) to update BKDG information accordingly
		
![edit revision][12]			
		
	o Edit Source\btsapp\bts\res\Common\DeviceFamilyIds.lua & program_revision.lua as well
	o Commit all your change to 8.0.10 & trunk
	

> • If all the steps have done, BTS will support the new program.

- For ARM Arc program support --> need to change the interface of this BTS & SimNow



  [1]: http://media-cache-ec0.pinimg.com/originals/e0/59/0d/e0590d6831ee5206612c8561d83eb687.jpg
  [2]: http://media-cache-ec0.pinimg.com/originals/5f/20/39/5f2039cdec9c9d6fdd3a22b0e3cb9f6e.jpg
  [3]: http://media-cache-ak0.pinimg.com/originals/c4/ae/57/c4ae579fdb56209cad7c971b928c25e0.jpg
  [4]: http://media-cache-ak0.pinimg.com/originals/bf/6c/71/bf6c71d2dc1157dc4eb3b8e5a6aad3b2.jpg
  [5]: http://media-cache-ak0.pinimg.com/originals/4e/dd/fe/4eddfe3141479ff50e2f328c2c39500f.jpg
  [6]: http://media-cache-ec0.pinimg.com/originals/6f/df/a1/6fdfa1a06d1591733c897be804ddf3d7.jpg
  [7]: http://media-cache-ec0.pinimg.com/originals/21/51/e9/2151e9a5b1d59202ca4d4a142da2e795.jpg
  [8]: http://media-cache-ak0.pinimg.com/originals/af/64/fd/af64fdc44782167bc27b002bd27a925d.jpg
  [9]: http://media-cache-ec0.pinimg.com/originals/d8/7a/23/d87a23d3bba7ada097800fd0fa6ed3bf.jpg
  [10]: http://media-cache-ec0.pinimg.com/originals/18/c9/be/18c9be437ecef881d083975fd60c93f5.jpg
  [11]: http://media-cache-ak0.pinimg.com/originals/ea/13/7b/ea137b1f9ca1fdde66089bc9a2df1612.jpg
  [12]: http://media-cache-ak0.pinimg.com/originals/00/b2/14/00b2146fc6ddf6d758c3c27624573910.jpg