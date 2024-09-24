<br>

<h1 align="center">Enable Log Collection for Virtual Machines and NSGs</h1>

<br>

<p align="center">
<img width="1000" src="https://github.com/user-attachments/assets/6b229d25-baf9-46a8-8333-449fbc55397e" alt="Banner"/>
<br />

<br />

In this lab we're going to finish the configuration to allow our Logs from the Virtual Machines to be sent into our Log Analytics Workspace, along with the actual Network Security Groups.

After we make the necessary configurations, we'll **Query our LAW (using KQL)** for the Linux Logs, the Windows Logs as well as the NSG Logs.

This way we'll make sure that all of our Logs are coming into our Central Repository.

<br />

<details close> 
<summary> <h2> 1️⃣ Create an Azure Storage Account</h2> </summary>
<br>

>   <details close> 
>   
> **<summary> 📝 Explanation</summary>**
> 
> The first thing we're going to do is Create an Azure Storage Account.
> 
> You can think about this as an ***Enterprise Dropbox or Google Drive*** ➜ it's just a place where you can store files.
>
> It does offer a lot more functionallity and features than just a normal "Consumer Dropbox".
>
> Azure requires this Storage Account to be set up for our NSG Flow Logs to be recorded.
>
> A Network Security Group is essentially a Firewall that "seats" in front of the Virtual Machine.
>
> And we can create what's called a **"Flow Log"** which will keep track of all of the traffic coming in and going out through this "Firewall".
>
> It can Log Mallicious Traffic and different traffic types.
>
> And so we need to Create the Storage Account where those Logs will be stored in an intermediary basis ➜ it's just something required by Azure.
>
>   </details>

<br>

We will go to our **Azure Portal** ➜ search for **Storage Account** ➜ and click **"Create storage account"**

