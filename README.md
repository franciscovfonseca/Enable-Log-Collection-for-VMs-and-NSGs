<br>

<h1 align="center">Enable Log Collection for Virtual Machines and NSGs</h1>

<p align="center">
<img width="1000" src="https://github.com/user-attachments/assets/280173e7-0286-41a9-b5bb-143a93366dfe" alt="Banner"/>
<br />

<br />

In this lab we're going to Enable Microsoft Defender for Cloud.

Microsoft Defender for Cloud gives us a highlevel view of our Azure Environment in terms of Security and Secure Score.

It also allows us to take Logs from Virtual Machines and NSGs and ingest them into our Log Analytics Workspace.

So that's what we will be doing in this lab ➜ Set Up Microsoft Defender for Cloud.

<br />

<details close> 
<summary> <h2> 1️⃣ Enable Microsoft Defender for Cloud for Log Analytics Workspace</h2> </summary>
<br>

> First thing we're going to do is Enable Defender for Cloud for our Log Analytics Workspace.
> 
> This will allow us to forward Logs from the Virtual Machines into the LAW.

<br>

Got to the **Azure Portal** ➜ search for **"Defender for Cloud***:

![azure portal](https://github.com/user-attachments/assets/42c1fe46-b2c3-4330-8a86-bd32748cb890)

Then we'll click on **"Environment settings"**:

![azure portal](https://github.com/user-attachments/assets/041b5689-7033-40f6-97be-e4d955a2bf22)

Expand the ```∨``` next to **"Azure subscription 1"**

All the way on the right side for the **"LAW--Cyber-Lab-01"** line ➜ click on ```...``` ➜ and then **"Edit settings"**:

![azure portal](https://github.com/user-attachments/assets/1a969a7f-8ae6-4536-849e-6e190365d2ba)

First we'll go to the **"Defender plans"** tab.

We'll **Turn On** MDC for **"Servers"** and also for **"SQL servers on machines"** ➜ since we do have a SQL Server instance.

<br>

>   <details close> 
>   
> **<summary> 💡 </summary>**
> 
> This will allow us to **Collect Logs** from these resources.
> 
>   </details>

<br>

Make sure you click the 💾 **Save** button:

![azure portal](https://github.com/user-attachments/assets/ec89cb13-f9b6-4c74-8d13-52ca62d433f0)

Then we'll go to the **"Data collection"** tab ➜ and check **"All Events"**

<br>

>   <details close> 
>   
> **<summary> 💡 </summary>**
> 
> This will allow us to **Collect All Events from the Windows Security Log**.
> 
> Which is like the **Windows Event Viewer**, where we can see people attempting to **Log Into our Windows Machines over the Internet**.
> 
> We're going to be able to **Collect Logs** from that and then **Forward Them to this Log Analytics Workspace**.
> 
>   </details>

<br>

Again ➜ click the 💾 **Save** button:

![azure portal](https://github.com/user-attachments/assets/f6391fa7-b49e-4a7e-a91d-f8da0de362d4)

  </details>

<h2></h2>

<details close> 
<summary> <h2>2️⃣ Enable Microsoft Defender for Cloud for the Subscription</h2> </summary>
<br>

Go back to **MDC** ➜ **"Environment settings"** blade.

This time for the **"Azure subsription 1"** line ➜ click on ```...``` ➜ and then **"Edit settings"**:

![azure portal](https://github.com/user-attachments/assets/948dd0ed-985f-44ae-bf88-d5dce29a8a42)

Now we'll **Turn On** MDC for **"Servers"**, **"Databases"**, **"Storage"** & **"Key Vault"**.

>   <details close> 
>   
> **<summary> 💡 </summary>**
> 
> We'll create a **Storage Account** and **Key Vault** instances in a subsequent lab.
> 
>   </details>

Then click on **"Settings >"** under **"Monitoring coverage"** for the **Servers**:

![azure portal](https://github.com/user-attachments/assets/981ad39b-3c2c-487d-8972-5eef50ad7289)

We'll now click on **"Edit configuration"** for the **Log Analytics agent**.

And for the **Workspace** ➜ pick our actual ```LAW-Cyber-Lab-01``` workspace

>   <details close> 
>   
> **<summary> 💡 </summary>**
> 
> We don't want it to automatically create a new LAW ➜ we want to use the one we created.
> 
> We're basically onboarding our Virtual Machines to our LAW and then forward the Logs to it
> 
>   </details>

Make sure you click **"Apply"** and then 💾 **Continue**:

![azure portal](https://github.com/user-attachments/assets/c721c91e-0cb0-4ac9-bc16-cf1dc562007c)

After that we'll click on the 💾 **Save** button to save the **Defender plans for the Subscription**:

![azure portal](https://github.com/user-attachments/assets/cf432e44-419e-4bc9-88fd-e0bfe5bd11f9)

  </details>

<h2></h2>

<details close> 
<summary> <h2>3️⃣ Enable Microsoft Defender for Cloud Continuous Export in Environment Settings</h2> </summary>
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

 
