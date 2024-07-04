<p align="center">
<img src="https://imgur.com/BgjkD0J.png" height="80%" width="80%"/>

</p>

<h1>Microsoft Sentinel SIEM Home Lab - Live Cyber Attacks</h1>

<h2>Description</h2>
This home lab shows me setting up a Microsoft Sentinel SIEM and connecting it to a live virtual machine acting as a honeypot. I configured the SIEM to log live RDP brute force attempts from various locations around the world. I then utilized a custom PowerShell script to lookup the attackers' geolocation information and plot it on the Microsoft Sentinel map. Follow along below to see the process and results!





<h2>Languages and Utilities Used</h2>

- <b>PowerShell Script</b>
- <b>ipgeolocation.io</b> 

<h2>Environments Used </h2>

- <b>Microsoft Azure</b>
- <b>Microsoft Sentinel</b>
- <b>Windows 10 VM</b>




<h2>Setting Up Our Honeypot (Windows 10 VM):</h2>

<p align="center">
<br />
First thing we're going to do is create our Windows 10 virtual machine, which will act as our honeypot. We want to make sure that RDP is selected as our inbound port. 
<br />
<br />
<img src="https://imgur.com/mYw2ryZ.png" height="80%" width="80%"/>
<br />
<br />
<img src="https://imgur.com/9vQcZyZ.png"height="80%" width="80%"/>
<br />
<br />
Next, we need to configure a new network security group. To do this, we'll select advanced next to NIC network security group. Doing this will give us the option to create a new group where we can add a new inbound rule. 
<br />
<br />
The asterisk that is placed in the destination port ranges simply means any port range.
<br />
<br />
The rule that we've created will essentially allow all traffic into the virtual machine. We're doing this to make the machine more discoverable so we can generate as much traffic as we can. 
<br/>
<br />
<img src="https://imgur.com/slYNrGt.png" height="80%" width="80%"/>
<br />
<br />
<img src="https://imgur.com/zNYVfvy.png" height="80%" width="80%"/>
<br />
<br />
Now we can click review and create.
<br />
<br />
<img src="https://imgur.com/kjTOfPi.png" height="80%" width="80%"/>
<br />
<br />
<br />
<br />
<br />
<h2>Configuring Our Log Analytics Workspace:</h2>

<p align="center">
<br />
While the virtual machine is being created, we can head over to the Log Analytics workspace by searching for it in the search bar.
<br />
<br />
The reason we're creating a log analytics workspace is to ingest logs from our virtual machine.
<br />
<br />
We're also going to end up creating our own custom log that contains the attackers' geographic information
<br />
<br />
<img src="https://imgur.com/mcxJxrS.png"height="80%" width="80%"/>
<br />
<br />
<img src="https://imgur.com/zuqRAYU.png"height="80%" width="80%"/>
<br />
<br />
Now that we've created the virtual machine (honeypot-vm) and log analytics workspace (law-honeypot), let's head over to Microsoft Defender for Cloud to enable the ability to gather logs from the virtual machine into the log analytics workspace. 
<br/>
<br/>
We'll go to our log analytics workspace environment settings and disable SQL servers and select All Events under Data Collection on the left hand side.
<br/>
<br/>
<img src="https://imgur.com/WOFGP5R.png" height="80%" width="80%"/>
<br />
<br />
<img src="https://imgur.com/8fu68zK.png" height="80%" width="80%"/>
<br/>
<br/>
<img src="https://imgur.com/UKAiBT4.png" height="80%" width="80%"/>
<br />
<br />
<br />
<br />
<br />
<h2>Connecting Our Log Analytics Workspace to the VM:</h2>

<p align="center">
<br />
Very simple step here. We'll just go back to the log analytics workspace, select virtual machines, and connect.
<br />
<br />
<img src="https://imgur.com/ohZgihK.png" height="80%" width="80%"/>
<br />
<br />
<img src="https://imgur.com/JVmiQ0s.png" height="80%" width="80%"/>
<br />
<br />
<br />
<br />
<br />
<h2>Setting Up Microsoft Sentinel:</h2>

<p align="center">
<br />
Just like the others, we will use the search bar to locate Microsoft Sentinel.
<br />
<br />
<img src="https://imgur.com/zVrz9Mo.png" height="80%" width="80%"/>
<br /> 
<br />
From here, we'll click Create Microsoft Sentinel then add it to our log analytics workspace. 
<br />
<br />
<img src="https://imgur.com/Yyw97u6.png" height="80%" width="80%"/>
<br />
<br />
<br />
<br />
<br />
<h2>Logging Into Our VM via Remote Desktop:</h2>

