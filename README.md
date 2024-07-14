# ACTIVE-DIRECTORY-HOMELAB-WITH-SPLUNK-INTEGRATION

## Objective
The Active Directory Home Lab project aimed to establish a comprehensive environment for learning Active Directory administration and cybersecurity practices. The primary focus was to set up an Active Directory environment, integrate it with Splunk for log analysis, and simulate attacks using Kali Linux and Atomic Red Team. This hands-on experience was designed to deepen understanding of IT administration, attack patterns, and defensive strategies.

### Skills Learned
Advanced understanding of Active Directory setup and administration.

Proficiency in configuring and using Splunk for log ingestion and analysis.

Ability to simulate and recognize cyber attack signatures and patterns.

Enhanced knowledge of network protocols and security vulnerabilities.

Development of critical thinking and problem-solving skills in cybersecurity.

### Tools Used
Active Directory on Windows Server 2022 for managing resources.

Splunk for Security Information and Event Management (SIEM) to analyze logs and telemetry.

Kali Linux for simulating cyber attacks.

Atomic Red Team for generating realistic attack scenarios and telemetry.

## Steps
**01 Introduction**

I will be conducting a walkthrough on setting and configuring an Active directory lab which consists of certain platforms like Splunk and Kali Linux. I will also be running the Atomic Red Team framework, which is an open-sourced library consisting of MITRE ATT&CK scripted tests to help alert a security system’s defenses. By Conducting the lab, I was able to examine how a domain environment works, the process of initializing users within an active directory, ingesting events into a SIEM, and creating telemetry in regards to real-world attacks to improve future threat detection and defenses. By using the Atomic Red team to simulate the attacks, I was able to effectively use splunk to ingest events and practice attacking a device from a remote Kali Linux device. This lab has improved my capability to effectively identify and address security threats. This project not only offered practical experience but also emphasized the significance of proactive measures in strengthening cybersecurity defenses.

**02 Lab Setup**

For the lab I utilized 4 virtual machines (VMs) to help simulate the lab environment through Oracle Virtualbox, a virtualization software capable of creating VMs on a single host machine. 
 
1. Ubuntu Server: Hosted the Splunk server.

2. Kali Linux: Functioned as the attacker.

3. Windows Server 2022: Provided Active Directory services and sent logs to Splunk.

 4. Windows 10 Virtual Machine (VM): Served as the target machine and sent logs to Splunk.