![azure portal](https://github.com/user-attachments/assets/5efd94ea-5bb1-440e-af0a-cb50d46a19ed)

You can set it up with this details (or similar if applicable):
- **Resource group**: ```RG-Cyber-Lab```
- **Storage account name**: ```sacyberlab999``` ➜ it has to be globally unique
- **Region**: ```East US 2``` ➜ ⚠️ make sure you put it in the **Same Region as you VMs**, otherwise it won't work!
- Leave everything else as it is

Click **"Create"**:

![azure portal](https://github.com/user-attachments/assets/5e290340-27f6-43d2-bf13-afba7cefeeaf)

💡 Again, this will be used to store what's called the **"NSG Flow Logs"** ➜ there're essentially Logs from the Firewalls.

<br>

  </details>

<h2></h2>

<details close> 
<summary> <h2>2️⃣ Enable Flow Logs for both Network Security Groups</h2> </summary>
<br>

> If you remember we have 2 NSGs ➜ 1 on the Windows Vm & 1 on the Linux VM.
> 
> So now we'll Enable Flow Logs for both of them.

<br>

We'll just go to **"Network security groups"** in the **Azure Portal**.

And then we'll first click on the **"windows-vm-nsg"**:

![azure portal](https://github.com/user-attachments/assets/f18036aa-2c1c-450d-8f6e-41ff6d1e7c26)

Click on the **"NSG flow logs"** blade ➜ and then the **"Create flow log"** button:

![azure portal](https://github.com/user-attachments/assets/796cb1b1-b16b-46c8-9aca-75487bd045bc)

We can actually create the Flow Log Settings for both of the NSGs' Flow Logs at the same time.

To do so, we'll click on ➕ **Select resource** ➜ and after we'll select ☑️ both the **windows-vm-nsg** and the **linux-vm-nsg**:

![azure portal](https://github.com/user-attachments/assets/9d3c3760-6414-4ba6-b16f-6812647fb6be)

- We'll select the Storage Acount ```sacyberlab999``` we just created

- And we'll set the Retention to **"0 days"** ➜ meaning the data will be retained indefinitely:

![azure portal](https://github.com/user-attachments/assets/b2194b97-aef5-49ad-b9bc-47decc77fdaf)

For the **Analytics** tab:
- **Flow Logs Version**: ```⦿ Version 2```
- Check ☑️ **Enable Traffic Analytics**
  
  - **Traffic Analytics processing interval**: ```Every 10 mins```
  - **Log Analytics Workspace**: ```LAW-Cyber-Lab-01``` ➜ ⚠️ make sure to the Flows Logs are going to your own LAW!

<br>

>   <details close> 
>   
> **<summary> 💡 Traffic Analytics</summary>**
> 
> This is where **Microsoft Defender for Cloud** will **Analyse the Traffic** and it will determine which traffic is **Malicious** and which one is **Benign**.
> 
> MDC has different categorizations for the traffic ➜ and so we'll use Traffic Analysis later to plot on a **World Map**.
>  
>   </details>

<br>

Then you can click **"Review + create"** to create our Flow Logs:

![azure portal](https://github.com/user-attachments/assets/05dddcb8-7266-4ef6-8add-372c928a8a2b)

  </details>

<h2></h2>

<details close> 
<summary> <h2>3️⃣ Configure Data Collection Rules within our Log Analytics Workspace</h2> </summary>
<br>

>   <details close> 
>   
> **<summary> 📝 Explanation</summary>**
> 
> The next thing we're going to do is Configure Data Collection Rules for our Virtual Machines.
> 
> The Data Collection Rules will work in conjunction with Defender for Cloud and the Agents that get installed on the VMs.
>
> They work to specify which Logs from the VMs to forward to the Log Analytics Workspace.
> 
> If you remember ➜ in the Windows VM there were the System Logs, the Security Logs, the Application Logs, and a whole bunch of other Logs.
> 
> The Data Collection Rules allow us to define which ones of those Logs get forwarded to the LAW.
> 
> We don't want to forward everything ➜ because it will end up costing more than it should.
>
>   </details>

<br>

To **Configure Data Collection Rules (DCR)** we can go to our Log Analytics Workspace ```LAW-Cyber-Lab-01```

We'll click on the **"Agents"** blade ➜ and the on the **"Data Collection Rules"** Button:

![azure portal](https://github.com/user-attachments/assets/1a684f65-750d-458a-981d-7f5d7bee42f1)

Then click on ➕ **Create** to create a new DCR:

![azure portal](https://github.com/user-attachments/assets/e13bb6ac-9ece-4108-bdcc-0fd7d3386a00)

We'll create it with the following details:
- **Rule Name**: ```dcr-all-vms``` ➜ 💡 this stands for 1 single DCR that will apply to all of our VMs
- **Resource group**: ```RG-Cyber-Lab```
- **Region**: ```East US 2``` ➜ ⚠️ again, make sure you put it in the **Same Region as you VMs**, otherwise it won't work!
- **Platform Type**: ⦿ **All**

Then click "Next" to reach the **Resources** tab:

![azure portal](https://github.com/user-attachments/assets/010af302-782a-4c56-9a0f-80ca5574ded1)

For the Resources we're going to ➕ **Add Resources**:

![azure portal](https://github.com/user-attachments/assets/45dfe720-c3fc-491a-9bef-7eaf3997f227)

Now expand the **> RG-Cyber Lab** Resource Group ➜ and select ☑️ for both of our VMs ➜ click **"Apply"**:

![azure portal](https://github.com/user-attachments/assets/dbf4b74a-94ae-4b99-8692-9566f2952cc0)

Click **"Next: Collect and deliver >"**

💡 This is where we'll specify which Logs from within the VMs we're going to collect.

So we'll click on the ➕ **Add data source** button:

![azure portal](https://github.com/user-attachments/assets/a302a685-643c-468d-9d47-44042b329685)

We'll do the **"Linux Syslog"** first.

![azure portal](https://github.com/user-attachments/assets/29944fe1-85ea-4c4a-b1bf-2b50e6325277)

In this Lab, the only Logs we're going to Collect from the Linux VM are the ```LOG_AUTH``` Logs.

>   <details close> 
>   
> **<summary> 💡 </summary>**
>   
> The AUTH LOGS are the Logs we inspected earlier ➜ where we saw all the **SSH Failure Attempts**.
> 
> For the data source settings, when selecting the **"Minimum log level"** ➜ this signifies the level of logging we want to collect:
> 
> DEBUG essentially means "collect everything", and then in kind of scales down towards EMERG which means "only collect critical logs".
> 
>   </details>

So for **LOG_AUTH** ➜ leave the **"Minimum log level"** at ```LOG_DEBUG``` ➜ meaning we'll collect all the Auth Logs.

And then we'll select ```LOG_DEBUG``` for the rest of the Log Types.

After setting that up, click on the **"Next : Destination"** button:

![azure portal](https://github.com/user-attachments/assets/9c54a7ba-a867-468b-b987-7ba980c2c851)

For **Destination** ➜ make sure you're sending the data to your actual **LAW**, not the random one that was created! ⚠️

Click **"Add data source"**:

![azure portal](https://github.com/user-attachments/assets/07a7a358-78fa-4e5a-b6da-aa19b4b18e6b)

Now click on the ➕ **Add data source** button again:

The next one is going to be **"Windows Event Logs"**.

>   <details close> 
>   
> **<summary> 💡 </summary>**
>   
> This might be hard to remember, but back when we were configuring SQL logging for the SQL Server Database ➜  the SQL Logs appeared on the Application Event Log ➜ under Information
> 
> And then for Security ➜  Audit Success & Audit Failure ➜  this is when someone tries to Remote Desktop into our Windows VM or tries to Map a File Share.
> 
>   </details>

We're going to select the ☑️ **Information** Logs from **"Application"** type.

And also select the ☑️ **Audit Success** & ☑️ **Audit Failure** Logs from the **"Security"** type.

This is all we need for the Windows Event Logs, so then we'll click on **"Next : Destination"**:

![azure portal](https://github.com/user-attachments/assets/a178e78d-c2ad-49f2-897d-b32724a8bcfd)

Again ➜ for **Destination** ➜ we need to make sure we put our actual LAW ```LAW-Cyber-Lab-01```.

Then Add the data source:

![azure portal](https://github.com/user-attachments/assets/294f285c-d6a4-4365-b131-c1631a60fcb8)

We can now **"Review + create"** to Create the Data Collection Rule:

![azure portal](https://github.com/user-attachments/assets/1c17ac54-9730-4642-bafd-dbae067b348f)

  </details>

<h2></h2>

<details close> 
<summary> <h2>4️⃣ Configure Special Windows Event Data Collection (Defender and Windows Firewall)</h2> </summary>
<br>

>   <details close> 
>   
> **<summary> 💡 Summary</summary>**
>   
> Next we're going to add a Special Data Sources to our Data Collection Rule.
>   
> It will Log whenever somebody messes with the Windows Firewall ➜ like if they Turn Off the Firewall
> 
> And also when Malware is discovered in the Virtual Machines ➜ it will create and pull those Logs out as well.
> 
>   </details>

<br>

We'll go back to our **Log Analytics Wokspace** ➜ click on the **"Agents"** blade ➜ and then on the **"Data Collection Rules"** Button:

![azure portal](https://github.com/user-attachments/assets/052dde4c-bd0c-4495-a75f-499981469cb0)

Click on the Data Collection Rule that we just created ➜ ```dcr-all-vms```

![azure portal](https://github.com/user-attachments/assets/990345e8-e780-46f5-bfa1-f885f7fccc8f)

Then we'll go to the **"Data Sources"** blade ➜ and click on the **"Windows Event Logs"** Data Source:

![azure portal](https://github.com/user-attachments/assets/a07b10c1-bb68-455a-9b70-ccf1870af142)

This time instead of **"Basic"** we'll go to **"Custom"**.

<br>

>   <details close> 
>   
> **<summary> 📝 Explanation</summary>**
>   
> We can see in the image below the XPath queries that we previously selected ➜ under **Event Logs**.
>   
> Think of an XPath query as Microsoft's "convention" for specifying which Logs (Application & Security in this case) and which "Sub-Logs" inside of those two we want to capture.
> 
> So in order for us to Collect Logs from the Firewall, as well as the actual Defender Anti-Malware on the Virtual Machines ➜ we have to use this XPath syntax convention to specify which Logs to capture.
> 
>   </details>

<br>

![azure portal](https://github.com/user-attachments/assets/060a1a2d-865a-48e7-b6f0-344431c85332)

We want to configure our **Data Collection Rule** so that:

1. If Malware is discovered ➜ a Log is created and it's forwarded into our Log Analytics Workspace:

Copy the following **Windows Defender Malware Detection XPath Query**.

```commandline
Microsoft-Windows-Windows Defender/Operational!*[System[(EventID=1116 or EventID=1117)]]
```
<br>

And now add it to the **Add Data Source Section**:

![azure portal](https://github.com/user-attachments/assets/83506e71-c0e7-4e06-a98b-276e4cb58c89)

2. Also if the Firewall is disabled or messed with ➜ we want the Firewall Logs to be forwarded to our Log Analytics Workspace as well:

Copy the following **Windows Firewall Tampering Detection XPath Query**.

```commandline
Microsoft-Windows-Windows Firewall With Advanced Security/Firewall!*[System[(EventID=2003)]]
```
<br>

Again, we'll add it to the **Add Data Source Section**:

![azure portal](https://github.com/user-attachments/assets/f23a39c8-5c61-4394-83fc-262ad6a15dd3)

Click **"Save"** and we've successfully configured our Data Collection Rule with "Special Forwarding" ✅

<br>

  </details>

<h2></h2>

<details close> 
<summary> <h2>5️⃣ Manually Install the Log Analytics Agent on the Windows VM</h2> </summary>
<br>

>   <details close> 
>   
> **<summary> 💡 Summary</summary>**
>   
> Defender for Cloud in Azure should automatically install the necessary agent on both Virtual Machines to allow the Logs to be forwarded.
> 
> Basically the agent will work in conjunction with the Data Collection rules to pick which Logs to forward, and then it will ultimately forward them into the Log analytics Workspace.
> 
> But we can manually install the Agent on the Virtual Machines just to make sure it is indeed there and it is forwarding the Logs as it should.
> 
> So we're just going to do that in this section of the lab.
> 
>   </details>

<br>

Back to our **Log Analytics Wokspace** ➜ click on the **"Agents"** blade

We're first going to Install the Agent on the ```windows-vm``` ➜ so under the **"`Windows servers"** tab ➜ expand the **"Log Analytics agent instructions"**:

![azure portal](https://github.com/user-attachments/assets/51c87b0a-aad5-4daa-aa85-103770ba116d)

>   <details close> 
>   
> **<summary> 📝 Explanation</summary>**
>   
> Basically we're going to log into our Windows Vm and then install the ***Windows Agent (64 bit)***.
> 
> We'll then use the **Workspace ID** & **Primary Key** to force the Agent to point back to our Log Analytics Workspace and Forward the Logs to it.
> 
>   </details>

<br>

So let's Connect to the **Windows Vm** ➜ open **Microsoft Remote Desktop** ➜ and then connect with the VM's Public IP Address (which we've done many times before in previous labs):

![azure portal](https://github.com/user-attachments/assets/19dcdcc3-563e-4d32-9624-052fc3934d89)

Use your credentials:
- **Username**: ```labuser```
- **Password**: ```Cyberlab123!```

![azure portal](https://github.com/user-attachments/assets/3f316622-9025-4bf9-aa00-50dc05018c6b)

Once connected to the Windows Vm ➜ go back to the Azure Portal on your Computer and copy the "Download link" to **Install the Agent on the Windows VM**.

![azure portal](https://github.com/user-attachments/assets/55410b99-a755-442d-9c1a-b179dfcc8959)

Now in the Windows Vm ➜ open up **"Edge"** ➜ Paste the Link & Download the Agent:

![azure portal](https://github.com/user-attachments/assets/737fce37-39b2-4612-bdde-088b625550cb)

You can Open the File ➜ and click **"Next"** until you reach the section where you want to select:

```☑️ Connect the agent to Azure Log Analytics (OMS)```

![azure portal](https://github.com/user-attachments/assets/cfb6d974-420d-47b1-a745-d4c489bd29e3)

In the next section ➜ Paste the **Workspace ID** & **Workspace Key** you Copied from the Azure Portal back in your Computer:

![azure portal](https://github.com/user-attachments/assets/06e2ef7a-f4aa-48e1-88d3-57c9ac7aa934)

⚠️ Make sure **"Azure Cloud"** is set to ```Azure Commercial```

Click **"Next and then **"Install"**:

![azure portal](https://github.com/user-attachments/assets/f8681207-f4a9-4025-ac69-510569c744b8)

Once the Agent has finished Installing ➜ still inside the **Windows VM** ➜ open **Control Panel**:

![azure portal](https://github.com/user-attachments/assets/53416129-0420-4b7f-b4bf-9228168ff48c)

Click on **"Large Icons"** ➜ and then **"Microsoft Monitoring Agent"**:

![azure portal](https://github.com/user-attachments/assets/0c0d97be-868d-4c8f-9a23-6518764288c4)

Then if we go to the **"Azure Log Analytics (OMS)"** tab ➜ and we should see:

- The Workspace ID we pasted earlier

- The status: ```✅ The Microsoft Monitoring Agent has successfully connected to the Microsoft Operations Management Suite service.```

![azure portal](https://github.com/user-attachments/assets/e9fafa49-dcd1-43dc-b1dd-6f46cc02d672)

This signifies that the Connection is Successfull and so it should be Forwarding Logs into our LAW.

✅ So we're done with this Windows VM portion.

<br>

  </details>

<h2></h2>

<details close> 
<summary> <h2>6️⃣ Manually Install the Log Analytics Agent on the Linux VM</h2> </summary>
<br>

> The Next thing we're going to do is do same thing as previously, but this time for the Linux Virtual Machine.
> 
> Basically in order to Install the Agent on the Linux ➜ we need to SSH into our Linux VM and Paste a command in there.

<br>

Still in the **Azure Portal** ➜ we'll go to our **linux-vm**  ➜ and copy its **Public IP Address**:

![azure portal](https://github.com/user-attachments/assets/110a23e4-4b37-4baa-9a62-b40fbd698931)

![azure portal](https://github.com/user-attachments/assets/e44a1421-0558-4b83-8321-ef8b249f0ced)

- Then if you're on **Windows** ➜ open **Powershell**

- But if you're on **Mac**➜ open **Terminal**

And now we'll **SSH into our Linux VM** ➜ so type ```ssh USERNAME@LINUX-VM IP ADDRESS```

![azure portal](https://github.com/user-attachments/assets/1c548eab-261b-4307-bf38-b1bf020f247d)

It'll ask for the **Password** so just type it in (```Cyberlab123!```):

![azure portal](https://github.com/user-attachments/assets/9964c04e-50b7-483b-b82f-f92274a3e85b)

You'll know you're **logged in** when your prompt changes to something like this ```labuser@linux-vm```:

![azure portal](https://github.com/user-attachments/assets/667e9cdf-2ee1-455f-8fc4-d04ec5a46e98)

<br>

<h2></h2>

<br>

Now to Install the Log Analytics Agent on the Linux VM  ➜ we'll go back to the **"Agents"** blade in our **LAW**

Click on the **"Linux Server"** tab ➜ and expand the **"Log Analytics agent instructions"**:

![azure portal](https://github.com/user-attachments/assets/81a652c9-2c1c-493f-8dd8-89b151af2ad1)

Copy the Command Line under ***"Download and onboard agent for Linux"***:

![azure portal](https://github.com/user-attachments/assets/8897b4c9-c21a-4f78-b44a-5751c3933180)

Go back to the **Terminal App** ➜ **Paste the Command** ➜ and **Run It** (Press Enter):

This will:
- Download the Script
- Execute the Script
- Pass the Parameters, which is our ➜ Log Analytics **Workspace ID**, as well as our **Primary Key**
- And then it'll also define the Endpoint that Ingests the Logs.

![azure portal](https://github.com/user-attachments/assets/0fdb4693-ca42-4b20-b989-d973e3aba495)

It should now install the **Log Analytics Agent** locally with a ```status code 0``` at the end:

We can then just ```exit``` the SSH connection to our **Linux VM**:

![azure portal](https://github.com/user-attachments/assets/699f0f56-8962-4e2e-b413-24c1e7dc8735)

Now we'll go back to the **Azure Portal** ➜ inside of our **Log Analytics Wokspace** ➜ click on the **"Agents"** blade again.

On the VM's tabs we can check that both the **Log Analytics Agents were Successfully Installed** ✔️

![azure portal](https://github.com/user-attachments/assets/964d12e6-b19e-4311-a86b-90d758788d43)


✅ We should be able to start **Querying the Logs** directly inside of **Log Analytics Workspace**.

<br>

  </details>

<h2></h2>

<details close> 
<summary> <h2>7️⃣ Query the Log Analytics Workspace for our VM Logs & NSG Logs</h2> </summary>
<br>

> The next thing we're going to do, before we finish this lab up, is Query the LAW for our VM Logs as well as our NSG Logs.
> 
> We can't move on to the next Lab until we actually start seeing those Logs showing up in there.
>
> So let's just start doing that and hopefully they start showing up.

<br>

Back to the **Azure Portal** and to our **Log analytics Workspace** ➜ on the left we'll click on the **"Logs"** blade.

💡 This is where we can start **Querying the Logs**:

![azure portal](https://github.com/user-attachments/assets/ba557976-60b7-4299-9113-cd628e562950)

>   <details close> 
>   
> **<summary> 📝 Explanation</summary>**
> 
> Basically this is where we can practice **KQL** (Kusto Query Language) ➜ which is similar to **SQL** (Structured Query Language).
> 
> Azure will create what's called a **Table**.
> 
> You can think of a **Table** as something similar to an Excel Spreadsheet, but in this case it's like a Database to Store our Logs.
> 
>   </details>

<br>

To make sure the Logs are coming in from all 3 sources, we'll Query the different **Tables**.

1. The Table that is used to store the **Linux Logs** is called **Syslog**.

We should be able to type ```Syslog``` ➜ and click the ▶️ **"Run"** Button to Query the Syslog Log:

![azure portal](https://github.com/user-attachments/assets/cbb34900-5811-4498-b23d-a75178bb0a11)

✅ Looks like the **Linux VM Logs** are actually coming in ➜ so we know it's working.

<br>

<h2></h2>

<br>

2. The Table (aka the Excel Spreadsheet) that is used to hold the Logs from our Windows VMs is called **SecurityEvent**

So next we're going to inspect the ```SecurityEvent``` Logs:

![azure portal](https://github.com/user-attachments/assets/9b4f4b61-932e-4beb-86e3-cb002925badc)

✅ We can see the SecurityEvent Logs are coming in ➜ which is great!

<br>

<h2></h2>

<br>

3. The last Table is used to hold the NSG Logs ➜ **AzureNetworkAnalytics_CL**

Again ➜ inside the LAW's **"Logs"** Blade ➜ Paste the command ```AzureNetworkAnalytics_CL``` and Run the Query:

![azure portal](https://github.com/user-attachments/assets/b8bf7908-0bf0-484e-b2ca-47aa5b9731de)

✅ And we can see that the NSG Logs did come in.

<br>

<h2></h2>

  </details>

<br>

<br>

<br>

# Summary

<br>

Just to recap, in this lab we:

1. Created an **Azure Storage Account** for the Network Security Groups’ Flow Logs.

2. Enabled **NSG Flow Logs**.

3. Created **Data Collection Rules** to specify which Logs to collect from our Windows and Linux VMs.

4. Manually installed the **Log Analytics Agent** locally on the Virtual Machines.

5. **ueried the Log Analytics Workspace** for our VMs Logs and for our NSGs Logs ➜ to see if they were showing up there.

<br>

After completing this lab, we are now successfully sending Logs from both the VMs and the NSGs into the Log Analytics Workspace.

This is why we can Query things like Syslog and see all the Linux VM Logs.

In subsequent labs we’ll do the same thing for our other resources as well as for our **Microsoft Entra ID Logs**.


<br />

<br />

<br />  

<br /> 

<br />

<br />  

<br /> 

<br />

<br />

 
