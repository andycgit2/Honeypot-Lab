# Honeypot-Lab

Mission:
Create a virtual machine using Microsoft Azure that allows all incoming traffic from all ports to capture and visualize location information from RDP Brute Force Attacks using the Microsoft Sentinel SIEM tool.

Steps:
1.	Create a Microsoft Azure account.
  a.	Accessible via portal.azure.com
  b.	This will use a portion of the $200 in free credits, but should not cost any money out of my pocket.
  c.	BE SURE TO DELETE THE RESOURCE GROUP WHEN FINISHED!!! (Or else I’ll be paying)
2.	Create a virtual machine
  a.	I am using Windows 10 to gain more experience with the OS (I couldn’t use Windows 11  with my current subscription).
  b.	Minimum specs: 2 vcpus with 8GB of RAM
  c.	Setup the VM firewall.
    i.	Add a firewall rule that allows all incoming traffic from any port.
    ii.	Set priority to 100.
  d.	Finish the build.
  e.	It takes a while to finish building the virtual machine.
3.	RDP into your VM.
  a.	Find your machine’s IP and RDP into it.
  b.	Say no to all the privacy settings. They are just trying to pull information off of you.
  c.	Open Edge.
4.	Open Event Viewer on the Windows Device.
  a.	Go to “Windows Logs” and click on the “Security” subfolder.
  b.	Try to fail logging on to the VM through RDP so an event pops up.
5.	Ping your VM’s IP from your personal PC to show timestamp.
  a.	Example
ping 20.163.75.215 -t
  b.	As you can see from the example, your VM is hiding information.
6.	Open Window’s Firewall
  a.	Type wf.msc in the search box.
  b.	Click on “Windows Defender Firewall Properties”
  c.	Turn off the Firewall on the Domain, Private, and Public Tabs. Click “Apply”.
  d.	When you ping your device again, you can see a response from the VM because turning off the firewall enabled echo time requests.
7.	Create a Log Analytics Workspace (LAW).
  a.	This will be where we store the collected logs for the SIEM tool to analyze.
  b.	Use the same resource group as the virtual machine.
8.	Allow logs from the server to be analyzed by the Log Analytics Workspace.
  a.	Search for Microsoft Defender for Cloud.
  b.	Go to “Environment Settings”
  c.	Find your subscription at the bottom and go to the subcategory where your LAW resides.
  d.	Select your LAW.
  e.	Make sure “Servers” is selected to be “On”. Everything else can be left turned off.
  f.	Click “Save” in the top-middle of the screen.
  g.	Next, select “Data collection” underneath “Azure Defender plans” on the left-hand side of the screen.
  h.	Choose “All Events” to collect data and click “Save” again.
9.	Connect your VM to the LAW.
  a.	Go back to the “Log Analytics workspaces”.
  b.	Click your workspace.
  c.	Find your virtual machine. (it says deprecated now)
  d.	Click your machine and then select “Connect” at the top.
10.	Set up the SIEM, Microsoft Sentinel connection to the VM.
  a.	Create Sentinel.
  b.	Choose the LAW to work with and click “Add”.
  c.	Once that’s done, go to “Content Management” to “Content Hub”.
  d.	Go to Window’s Security Events and click “Install”.
  e.	Once everything is done installing, click on Windows Security Events and then select “Manage”.
  f.	Select “Windows Security Events via AMA” and then click “Open Connector page”.
  g.	Click “+Create data collection rule”.
  h.	Enter subscription information. Click, next.
  i.	Choose the VM you want to connect.
  j.	Collect all security events.
  k.	Finish creating data collection rule.
  l.	Once finished, go to VMs. Select your VM, go to “Settings”, find “Extensions + applications”, and you will see an Azure Monitoring Connector.
11.	Query Log Repository
  a.	Go to the “Logs” in LAW
  b.	Go to the KQL page and query “SecurityEvents” table.
    i.	Example KQL Query
  SecurityEvent
  | where Account == “User”
  | project TimeGenerated, Account, Computer, EventID
    ii.	This queries all data from the SecurityEvent table, wherever the Account field equals “User”, and project is used to show only the columns I listed afterwards.
13.	Upload Geolocation data to Sentinel One.
  a.	I have a .csv file that maps IP ranges to locations throughout the world.
  b.	Select your workspace on Sentinel and under “Configuration”, select “Watchlist”.
  c.	Create a new Watchlist.
  d.	Make the name and alias the same.
  e.	Choose your .csv and then make the “SearchKey” “network”. That is our primary key.
  f.	Create the Watchlist.
  g.	Click on your newly created watchlist. It should show how many items are on it (this could take some time). This is another Table added to your LAW. You can query the Security Events table and call the watchlist to produce your desired information.
    i.	Example:
SecurityEvent
_GetWatchlist(“geoip”)
14.	Create an Attack Map workbook in SentinelOne.
  a.	Go to “Threat Management” and select “Workbooks”.
  b.	Click “Add Workbook” and then “Edit”.
  c.	Remove all the pre-populated clutter.
  d.	Copy json from the following link: https://drive.google.com/file/d/1ErlVEK5cQjpGyOcu4T02xYy7F31dWuir/view 
  e.	“Add query” in the workbook and select “Advanced Editor”.
  f.	Remove all content and paste your json. Click “Done Editing” when finished.
  g.	Save your workbook.
