# Honeypot-Lab

## Mission
Create a virtual machine using Microsoft Azure that allows all incoming traffic to capture and visualize geolocation data from RDP Brute Force Attacks using the Microsoft Sentinel SIEM tool.

## Steps
1.	Create a Microsoft Azure account.
- Accessible via portal.azure.com
- This will use a portion of the $200 in free credits, but should not cost any money out of my pocket.
- BE SURE TO DELETE THE RESOURCE GROUP WHEN FINISHED!!! (Or else I’ll be paying)
2.	Create a virtual machine
- I am using Windows 10 to gain more experience with the OS (I couldn’t use Windows 11  with my current subscription).
- Minimum specs: 2 vcpus with 8GB of RAM
- Setup the VM firewall.
  - Add a firewall rule that allows all incoming traffic from any port.
  - Set priority to 100.
- Finish the build.
- It takes a while to finish building the virtual machine.
3.	RDP into your VM.
- Find your machine’s IP and RDP into it.
- Say no to all the privacy settings. They are just trying to pull information off of you.
- Open Edge.
4.	Open Event Viewer on the Windows Device.
- Go to “Windows Logs” and click on the “Security” subfolder.
- Try to fail logging on to the VM through RDP so an event pops up.
5.	Ping your VM’s IP from your personal PC to show timestamp.
- Example (actual IP redacted)
  - ping 123.34.67.89 -t
- As you can see from the example, your VM is hiding timestamp information.
6.	Open Window’s Firewall
- Type wf.msc in the search box.
- Click on “Windows Defender Firewall Properties”
- Turn off the Firewall on the Domain, Private, and Public Tabs. Click “Apply”.
- When you ping your device again, you can see a response from the VM because turning off the firewall enabled echo time requests.
7.	Create a Log Analytics Workspace (LAW).
- This will be where we store the collected logs for the SIEM tool to analyze.
- Use the same resource group as the virtual machine.
8.	Allow logs from the server to be analyzed by the Log Analytics Workspace.
- Search for Microsoft Defender for Cloud.
- Go to “Environment Settings”
- Find your subscription at the bottom and go to the subcategory where your LAW resides.
- Select your LAW.
- Make sure “Servers” is selected to be “On”. Everything else can be left turned off.
- Click “Save” in the top-middle of the screen.
- Next, select “Data collection” underneath “Azure Defender plans” on the left-hand side of the screen.
- Choose “All Events” to collect data and click “Save” again.
9.	Connect your VM to the LAW.
- Go back to the “Log Analytics workspaces”.
- Click your workspace.
- Find your virtual machine. (it says deprecated now)
- Click your machine and then select “Connect” at the top.
10.	Set up the SIEM, Microsoft Sentinel connection to the VM.
- Create Sentinel.
- Choose the LAW to work with and click “Add”.
- Once that’s done, go to “Content Management” to “Content Hub”.
- Go to Window’s Security Events and click “Install”.
- Once everything is done installing, click on Windows Security Events and then select “Manage”.
- Select “Windows Security Events via AMA” and then click “Open Connector page”.
- Click “+Create data collection rule”.
- Enter subscription information. Click, next.
  - Choose the VM you want to connect.
- Collect all security events.
- Finish creating data collection rule.
- Once finished, go to VMs. Select your VM, go to “Settings”, find “Extensions + applications”, and you will see an Azure Monitoring Connector.
11.	Query Log Repository
- Go to the “Logs” in LAW
- Go to the KQL page and query “SecurityEvents” table.
  - Example KQL Query
  SecurityEvent
  | where Account == “User”
  | project TimeGenerated, Account, Computer, EventID
  - This queries all data from the SecurityEvent table, wherever the Account field equals “User”, and project is used to show only the columns I listed afterwards.
13.	Upload Geolocation data to Sentinel One.
- I have a .csv file that maps IP ranges to locations throughout the world.
- Select your workspace on Sentinel and under “Configuration”, select “Watchlist”.
- Create a new Watchlist.
- Make the name and alias the same.
- Choose your .csv and then make the “SearchKey” “network”. That is our primary key.
- Create the Watchlist.
- Click on your newly created watchlist. It should show how many items are on it (this could take some time). This is another Table added to your LAW. You can query the Security Events table and call the watchlist to produce your desired information.
- Example:
  - SecurityEvent
_GetWatchlist(“geoip”)
14.	Create an Attack Map workbook in SentinelOne.
- Go to “Threat Management” and select “Workbooks”.
- Click “Add Workbook” and then “Edit”.
- Remove all the pre-populated clutter.
- Find a JSON world map online. 
- “Add query” in the workbook and select “Advanced Editor”.
- Remove all content and paste your json. Click “Done Editing” when finished.
- Save your workbook.

## Finished Product
See attached documents to view the attack map after 24 hours of nonstop RDP Brute Force Attacks and where they came from. Also, see resources used.
