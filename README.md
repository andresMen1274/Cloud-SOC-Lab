# Cloud-SOC-Lab
Start by creating a free subscription with Azure. Then naviagte to resource groups -> create to create a new resource group and walk through the configuration. After the configuration is completed navigate to virtual network -> create. Walk through the first configuration steps and do not allow virtual network encryption, bastion, firewall, and DDOS protection. Then turn off enable private subnet, so the virtual machine will have internet access. Then create two new subnets and give them DMZ-subnet and Internal-subnet names(make sure to turn off enable private subnet as well as leave other settings as defalut). Next delete the defalut subnet and select review + create to create the virtual network. Search network security groups -> create to create a both nsg-dmz as well as nsg-internal. Make sure that all settings are left as defalut. Moreover, make sure that the same resource group and region are used for the creation of each NSG. 

Now we will add rules to the each NSG. To do this select the nsg-dmz -> settings -> inbound security rules -> add. Leave the defalut settings, but change the port to 3389(This is the port that is used for RDP). Set the protocol to TCP and name it allow-rdp. Then do the same to create a rule that allows SSH connections. Make sure to change the priority to 110(the lower the priority number means the rule will be evaluated first). The reason that these rules have been added are because we will create a honeypot virtual machine that is exposed to the internet. These rules will allow brute force attacks and allow us to gain real world attack information.

<img width="1825" height="485" alt="image" src="https://github.com/user-attachments/assets/708f6554-28c4-484d-9528-aac3306e3267" />

Now we will configure rules for the nsg-internal network security group. Select nsg-internal -> settings -> inbound security rules -> add. The source IP address is the IP address of the dmz subnet and the destination IP address is the IP address of the internal subnet.

<img width="722" height="642" alt="image" src="https://github.com/user-attachments/assets/6a69749c-cab4-413a-a2cb-de76ff6abcf5" />

The rule was added because if an attacker is able to infiltrate the honeypot virtual machine they will not be able to move laterally through the network. All of the rules that are configured for the nsg-internal should look as follows.

<img width="1826" height="452" alt="image" src="https://github.com/user-attachments/assets/46f7326f-512e-4f02-bf0b-45b39e3723a8" />

Now that all of the rules have been created we have to add these NSG rules to the Vnet. Select the virtual network that you have created -> select the dmz subnet -> naviagte to the secuirty section -> select nsg-dmz -> save. Do the same configuration with the internal subnet. What this does is add the rules that we want to enforce to each subnet. 

<img width="1020" height="265" alt="image" src="https://github.com/user-attachments/assets/bb141da0-512d-4e50-a5b6-40d9d45d7beb" />

Now we will create the virtual machine to do this navigate to virtual machines -> create -> virtual machine. Select the resource group that you created and name the virtual machine. Since I want this lab to be free I will select no infastructure redundancy require(This means if the virtual machine goes down it will not restore itself). For this particular lab I selected the Windows 10 Enterprise, version 22H2 -x64 Gen2. The size will vary with what you will like to do with the lab. In this case I want to make it free and build a complex network, so I will select the standard_B2ats_v2(This is the cheapest option). Next create a usernmae and password. When in the disk tab you can leave everything as defalut, but I want this lab to cost no money so I will change it to a standard HDD. In the networking tab use the dmz subnet that was previously created and enable the public IP(This allows others to discover the vm). I have selected delete public IP when vm is delected and disable accelerated networking. Do not change anything in the management tab then next. In the monitoring tab diable boot diagnoistics. Then select review and create.

<img width="1216" height="495" alt="image" src="https://github.com/user-attachments/assets/3fc302a4-2bf1-47b3-9c40-e34229433374" />

Now that the virtual machine has been created go to connect -> RDP -> Download RDP. After this is done open the file and login with the credientals previously created. Now I will generate some fake traffic by copying the public IP address of the newly created virtual machine and using remote desktop control app on Windows to have false connections. The logs will be shown on Windows event viewer. When in event viewer naviagte to -> windows logs -> security. Then select create filter and enter the id 4625(Id for events in which a login attempt has failed).

<img width="497" height="105" alt="image" src="https://github.com/user-attachments/assets/7cd82d13-f70f-44e8-ab7c-6af2ea6ba611" />

Next we will setup a log analytics workspace. To do this navigate to log analytics workspace -> create and select the resource group that we created for the virtual machine. Then select review and create. After it has finished we will install a sentienal instance on the virtual machine. Go to Microsoft Sentinel -> create -> select the log analytics workspace that we created. Now we have linked the log analytics workspace to microsoft sentinal. While in the sentinal select the name of the log analytics workspace -> content management -> content hub -> install windows security events

<img width="578" height="202" alt="image" src="https://github.com/user-attachments/assets/4c88a3d7-8366-4960-a2b1-cb9e0ef94380" />

When it has finished installing it will say manage. Click manage and Windows Security Events via AMA -> create data collection rule. Name the rule and select the virtual machine and create.  

<img width="763" height="597" alt="image" src="https://github.com/user-attachments/assets/75e1a0ca-3adb-4a18-91be-6b10a46cac04" />