![image](https://github.com/user-attachments/assets/1eec97a4-a4d2-471b-a93f-58642e8f9f21)


**03 Configuration**

Note: Please install your own VMs by yourself before continuing with the walkthrough.

Configuration Steps​

1. Setting Up the Network:

First, I created a NAT network and configured the network settings for all the virtual machines (VMs) so they could all communicate to each other and access the internet through my host machine.

 

2. Giving the Ubuntu Server a Static Address:

Next, I made sure the Ubuntu server was set to the same static IP address (192.168.10.10)  to establish uniformity and a DNS server of (8.8.8.8), which is also one of Google’s public DNS servers. 

 

3. Getting Splunk Up and Running:

I downloaded and installed Splunk Enterprise on the Ubuntu VM. One can do so by traversing to the Splunk website and downloading the free edition of Splunk enterprise. This enables splunk to help us keep an eye on what's happening across all our machines.

 

4. Getting the Windows 10 Machine Ready:

I renamed my Windows 10 VM to "target-PC", to help easily identify the VM during the lab, and set its address to 192.168.10.100. Then, I checked the connectivity by accessing the configured Splunk at 192.168.10.10:8000. - look into the ip

 

5. Setting Up Splunk Forwarder:

On the target-PC, I installed Splunk Universal Forwarder. This tool can be configured to send specific telemetry to Splunk so we can keep track of information a user may be interested in without being bogged down by irrelevant telemetry. 

 

6. On the target-PC machine, head over to the Microsoft Website to download Sysmon, an internal Microsoft Suite tool. Once downloaded I would download a Sysmon configuration file from olafhartong on GitHub, a file that detect attacks based on the MITTRE Attack, and make sure our Sysmon file adopts the configuration. 

 

​​​​​​​​​7. Configuring Splunk Forwarder:

 I set up the Splunk forwarder on the target-PC to send the exact telemetry we need back to Splunk. This is done, under administrative privileges, by creating and configuring an input.conf file in the “C:\Program Files\SplunkUniversalForwarder\etc\system\local folder” where we add our specified events. 

​

​​![image](https://github.com/user-attachments/assets/36ddc82f-24b7-4663-b8ba-c3bd8e0fe7bf)

 

After making the configurations, I restarted the Splunk Forwarder Service under the Windows “Services” application.

**03 Configuration (Continued)**

8. Making Sure Splunk Is Ready:

   - Since we are to receive telemetry from the Splunk forwarder, I configured Splunk to listen for incoming data on the default port 9997. This way, it's all set up to receive the information we're sending from the forwarder. 
 
 
![image](https://github.com/user-attachments/assets/30de04ae-2758-46f6-919d-9e753b9a9c36)



9. Repeating/Looping for Windows Server 2022 VM:

    - I went through the same steps (4-9/8) for my Windows Server 2022 VM, making sure it was set up and configured just like the Windows 10 machine. 

​

10. Setting a Static IP for the Windows Server:

    - The Windows Server needed a stable address too, so I set it to 192.168.10.7 to keep everything organized.

 

     ​​​​​​​​​​​​Note: you can ping or ip config to confirm that the correct ip address is correct for the machine. 

 

   - Configuring Windows Server VM

Go ahead and open up the Windows Server Manager application installed earlier.

​

​​![image](https://github.com/user-attachments/assets/8a061613-1bec-4396-a063-fbafeebbd694)


Then, ​​​​​​​​​​Head over to manage and click on “Add Roles and Features”

​

11. Getting Active Directory Ready:

    - When configuring continue the prompts normally until reaching the Server Roles tab on the right pane and click on the Acitve Directory Domain Services (ADDS): 

 
​
![image](https://github.com/user-attachments/assets/a2b91026-d1d2-4412-9c8f-40ee908862c0)

​


Continue the prompt normally until you successfully complete installation of the ADDS:

 
![image](https://github.com/user-attachments/assets/039ece66-4e97-4b23-bee0-4993f60e8624)


After installing the ADDS, one must promote the server to set up the server as the domain controller: 

​
![image](https://github.com/user-attachments/assets/08d3aeeb-1b32-4220-af1d-b90ece37cb54)


I then configured the domain controller with the name "mydfir.local" and then continue the prompt normally until installation:

​![image](https://github.com/user-attachments/assets/4571b8a0-f391-40ac-9d11-c134d134c274)


​​​​After completing installation, you are automatically signed out and you should see a sign-in account that is named based on our domain name including a backslash indicating we ‘ve successfully installed our ADDS and promote the  server to a domain controller:

​

​​![image](https://github.com/user-attachments/assets/1df9da97-a117-496d-a0d1-8d55aa281489)


**04 Creating Departments and Users**

On the Server Manager dashboard, I head over to the tools tab and select the “Active Directory Users and Computers”. This allows me to create separate groups called "IT" and "HR" in Active Directory and add a user to each group. It helps keep the management of users organized and secure.

![image](https://github.com/user-attachments/assets/b358a123-643a-40c5-b23c-16ac1a2b098c)

![image](https://github.com/user-attachments/assets/d82c78b1-c409-46c6-ac23-264f3ea81c1f)

![image](https://github.com/user-attachments/assets/d8d6da95-e4da-4615-874f-1bcd17077b36)


**05 Joining the Target-PC to the Domain**

I connected our target-PC to the "mydfir.local" domain, making sure it could find everything it needed through our domain's settings.


Follow the steps:

1. Search for "PC" and click on "Properties."

 
![image](https://github.com/user-attachments/assets/b294716b-4e62-4762-a604-487f79daeba5)

​

2. Then click on "Advanced system settings."

​![image](https://github.com/user-attachments/assets/ce88b95b-d1b7-445c-a74c-0302647020b0)


3. Under "Computer Name," select "Domain" and type the domain name `MYDFIR.LOCAL`. Once you confirm your configuration, you will run into an error. We can troubleshoot this error from our target machine since it doesn’t know how to process or resolve “MYDFIR.LOCAL” which is sourced back from how DNS works. ​

​![image](https://github.com/user-attachments/assets/8714f429-2c00-4c14-9a3f-67f0a2b0d4bc)


4. To resolve this error, right-click on the wifi-adapter icon on the task bar and select "Network and Internet settings," then click on "Change adapter options."

​​​
![image](https://github.com/user-attachments/assets/3125cca4-fb6a-4966-8a71-d411c04c9cb3)


5. Right-click on the adapter, select "Properties," and choose "Internet Protocol Version 4."

​
![image](https://github.com/user-attachments/assets/49c110e4-c43c-49c1-950c-ead1090ba49f)


6. Change the DNS server from `8.8.8.8` to the domain controller at `192.168.10.7`.

​![image](https://github.com/user-attachments/assets/77b3a97d-8242-47ff-8dae-40c92f013dd5)


Note: To confirm the change, open Command Prompt and type `ipconfig /all`.​

​

7. Join Domain to Target VM:

    - Retry to join the “target-PC” with the domain

 
![image](https://github.com/user-attachments/assets/c6efd95e-dbc7-43c0-b28e-67174679cbb6)
 

After attempting a successful join,  you will receive a prompt to enter credentials before finalizing the join.​


​![image](https://github.com/user-attachments/assets/d994147b-90f1-41e1-80e1-bec9084ca6ea)
​

**06 Setting Up Kali Linux with a Static IP**

On my Kali Linux machine, I set a static IP address (192.168.10.250) to keep things consistent based on the diagram created earlier, which helps when we're testing and tracing different attacks. 

 

To do this right click the internet connection on the top pane of Kali Linux and select edit connections.​

​
![image](https://github.com/user-attachments/assets/9da5b131-2888-4110-8f1f-8cd1013f9c90)


Select the Function Cog

​
![image](https://github.com/user-attachments/assets/037b395b-f6f5-47e0-96c3-2d0b26ace033)


Next, select the IPv4 setting profile and change the method to manual and fill out the rest of the information for address and DNS servers:​

​
![image](https://github.com/user-attachments/assets/2a43d65a-ca53-4dba-a363-5e2fd41efbb9)


Note: To check if our connection configurations are correctly set up, open the terminal and run the commands “ip a” to display addresses, ping public domain names like google.com, or ping against our created servers.

​​**07 Preparing for Attacks**

To prepare for our real-world simulated attacks, open the terminal and drop the commands in the following picture. 

​
This will create a special folder named "ad-project" on Kali Linux which is where I stored all the files we'd use to test how secure our setup is. The tool we download, Crowbar, is a brute-force tool commonly utilized in various pen testing environments or situations. It can also help us test how strong passwords are against attacks.

 
![image](https://github.com/user-attachments/assets/641cc764-a65f-416a-a296-d8a90ae8393f)

**08 Getting Passwords Ready**

​The next step is to extract and edit a file called “rockyou.txt” which consists of a well-known  wordlist that comes preinstalled with Kali Linux. The common words found in the lists are often utilized in password cracking or a dictionary attack, and for security auditing as well. I utilize the list of passwords for a simulated attack by extracting and modifying a personal password list to use for brute force. 


This is for extracting the file: 
![image](https://github.com/user-attachments/assets/ffeb9174-2fb4-4630-8c77-f588c578fe00)


Copying the file into the ad-project folder and moving myself to the folder:

​![image](https://github.com/user-attachments/assets/c71186f9-4563-49ea-80e7-4e2a898b2d5a)


This will copy the first 20 words inside the rockyou.txt file and input into the passwords.txt file

​​![image](https://github.com/user-attachments/assets/0a49a746-87fa-4f86-8244-9db3fe634433)


Went ahead and edited the password.txt file to enter a custom secured password:

​![image](https://github.com/user-attachments/assets/4138e6b0-d1ed-4c9b-a2cf-1d74fcb38b9f

![image](https://github.com/user-attachments/assets/7e908ae9-2198-45d0-8861-a1483a2c4e63)

**09 Enabling Remote Desktop on Windows 10**

​Having established a good starting point for brute-force testing, I made sure we could access our Windows 10 machine remotely by turning on Remote Desktop. Then, I added the users from Active Directory who needed access.

​
![image](https://github.com/user-attachments/assets/39138ee2-97a3-44e9-bc30-99cf62fdda51)

**10 Testing with Crowbar**

​ Using Crowbar on Kali Linux, I launched a test to see if I could break into the Windows 10 machine using the passwords from our list. I utilized this command to create telemetry: 

![image](https://github.com/user-attachments/assets/b75700fa-f5f7-4ec4-ae84-71aaec958ff2)


Running the command will have crowbar test all the passwords listed within the passwords.txt file. Keep in mind that having a success status after running against a password means the login attempt was a success, and that it is still only targeting the Windows 10 VM and not the entire network we’ve set up.

​
![image](https://github.com/user-attachments/assets/07cb93b8-a887-4359-a8c6-c05b51b208c7)

**11 Checking Telemetry in Splunk**


After the test, I headed over to the Windows target machine and looked at the data in Splunk to see if we could spot any signs of an attack. It's crucial to catch these things early. I would check by checking for related event Splunk fields and find the stats of event codes 4625 (failed logins) and 4624 (successful logins) to confirm if our test had worked and if there were any security issues.​

​
![image](https://github.com/user-attachments/assets/3a804c6a-9aca-4384-9fb6-34245f43c256)


Note: If you don’t know what these codes mean, you can simply look up it up such as “event id 4625”


We can also delve into the logs deeper and see what credentials and addresses and other telemetry took place: 

![image](https://github.com/user-attachments/assets/ad3b66e6-04ab-4632-81b8-4bc6d5e35bb6)

**12 Installing Atomic Red Team**

To test our defenses further, I installed Atomic Red Team on our Windows 10 machine. It lets us simulate different kinds of cyber-attacks. 


Run this command to bypass unknown scripts:

![image](https://github.com/user-attachments/assets/8704b261-ea3e-4a9e-ad65-b390874fde09)


And run an exclusion on the entire drive, C:\

![image](https://github.com/user-attachments/assets/a285c299-de27-472f-8c31-f133d4a3a24b)


You should be enabled to install Atomic Red Team, and the command line to install is: 

![image](https://github.com/user-attachments/assets/8ac4c656-89e5-4e94-a5ed-9073d1d6d3c2)



After the download is complete, Atomic Red Team will be available on the C: Drive

​![image](https://github.com/user-attachments/assets/c735fbbd-5583-423b-af2c-4eeef52a6808)



I went to check the contents of the “AtomicRedTeam” folder and saw sub folders of techniques based on the MITRE ATT&CK framework: 

 ![image](https://github.com/user-attachments/assets/2af90382-a601-4e54-8f29-be3f8c569286)

 ![image](https://github.com/user-attachments/assets/8bd19962-0b5c-48b1-95b9-65d8cd49a3c2)

  **13 Generating Telemetry with Atomic Red Team**
  
 I also simulated using PowerShell for an attack utilizing the sub-technique (T1136.001).

 I initialized the technique by calling out the command

 ![image](https://github.com/user-attachments/assets/0fa618c3-ebe0-405d-8032-4bd8e0d46090)


I am then given an executed script that shows the details and was created from the sun-technique attack.

 ![image](https://github.com/user-attachments/assets/c7fb092a-d8d7-4a7b-9c97-8cae9ca9883f)



It appears that a “NewLocalUser” was created from the attack. Having and analyzing such information from the script allows us to understand an attacker’s motive and what step they’re in during their attack. 


 Again, I checked Splunk to see if it could detect this kind of activity and discovered information of a new account existing for “NewLocalUser” within the logs. 

​![image](https://github.com/user-attachments/assets/25dfa330-30ad-4b98-abdd-261c58e2aeb1)

​

​​When running techniques, I recommend using words from the Invoked Red Atomic PowerShell script and adding it to the Splunk search to help improve one’s detection skills. If you are sure you cannot find it in Splunk, this may be a tell-tale sign that there is a gap in detecting an attack, and may require you to do more research and utilize different tools to understand the executed attack technique. I also recommend attempting to invoke or initialize other techniques from Red Atomic, and research or analyze with tools like Splunk in order to help better understand how an attack works. I have tried some techniques myself as well! 



When running techniques, I recommend using words from the Invoked Red Atomic PowerShell script and adding it to the Splunk search to help improve one’s detection skills. If you are sure you cannot find it in Splunk, this may be a tell-tale sign that there is a gap in detecting an attack, and may require you to do more research and utilize different tools to understand the executed attack technique. I also recommend attempting to invoke or initialize other techniques from Red Atomic, and research or analyze with tools like Splunk in order to help better understand how an attack works. I have tried some techniques myself as well! 

**14 Key Takeaways and Conclusion**


​Doing this lab has helped give me better insight on various aspects of a SOC analyst. Such aspects were the ability to analyze and detect attacks early. Most importantly I gained insight into the process of configuring an Active Directory in order to improve management and security for users and the machine itself. The lab has tremendously improved my understanding of cybersecurity and proficiency in establishing the skills necessary as a SOC analyst. ​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​​

​
