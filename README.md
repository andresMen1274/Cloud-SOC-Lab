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

