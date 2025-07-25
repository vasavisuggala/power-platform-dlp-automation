
🚀 Automating DLP Policy Updates in Power Platform: A Developer’s Guide

![Power Automate](https://img.shields.io/badge/Built%20With-Power%20Automate-0078D7?logo=microsoftpowerautomate&logoColor=white)
![Power Platform](https://img.shields.io/badge/Platform-Microsoft%20Power%20Platform-purple?logo=microsoftpowerapps&logoColor=white)
![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)
![Automation Tool](https://img.shields.io/badge/Tool-Type%3A%20Automation-blue)


🔐 **The Challenge**  
In large Power Platform environments, keeping Data Loss Prevention (DLP) policies updated with approved connectors is critical for governance.  

⚠️ The `Update DLP Policy` action in Power Automate replaces the entire policy configuration, not just one group.  

This means:  
- ⚠️ If we call it blindly, existing settings could get **overwritten**.  
- ✅ So, I designed a solution to **fetch the current policy**, merge new approved connectors, and safely update it.  

## 🎯 Goal of the Automation  

✅ Automatically add all **approved connectors** to the **Business group** in an existing DLP policy.  
✅ Ensure:  
- 🔄 **No duplicate connectors**  
- 🔒 Approved connectors in Confidential group are moved to Business.  
- 🚫 Approved connectors are removed from Blocked.  
✅ Maintain **audit logs** for governance transparency.


## 🛠️ Step-by-Step Process  

# 🚀 Start
Trigger: Manual (for testing purposes)

# 📥 Step 1: Fetch Approved Connectors

Initialize Array approvedConnectorList[]
List Rows from Dataverse (Business Team Solution table)
For each Approved Solution:
    Get Related Connectors (Many-to-Many Relationship)
    For each Connector:
        Store DisplayName and InternalID into approvedConnectorList[]

![Manual Trigger and List Rows](https://github.com/vasavisuggala/power-platform-dlp-automation/blob/images/manual-trigger-list-rows.jpeg)

# 📥 Step 2: Fetch Existing DLP Policy
  
Get DLP Policies using 'GET DLP Policies v2'
Filter TargetPolicyName
Fetch Policy Details:
    BusinessGroupConnectors[]
    ConfidentialGroupConnectors[]
    BlockedGroupConnectors[]

![Get DLP Policy](https://github.com/vasavisuggala/power-platform-dlp-automation/blob/images/Get-DLP-Policy.jpeg)

# 🔄 Step 3: Clean & Merge Data
  
For each connector in approvedConnectorList[]:
    IF connector exists in BlockedGroupConnectors[]:
        Remove it from BlockedGroupConnectors[]
    IF connector exists in ConfidentialGroupConnectors[]:
        Remove it from ConfidentialGroupConnectors[]
Merge approvedConnectorList[] with BusinessGroupConnectors[]
Remove duplicates to create FinalBusinessGroupConnectors[]

# 🛠 Step 4: Prepare JSON Payload
  
Create JSON Payload:
[
  { "classification": "Business", "connectors": FinalBusinessGroupConnectors[] },
  { "classification": "Confidential", "connectors": ConfidentialGroupConnectors[] },
  { "classification": "Blocked", "connectors": BlockedGroupConnectors[] }
]

![Connector Groups](https://github.com/vasavisuggala/power-platform-dlp-automation/blob/images/connector-groups.jpeg)

# 🔀 Step 5: Update or Handle Errors
  
Try:
   Call 'Update DLP Policy v2' with Prepared JSON
    Add Record in Audit Log Table
Catch Error:
   Send Email to Admin with
     -failed reason
     -DLP policy name
     -approved connectors list

![Update DLP Policy](https://github.com/vasavisuggala/power-platform-dlp-automation/blob/images/Update-DLP-policy.jpeg)

# ✅ Step 6: End
  
Return Success/Failure Notification

---

## ⚠️ Why Fetch Before Update?
If you skip this step and directly use Update DLP Policy:  
❌ You risk erasing existing configurations.  
✅ Fetching first ensures a **non-destructive update**.  

---

## 🌟 Result  

✔️ DLP Policies updated dynamically.  
✔️ Existing settings fully preserved.  
✔️ No manual intervention needed.  

⭐ If you find this helpful, please star the repo to support it!


📢 **Pro Tip for Developers:**  
Always fetch and merge existing data before calling APIs that overwrite configurations. 

👩‍💻 Author ✍️Developed with ❤️ by *Vasavi Suggala*

💬 **Have you automated your DLP updates yet? What governance workflows are you thinking to streamline?**  

🌟 Like this Script? If you found this script useful, please ⭐ star this repository to support future updates and help others discover it.

#PowerPlatform #PowerAutomate #Governance #DLP #Automation