<p align="center">
<br />
Now that our virtual machine has been created, we'll login using the credentials we created at the beginning. But first, let's get the public IP address for it by going back to virtual machines in Azure and copying the IP address from the right hand side of the screen.
<br />
<br />
<img src="https://imgur.com/LmgKCi9.png" height="80%" width="80%"/>
<br />
<br />
Now that we have the IP address of our virtual machine, let's login via remote desktop.
<br />
<br />
<img src="https://imgur.com/SN0BJjz.png" height="80%" width="80%"/>
<br /> 
<br />
<img src="https://imgur.com/SXg2o5A.png" height="80%" width="80%"/>
<br/>
<br/>
<img src="https://imgur.com/tcrHU6L.png" height="80%" width="80%"/>
<br/>
<br/>
Next, we can go to Event Viewer and check the security events within our Windows Logs.
<br />
<br />
I purposely generated a security event by making a failed attempt at logging into the machine so I could view it here.
<br />
<br />
We can see the username that was attempted. I used antman. If you scroll down a bit, you can see the workstation and IP address that I attempted to login from as well.
<br />
<br />
This is great, but we want geographical information as well. We want to know where these login attempts are coming from. This is why we will use ipgeolocation.io
<br />
<br />
Before doing so, we'll turn off the windows firewall so our virtual machine can respond to requests and attackers can discover it faster.
<br />
<br />
<img src="https://imgur.com/9TKymVN.png" height="80%" width="80%"/>
<br/>
<br/>
Now we'll use ipgeolocation.io to get our API key for our PowerShell script to export the logs.
<br/>
<br/>
Once we have our API key, we'll open up PowerShell ISE and add it into the beginning of our script, then save it to the desktop.
<br/>
<br/>
Don't worry, I changed the API before posting this.
<br/>
<br/>
<img src="https://imgur.com/ZRSj4Zx.png" height="80%" width="80%"/>
<br/> 
<br/>
<img src="https://imgur.com/sjvRdmj.png" height="80%" width="80%"/>
<br/>
<br/>
We can now run the PowerShell script for the first time.
<br/>
<br/>
<img src="https://imgur.com/1uFrD5n.png" height="80%" width="80%"/>
<br/>
<br/>
Take a look at lines 3 and 4 of our PowerShell script. You'll notice a log file that we're creating with the geodata of all the failed login attempts. 
<br/>
<br/>
Let's open up that log file and examine it. If we look at the bottom line, it is the failed attempt that I purposely made as a test. You can see the username, antman, that I tried, coordinates, state, country, IP address, and timestamp. The rest of the lines above are all sample data that we'll use to train the log analytics workspace.
<br/>
<br/> 
<img src="https://imgur.com/9njTiUh.png" height="80%" width="80%"/>
<br />
<br />
<br />
<br />
<br />
<h2>Azure Custom Log & Map Creation:</h2>

<p align="center">
<br />
Now we will create a custom log in our log analytics workspace inside Azure. 
<br/>
<br/>  
<img src="https://imgur.com/WM7Osxk.png" height="80%" width="80%"/>
<br/>
<br/>
In order to add the sample log file to our log analytics workspace, we need to copy the log from the virtual machine and paste it into our host machine. From there we'll paste it into notepad on our host machine, save it, and upload it into Azure.
<br/>
<br/>
<img src="https://imgur.com/CttJyb0.png" height="80%" width="80%"/>
<br/>
<br/>
<img src="https://imgur.com/VGVZuan.png" height="80%" width="80%"/>
<br/>
<br/>
We need to make sure the log path is the same as our virtual machine.
<br/>
<br/>
<img src="https://imgur.com/sIeRTJ4.png" height="80%" width="80%"/>
<br/>
<br/>
After creating the custom log and giving it some time to begin populating the data we're looking for, we can run the log.
<br/>
<br/>
Now we see the sample data that we created the log with.
<br/>
<br/>
<img src="https://imgur.com/WRtHXYA.png" height="80%" width="80%"/>
<br/>
<br/>
At this point we can go back to Microsoft Sentinel and add a new workbook to create our geomap.
<br/>
<br/>
<img src="https://imgur.com/grH38va.png" height="80%" width="80%"/>
<br/>
<br/>
We'll add the following lines of code to pull the specific information we want for our map. This includes the latitude, longitude, sourcehost, label, destination host, country, and event count.
<br/>
<br/> 
<img src="https://imgur.com/KcBFu2B.png" height="80%" width="80%"/> 
<br/>
<br/>
The visualization we want for this is a map, so we'll select map from the dropdown menu.
<br/>
<br/>
<img src="https://imgur.com/jXZyPZR.png" height="80%" width="80%"/>
<br/>
<br/>
After confirming our map settings, we can apply them and save. We now have our geomap to show us all the data that we want to pull.
<br/>
<br/>
While letting the PowerShell script run during the lab, we had hundreds and thousands of brute force attempts on our honeypot stemming from multiple countries.
<br/>
<br/>
If we were to let it run longer, say hours, or even days, we'd be able to collect a lot more data. But for the sake of time, we had a sufficient amount of data.
<br/>
<br/>

<br/>
<br/>

</p>

<br />
<br />

<h2>Conclusion</h2>

This project demonstrated the effective use of Microsoft Sentinel to monitor and analyze live cyber attacks on a honeypot virtual machine. By leveraging a combination of PowerShell scripts and the ipgeolocation.io service, I was able to successfully log and visualize RDP brute force attempts from various locations around the world in real time. This setup was able to provide me with valuable insights into the geographic distribution of potential cyber threats. After completing this project, I can confidentally say that I've gained a deeper understanding of SIEM configurations, log management, and the importance of proactive cybersecurity measures.

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
