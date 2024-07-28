<br>

<h1 align="center">Enable Log Collection for Virtual Machines and NSGs</h1>

<br>

<p align="center">
<img width="1000" src="https://github.com/user-attachments/assets/6b229d25-baf9-46a8-8333-449fbc55397e" alt="Banner"/>
<br />

<br />

In this lab we're going to finish the configuration to allow our Logs from the Virtual Machines to be sent into our Log Analytics Workspace., along with the actual Network Security groups.

After we make the necessary configurations, we'll Query our LAW for the Linux Logs, the Windows Logs as well as the NSG Logs.

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

![azure portal](https://github.com/user-attachments/assets/42c1fe46-b2c3-4330-8a86-bd32748cb890)

You can set it up with this details (or similar if applicable):
- **Resource group**: ```RG-Cyber-Lab```
- **Storage account name**: ```sacyberlab999``` ➜ it has to be globally unique
- **Region**: ```East US 2``` ➜ ⚠️ make sure you put it in the **Same Region as you VMs**, otherwise it won't work!
- Leave everything else as it is

Click **"Create"**:

![azure portal](https://github.com/user-attachments/assets/42c1fe46-b2c3-4330-8a86-bd32748cb890)

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

![azure portal](https://github.com/user-attachments/assets/42c1fe46-b2c3-4330-8a86-bd32748cb890)

Click on the **"NSG flow logs"** blade ➜ and then the **"Create flow log"** button:

![azure portal](https://github.com/user-attachments/assets/42c1fe46-b2c3-4330-8a86-bd32748cb890)

We can actually create the Flow Log Settings for both of the NSGs' Flow Logs at the same time.

To do so, we'll click on ➕ **Select resource** ➜ and after we'll select ☑️ both the **windows-vm-nsg** and the **linux-vm-nsg**:

![azure portal](https://github.com/user-attachments/assets/42c1fe46-b2c3-4330-8a86-bd32748cb890)

- We'll select the Storage Acount ```sacyberlab999``` we just created

- And we'll set the Retention to **"0 days"** ➜ meaning the data will be retained indefinitely:

![azure portal](https://github.com/user-attachments/assets/42c1fe46-b2c3-4330-8a86-bd32748cb890)

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

![azure portal](https://github.com/user-attachments/assets/42c1fe46-b2c3-4330-8a86-bd32748cb890)

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

![azure portal](https://github.com/user-attachments/assets/42c1fe46-b2c3-4330-8a86-bd32748cb890)

Then click on ➕ **Create** to create a new DCR:

![azure portal](https://github.com/user-attachments/assets/42c1fe46-b2c3-4330-8a86-bd32748cb890)

We'll create it with the following details:
- **Rule Name**: ```dcr-all-vms``` ➜ 💡 this stands for 1 single DCR that will apply to all of our VMs
- **Resource group**: ```RG-Cyber-Lab```
- **Region**: ```East US 2``` ➜ ⚠️ again, make sure you put it in the **Same Region as you VMs**, otherwise it won't work!
- **Platform Type**: ⦿ **All**

Then click "Next" to reach the **Resources** tab:

![azure portal](https://github.com/user-attachments/assets/42c1fe46-b2c3-4330-8a86-bd32748cb890)

For the Resources we're going to ➕ **Add Resources**:

![azure portal](https://github.com/user-attachments/assets/42c1fe46-b2c3-4330-8a86-bd32748cb890)

Now expand the **> RG-Cyber Lab** Resource Group ➜ and select ☑️ for both of our VMs ➜ click **"Apply"**:

![azure portal](https://github.com/user-attachments/assets/42c1fe46-b2c3-4330-8a86-bd32748cb890)

Click **"Next: Collect and deliver >"**

💡 This is where we'll specify which Logs from within the VMs we're going to collect.

So we'll click on the ➕ **Add data source** button:

![azure portal](https://github.com/user-attachments/assets/42c1fe46-b2c3-4330-8a86-bd32748cb890)

We'll do the **"Linux Syslog"** first.

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

![azure portal](https://github.com/user-attachments/assets/42c1fe46-b2c3-4330-8a86-bd32748cb890)

For **Destination** ➜ make sure you're sending the data to your actual **LAW**, not the random one that was created! ⚠️

Click **"Add data source"**:

![azure portal](https://github.com/user-attachments/assets/42c1fe46-b2c3-4330-8a86-bd32748cb890)

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

![azure portal](https://github.com/user-attachments/assets/42c1fe46-b2c3-4330-8a86-bd32748cb890)

Again ➜ for **Destination** ➜ we need to make sure we put our actual LAW ```LAW-Cyber-Lab-01```.

Then Add the data source:

![azure portal](https://github.com/user-attachments/assets/42c1fe46-b2c3-4330-8a86-bd32748cb890)

We can now **"Review + create"** to Create the Data Collection Rule:

![azure portal](https://github.com/user-attachments/assets/42c1fe46-b2c3-4330-8a86-bd32748cb890)




<br>

<br>

<br>

<br>

<br>








Still inside the **"Edit settings"** for the Subscription ➜ we'll go to the **"Continous export"** blade now.

Click on the **"Log Analytics workspace"** tab ➜ and make sure **"Export enabled"** is **Turned On**:

>   <details close> 
>   
> **<summary> 💡 </summary>**
> 
> Doing this will **Export Alerts into our LAW** so we can **Query Them Later**.
> 
> So if **Defender for Cloud** discovers there's some problem with our Environment, like a **Brute-Force Attack** going on, or there's a **Poor Configuration** for example ➜ MDC will **Export those Alerts into our LAW** ➜ which will let us **Query Them Later**
> 
>   </details>

![azure portal](https://github.com/user-attachments/assets/6935cd85-97d1-4b21-a86f-f2b7c691be9a)

So we'll enable ☑️ **Exported data types** for all of the following options:

>   <details close> 
>   
> **<summary> 💡 </summary>**
> 
> We haven't actually configured **"Regulatory compliance"** yet, but we'll do that in a future lab.
> 
> This will basically enable **NIST 800-53** for our Environment to see what controls are missing in certain areas.
> 
>   </details>

![azure portal](https://github.com/user-attachments/assets/c35fbae6-732e-440c-bcfa-d2ea1ddcd9d5)

For the **"Export configuration"** option let's just configure it to our **Resource group** ```RG-Cyber-Lab```

And also for the **"Export target"** we'll select our **Target Workspace** ```LAW-Cyber-Lab-01```

>   <details close> 
>   
> **<summary> 💡 </summary>**
> 
> This is the target workspace where we want to **Export the Alerts to** ➜ so we have to select our **LAW**.
> 
>   </details>

We'll then click 💾 **Save**

![azure portal](https://github.com/user-attachments/assets/3ac116e8-151e-43f3-9a84-68a5216189c3)

<h2></h2>

  </details>

<br>

<br>

<br>

# Summary

<br>

That wraps things up for this lab.

We just enabled **Microsoft Defender for Cloud**, which can show us our Security Posture and gives us Insights into a lot of things.

Primarily it will allow us to take **Logs from our Virtual Machines** and **Forward them into our Log Analyitcs Workspace**.

We also enabled Defender for Cloud for Storage Account & Key Vault which we will Set Up and Use in future labs.

<br>

>   <details close> 
>   
> **<summary> 💡 Preview</summary>**
> 
> In the next lab we'll continue onboarding our **Virtual Machines & Network Security Groups**.
>   
> Then we're going to **Manually Install the Agent** on them to forward the Logs into our Log Analytics Workspace.
> 
> And then hopefully we'll be able to start actually **Querying the Logs Centrally**.
> 
>   </details>


<br />

<br />

<br />  

<br /> 

<br />

<br />  

<br /> 

<br />

<br />

 
