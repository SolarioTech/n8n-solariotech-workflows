# System Configuration Upgrade & Inventory Management Automation

A comprehensive n8n workflow automation that manages employee system upgrade requests, approval workflows, inventory tracking, and automated notifications for IT asset management.

## 📋 Table of Contents

- [Overview](#-overview)
- [What This Automation Does](#-what-this-automation-does)
- [How It Works](#-how-it-works)
- [Prerequisites](#-prerequisites)
- [Google Sheets Setup](#-google-sheets-setup)
- [Step-by-Step Setup Guide](#-step-by-step-setup-guide)
- [Understanding Each Node](#-understanding-each-node)
- [Testing the Automation](#-testing-the-automation)
- [Troubleshooting](#-troubleshooting)
- [FAQ](#-faq)
- [Best Practices](#-best-practices)

## 🎯 Overview

When employees need system upgrades (more RAM, larger SSD, etc.), this automation handles the entire process from request submission to installation completion, including approval workflows, inventory management, scheduling, and automatic restocking alerts.

## ✨ What This Automation Does

This intelligent workflow automatically manages the complete upgrade lifecycle:

- ✅ **Captures upgrade requests** from employees via Google Forms
- ✅ **Routes requests** to IT Admin for approval/rejection
- ✅ **Sends professional email notifications** for approval or rejection
- ✅ **Logs all requests** for compliance and reporting
- ✅ **Checks inventory levels** for requested components
- ✅ **Handles three inventory scenarios:**
  - Stock Available → Schedule upgrade
  - Low Stock → Schedule upgrade + alert admin
  - Out of Stock → Notify employee of delay
- ✅ **Calculates installation dates** (3 business days from request)
- ✅ **Sends reminder emails** to IT Admin until job completion is confirmed
- ✅ **Updates asset records** with new component specifications
- ✅ **Tracks consumable inventory** (RAM modules, SSDs, etc.)
- ✅ **Triggers restock alerts** when inventory is low
- ✅ **Maintains complete audit logs** of all assignments

**The Result:** A fully automated upgrade management system that ensures no request falls through the cracks and inventory is always maintained!

## 🔄 How It Works

### The Complete Process Flow

```
Employee Submits Upgrade Request Form
          ↓
System Captures Request Data
          ↓
Send Approval Request to IT Admin
          ↓
    [Approval Decision]
          ↓
    ┌─────┴─────┐
    │           │
Approved    Rejected
    │           │
    │     Send Rejection Email
    │           │
    │     Log Request (Rejected)
    │           │
    │         END
    │
Send Approval Email
    ↓
Log Request (Approved)
    ↓
Check Inventory for Component
    ↓
┌───────┴───────┬───────────────┐
│               │               │
Stock      Low Stock      Out of Stock
Available       │               │
│               │         Notify Employee
│               │         (On Hold)
│               │               │
│               │             END
│               │
Calculate Installation Date (+3 business days)
    ↓
Wait Until Installation Date
    ↓
Send Schedule Confirmation to Employee
    ↓
Ask IT Admin: "Job Complete?"
    ↓
┌───────┴───────┐
│               │
Yes            No
│               │
│         Wait 15 Minutes
│               │
│         Send Reminder
│               │
│         (Loop back)
│
Update Employee's Asset Record (RAM/Storage)
    ↓
Update Consumable Inventory (-1 qty)
    ↓
Log Assignment in Consumable Log
    ↓
[Check Updated Inventory Level]
    ↓
┌───────┴───────┐
│               │
Normal       Low/Critical
│               │
END      Send Restock Alert
              ↓
            END
```

### Key Features Explained

**Smart Approval System:**
- IT Admin receives beautifully formatted email with all request details
- Approve/Reject with a single click
- Automatic logging of decisions

**Inventory Intelligence:**
- Real-time stock checking
- Different workflows for different stock levels
- Automatic restock alerts
- Prevents over-allocation

**Business-Aware Scheduling:**
- Skips weekends automatically
- Counts only business days
- Ensures realistic timelines

**Persistent Follow-Up:**
- Sends reminders every 15 minutes
- Continues until IT Admin confirms completion
- Prevents forgotten tasks

## 📦 Prerequisites

### Required Accounts & Access

- **n8n account** (cloud at n8n.io or self-hosted)
- **Google Workspace account** with access to:
  - Google Sheets
  - Gmail
  - Google Forms
- **Admin permissions** to create and edit Google Sheets

### Required Skills

✅ **No coding required!**  
✅ **No technical expertise needed!**  
✅ **Follow the step-by-step instructions**

If you can use Google Sheets and follow instructions, you can set this up!

## 📊 Google Sheets Setup

You'll need to create **5 Google Sheets** with specific structures.

### Sheet 1: System Configuration Upgrade Request (Form Responses)

**Purpose:** Captures upgrade requests from employees

**How to Create:**
1. Go to Google Forms
2. Create a new form titled "System Configuration Upgrade Request"
3. Add these fields:

**Field 1: Employee Name**
- Type: Short answer
- Required: Yes

**Field 2: Employee ID**
- Type: Short answer
- Required: Yes

**Field 3: Email**
- Type: Short answer
- Required: Yes
- Validation: Email format

**Field 4: What components are you requesting to upgrade?**
- Type: Dropdown
- Options: 8GB RAM, 16GB RAM, 32GB RAM, 256GB SSD, 512GB SSD, 1TB SSD
- Required: Yes

**Field 5: Describe the main reason for the upgrade request**
- Type: Paragraph
- Placeholder: "e.g., slow performance, inability to run required software, multi-tasking issues"
- Required: Yes

**Field 6: Date of Request**
- Type: Date
- Required: Yes

**Field 7: How significantly is your current system's performance impacting your work efficiency?**
- Type: Linear scale
- Scale: 1 to 5
- Label for 1: Not Impacting
- Label for 5: Severely Impacting
- Required: Yes

4. Click "Responses" → "Create Spreadsheet"
5. This creates a sheet with "Form Responses 1" tab

**Sheet Structure:**
```
Timestamp | Employee Name | Employee ID | Email | What components are you requesting to upgrade? | 
Describe the main reason... | Date of Request | How significantly is your current system's performance...
```

---

### Sheet 2: Upgradation Request Log

**Purpose:** Historical record of all upgrade requests and their status

**Tab Required:**
- `Sheet1` (default tab is fine)

**Columns:**
```
Employee ID | Employee Name | Request Status | Request Date | Requested For
```

**Example Data:**
```
EMP-045 | John Smith | Approved | 21/01/2026 | 16GB RAM
EMP-046 | Jane Doe | Rejected | 22/01/2026 | 1TB SSD
```

**Important Notes:**
- This sheet is **append-only** (never delete records)
- Used for reporting and analytics
- Shows complete request history

---

### Sheet 3: Consumable Sheet

**Purpose:** Inventory of all consumable IT components (RAM, SSDs, etc.)

**Tab Required:**
- `Consumables`

**Columns:**
```
Item_ID | Item_Name | Category | Total_Qty | In_Use | Available_Qty | 
Min_Stock | Status | Vendor | Last_Purchase | Remarks
```

**Example Data:**
```
CONS-001 | 16GB RAM | Memory | 20 | 12 | 8 | 5 | OK | TechSupply Inc | 15-Dec-2025 | DDR4 3200MHz
CONS-002 | 512GB SSD | Storage | 15 | 10 | 5 | 3 | OK | StorageVendor | 10-Jan-2026 | NVMe M.2
CONS-003 | 8GB RAM | Memory | 10 | 9 | 1 | 5 | LOW | TechSupply Inc | 05-Nov-2025 | DDR4 2666MHz
CONS-004 | 1TB SSD | Storage | 5 | 5 | 0 | 2 | OUT | StorageVendor | 20-Oct-2025 | SATA 2.5"
```

**Field Explanations:**
- **Item_ID:** Unique identifier (e.g., CONS-001)
- **Item_Name:** Must match exactly what's in the form dropdown (e.g., "16GB RAM")
- **Category:** Memory, Storage, etc.
- **Total_Qty:** Total units purchased
- **In_Use:** Currently assigned to employees
- **Available_Qty:** Ready for assignment
- **Min_Stock:** Minimum threshold (triggers restock alert)
- **Status:** OK, LOW, OUT (automatically updated)
- **Vendor:** Where to reorder
- **Last_Purchase:** When last ordered
- **Remarks:** Additional notes

**CRITICAL:** The `Item_Name` must match the dropdown options in your form exactly!

---

### Sheet 4: SolarioTech Asset Data

**Purpose:** Inventory of all company laptops and desktops

**Tab Required:**
- `Assets`

**Columns:**
```
Asset_ID | Category | Brand | Model | CPU | RAM | Storage | Serial_No | 
Purchase_Date | Warranty_End | Asset_Status | Assigned_To | Employee_Name | 
Assigned_Date | Condition | Location | Remarks
```

**Example Data (Before Upgrade):**
```
AST-001 | Laptop | Dell | Latitude 5420 | i7-1165G7 | 8GB | 256GB SSD | DL12345 | 
15-Mar-2023 | 15-Mar-2026 | In-Use | EMP-045 | John Smith | 01-Jan-2024 | 
Good | New York | -
```

**Example Data (After RAM Upgrade):**
```
AST-001 | Laptop | Dell | Latitude 5420 | i7-1165G7 | 8GB + 16GB RAM | 256GB SSD | DL12345 | 
15-Mar-2023 | 15-Mar-2026 | In-Use | EMP-045 | John Smith | 01-Jan-2024 | 
Good | New York | Upgraded RAM on 21/01/2026
```

**How Upgrades Are Recorded:**
- RAM upgrade: `8GB + 16GB RAM` (shows original + added)
- Storage upgrade: `256GB SSD + 512GB SSD` (shows original + added)

---

### Sheet 5: Assigned Consumable Log

**Purpose:** Historical record of which components were assigned to which employees

**Tab Required:**
- `Sheet1` (default tab is fine)

**Columns:**
```
Employee ID | Employee Name | Item_ID | Item_Name | Assigned Date
```

**Example Data:**
```
EMP-045 | John Smith | CONS-001 | 16GB RAM | 21/01/2026
EMP-046 | Jane Doe | CONS-002 | 512GB SSD | 22/01/2026
```

**Important Notes:**
- Auto-populated by the workflow
- Never manually edit
- Used for tracking and audits
- Date is automatically added by Google Sheets

---

## 🚀 Step-by-Step Setup Guide

### Phase 1: Prepare Your Google Sheets

#### Step 1: Create the Google Form

Follow the detailed instructions in [Sheet 1](#sheet-1-system-configuration-upgrade-request-form-responses) above to create your form.

#### Step 2: Create Remaining Sheets

Create these sheets manually with the exact column names:

1. **Upgradation Request Log** - Empty sheet ready for data
2. **Consumable Sheet** - Populate with your inventory
3. **SolarioTech Asset Data** - List all employee laptops/desktops
4. **Assigned Consumable Log** - Empty sheet for auto-population

#### Step 3: Populate Initial Data

**Consumable Sheet:**
```
Add all your consumable inventory:
- RAM modules (8GB, 16GB, 32GB, etc.)
- SSD drives (256GB, 512GB, 1TB, etc.)
- Set realistic Min_Stock levels
- Add vendor information
```

**Asset Data:**
```
Add all employee computers with:
- Current specifications
- Who it's assigned to
- Purchase/warranty dates
```

#### Step 4: Verify Data Accuracy

Before proceeding, verify:
- [ ] All column names match exactly (case-sensitive)
- [ ] Item names in Consumable Sheet match form dropdown options
- [ ] Employee IDs in Asset Data are correct
- [ ] Inventory quantities are accurate

---

### Phase 2: Setting Up the n8n Workflow

#### Step 1: Create New Workflow

1. Log into your n8n account
2. Click **"New Workflow"**
3. Name it: **"Consumable + Inventory"**
4. Save the workflow

#### Step 2: Configure Email Settings

**Important:** You'll need to update email addresses in multiple nodes. Decide:
- IT Admin email address (for approvals and alerts)
- Your test email address

---

## 🔧 Understanding Each Node

This workflow has **44 nodes**! Don't worry - I'll explain each one in detail.

### 📥 INPUT SECTION

---

### Node 1: Google Sheets Trigger

**🎯 What It Does:**  
Monitors the upgrade request form for new submissions and starts the entire workflow.

**⚙️ Configuration:**

1. **Add the Node:**
   - Search for "Google Sheets Trigger"
   - Click to add

2. **Connect Google Account**

3. **Configure Settings:**
   - **Document:** Select "System Configuration Upgrade Request (Responses)"
   - **Sheet:** Select "Form Responses 1"
   - **Event:** "On Row Added"
   - **Poll Time:** "Every Minute"

**📊 What Data It Provides:**
```json
{
  "Timestamp": "1/21/2026 10:30:00",
  "Employee Name": "John Smith",
  "Employee ID": "EMP-045",
  "Email ": "john.smith@company.com",
  "What components are you requesting to upgrade?": "16GB RAM",
  "Describe the main reason...": "Need more memory for development work",
  "Date of Request": "21/01/2026",
  "How significantly is your current system's performance...": 4
}
```

---

### Node 2: Edit Fields

**🎯 What It Does:**  
Cleans up and organizes the form data with shorter, cleaner field names for easier use throughout the workflow.

**⚙️ Configuration:**

1. **Add a "Set" node**

2. **Map these fields:**
```
Employee Name → {{ $json["Employee Name"] }}
Employee ID → {{ $json["Employee ID"] }}
Email  → {{ $json["Email "] }}
Component To Upgrade → {{ $json["What components are you requesting to upgrade?"] }}
Reason → {{ $json["Describe the main reason for the upgrade request..."] }}
Date of Request → {{ $json["Date of Request"] }}
Performance Impacting Ratings  → {{ $json["How significantly is your current system's performance..."] }}
```

**📊 What Data It Provides:**
```json
{
  "Employee Name": "John Smith",
  "Employee ID": "EMP-045",
  "Email ": "john.smith@company.com",
  "Component To Upgrade": "16GB RAM",
  "Reason": "Need more memory for development work",
  "Date of Request": "21/01/2026",
  "Performance Impacting Ratings ": 4
}
```

**💡 Why This Matters:**  
Makes data much easier to reference in later nodes with simple names.

---

### ✉️ APPROVAL SECTION

---

### Node 3: Mail Send For Approval/Rejection To IT Admin

**🎯 What It Does:**  
Sends a beautifully formatted approval request email to IT Admin with all request details and approve/reject buttons.

**⚙️ Configuration:**

1. **Add "Gmail" node**
2. **Connect Gmail account**
3. **Set Operation:** "Send and Wait"
4. **Configure:**
   - **To:** `your-it-admin@company.com` (REPLACE THIS!)
   - **Subject:** `System Configuration Upgrade Request`
   - **Message:** (HTML email template provided in the JSON)
   - **Approval Options:**
     - Approval Type: "Double" (Approve/Reject)
     - Approve Label: "Approve"
     - Disapprove Label: "Reject"

**📧 Email Preview:**
```
Subject: System Configuration Upgrade Request

A new request has been submitted and requires your review.

REQUEST DETAILS
Employee Name: John Smith
Employee ID: EMP-045
Date of Request: 21/01/2026
Component Requested: 16GB RAM
Performance Impact: 4/5

JUSTIFICATION
"Need more memory for development work..."

[APPROVE]  [REJECT]
```

**💡 Why This Matters:**  
IT Admin can approve or reject with one click. The workflow waits for their decision before proceeding.

---

### Node 4: If (Approval Check)

**🎯 What It Does:**  
Checks the IT Admin's decision and routes the workflow accordingly.

**⚙️ Configuration:**

1. **Add "IF" node**
2. **Configure condition:**
   - **Value 1:** `{{ $json.data.approved }}`
   - **Operation:** "Equals"
   - **Value 2:** `true` (boolean)

**📊 What It Checks:**
```
IF (Approved = true)
  → Continue to Approval Mail
ELSE
  → Continue to Rejection Mail
```

---

### ✅ APPROVAL BRANCH

---

### Node 5: Approval Mail

**🎯 What It Does:**  
Sends a professional approval email to the employee.

**⚙️ Configuration:**

1. **Add "Gmail" node**
2. **Operation:** "Send"
3. **To:** `{{ $('Edit Fields').item.json["Email "] }}`
4. **Subject:** `System Configuration Upgrade Request - Approved`
5. **Message:** (Provided HTML template)

**📧 Email Highlights:**
- ✓ Approved badge
- Request summary
- Next steps information
- Professional formatting

---

### Node 6: Updating Request Log2

**🎯 What It Does:**  
Records the approved request in the log sheet.

**⚙️ Configuration:**

1. **Add "Google Sheets" node**
2. **Operation:** "Append"
3. **Document:** Select "Upgradation Request Log"
4. **Sheet:** "Sheet1"
5. **Map columns:**
```
Employee ID → {{ $('Edit Fields').item.json['Employee ID'] }}
Employee Name → {{ $('Edit Fields').item.json['Employee Name'] }}
Request Date → {{ $('Edit Fields').item.json['Date of Request'] }}
Requested For → {{ $('Edit Fields').item.json['Component To Upgrade'] }}
Request Status → {{ $('If').item.json.data.approved === true ? 'Approved' : 'Rejected' }}
```

---

### ❌ REJECTION BRANCH

---

### Node 7: Rejection Mail

**🎯 What It Does:**  
Sends a professional rejection email to the employee.

**⚙️ Configuration:**

Similar to Approval Mail but with:
- **Subject:** `System Configuration Upgrade Request - Rejected`
- Different message explaining rejection
- Encouragement to resubmit if circumstances change

---

### Node 8: Updating Request Log

**🎯 What It Does:**  
Records the rejected request in the log sheet (same as Node 6 but from rejection branch).

---

### 📦 INVENTORY CHECKING SECTION

---

### Node 9: Filtering Out The Requesting Component Data

**🎯 What It Does:**  
Searches the Consumable Sheet to find the specific component that was requested.

**⚙️ Configuration:**

1. **Add "Google Sheets" node**
2. **Operation:** "Read"
3. **Document:** "Consumable Sheet"
4. **Sheet:** "Consumables"
5. **Add Filter:**
   - **Column:** `Item_Name`
   - **Value:** `{{ $json["Requested For"] }}`

**📊 What It Returns:**
```json
{
  "Item_ID": "CONS-001",
  "Item_Name": "16GB RAM",
  "Category": "Memory",
  "Total_Qty": 20,
  "In_Use": 12,
  "Available_Qty": 8,
  "Min_Stock": 5,
  "Status": "OK",
  "Vendor": "TechSupply Inc",
  "Last_Purchase": "15-Dec-2025",
  "Remarks": "DDR4 3200MHz"
}
```

**💡 Why This Matters:**  
We need to know current inventory levels before scheduling the upgrade.

---

### Node 10: Switch (Stock Level Decision)

**🎯 What It Does:**  
Evaluates the inventory level and routes to different workflows based on stock availability.

**⚙️ Configuration:**

1. **Add "Switch" node**
2. **Add 3 rules:**

**Rule 1: Stock Available**
- **Condition:** `{{ $json.Available_Qty }}` > 1
- **Output Key:** "Stock Available"

**Rule 2: Low Stock**
- **Condition:** `{{ $json.Available_Qty }}` = 1
- **Output Key:** "Low Stock"

**Rule 3: Out of Stock**
- **Condition:** `{{ $json.Available_Qty }}` = 0
- **Output Key:** "Out of Stock"

**📊 Decision Logic:**
```
Available_Qty > 1  → Stock Available (proceed with scheduling)
Available_Qty = 1  → Low Stock (schedule + send restock alert)
Available_Qty = 0  → Out of Stock (notify employee of delay)
```

---

### 🚫 OUT OF STOCK BRANCH

---

### Node 11: OUT OF STOCK!!!

**🎯 What It Does:**  
Sends an email to the employee explaining that their approved request is on hold due to stock unavailability.

**⚙️ Configuration:**

1. **Add "Gmail" node**
2. **To:** Employee email
3. **Subject:** `System Configuration Upgrade Request - Component Currently Out of Stock`
4. **Message:** Professional email explaining:
   - Request was approved
   - Component is out of stock
   - Will be notified when stock arrives
   - Their request remains prioritized

**💡 Workflow ends here for out-of-stock items**

---

### 📅 SCHEDULING SECTION (Stock Available & Low Stock Branches)

Both branches follow the same scheduling process:

---

### Node 12 & 13: Code in JavaScript / Code in JavaScript2

**🎯 What It Does:**  
Calculates the installation date as 3 business days from the request date (skipping weekends).

**⚙️ Configuration:**

1. **Add "Code" node**
2. **Paste this JavaScript:**

```javascript
const requestDate = new Date($("Edit Fields").first().json["Date of Request"]);

// Add 3 business days
let responseDate = new Date(requestDate);
let daysAdded = 0;

while (daysAdded < 3) {
  responseDate.setDate(responseDate.getDate() + 1);
  
  // Check if it's a weekday (Monday = 1, Friday = 5)
  const dayOfWeek = responseDate.getDay();
  if (dayOfWeek !== 0 && dayOfWeek !== 6) {
    daysAdded++;
  }
}

// Format as DD/MM/YYYY
const dd = String(responseDate.getDate()).padStart(2, '0');
const mm = String(responseDate.getMonth() + 1).padStart(2, '0');
const yyyy = responseDate.getFullYear();

const formattedResponseDate = `${dd}/${mm}/${yyyy}`;

return [
  {
    json: {
      responseDate: formattedResponseDate
    }
  }
];
```

**📊 Example:**
```
Request Date: Friday, 17/01/2026
Business Day 1: Monday, 20/01/2026
Business Day 2: Tuesday, 21/01/2026
Business Day 3: Wednesday, 22/01/2026
Installation Date: 22/01/2026
```

**💡 Why This Matters:**  
Gives IT team realistic time to prepare while ensuring employees get timely service.

---

### Node 14 & 15: Wait / Wait1

**🎯 What It Does:**  
Pauses the workflow until the calculated installation date arrives.

**⚙️ Configuration:**

1. **Add "Wait" node**
2. **No additional configuration needed**
3. The workflow will resume on the scheduled date

**💡 How It Works:**  
n8n keeps the workflow paused in the background and automatically resumes it on the target date.

---

### Node 16 & 17: Schedule Mail / Schedule Mail1

**🎯 What It Does:**  
Sends confirmation email to the employee that their upgrade is scheduled for today.

**⚙️ Configuration:**

1. **Add "Gmail" node**
2. **To:** Employee email
3. **CC:** IT Admin (to keep them informed)
4. **Subject:** `System Upgrade Request - Scheduled for Installation`
5. **Message:** Includes:
   - Scheduled date
   - Component details
   - Contact info for rescheduling

---

### 🔍 JOB CONFIRMATION SECTION

---

### Node 18 & 19: IT Admin Job Completion Confirmation Mail

**🎯 What It Does:**  
Sends an email to IT Admin asking them to confirm when the upgrade installation is complete.

**⚙️ Configuration:**

1. **Add "Gmail" node**
2. **Operation:** "Send and Wait"
3. **To:** IT Admin email
4. **Subject:** `Job Completion Confirmation - Action Required`
5. **Approval Options:**
   - Approve Label: "Done"
   - Disapprove Label: "Not Yet"
6. **Limit Wait Time:** 15 minutes

**📧 Email Contains:**
- Employee details
- Component upgraded
- Scheduled date
- [DONE] [NOT YET] buttons

**💡 Why Limit Wait Time:**  
If admin doesn't respond in 15 minutes, workflow sends a reminder.

---

### Node 20 & 21: Check If Job Confirmed

**🎯 What It Does:**  
Checks if IT Admin clicked "Done" or "Not Yet".

**⚙️ Configuration:**

1. **Add "IF" node**
2. **Condition:**
   - **Value 1:** `{{ $json.data.approved }}`
   - **Operation:** "Is True"

**📊 Decision:**
```
IF (Done clicked)
  → Proceed to update asset data
ELSE (Not Yet clicked)
  → Wait 15 minutes → Send reminder
```

---

### Node 22 & 23: Wait 15 Minutes

**🎯 What It Does:**  
Pauses for 15 minutes before sending a reminder.

**⚙️ Configuration:**

1. **Add "Wait" node**
2. **Wait Time:** 15 minutes

---

### Node 24 & 25: Send Reminder to IT Admin

**🎯 What It Does:**  
Sends a reminder email with more urgency if IT Admin hasn't confirmed completion.

**⚙️ Configuration:**

Similar to initial confirmation email but with:
- ⚠️ REMINDER in subject
- More urgent tone
- Warning that reminders will continue every 15 minutes

**💡 Loop Behavior:**  
This connects back to "Check If Job Confirmed" creating a loop that continues sending reminders every 15 minutes until admin clicks "Done".

---

### 💾 ASSET & INVENTORY UPDATE SECTION

---

### Node 26 & 27: Getting Asset Data

**🎯 What It Does:**  
Retrieves the employee's laptop/desktop record from the Asset Data sheet.

**⚙️ Configuration:**

1. **Add "Google Sheets" node**
2. **Operation:** "Read"
3. **Document:** "SolarioTech Asset Data"
4. **Sheet:** "Assets"
5. **Filter:**
   - **Column:** `Assigned_To`
   - **Value:** `{{ $('Edit Fields').item.json["Employee ID"] }}`

**📊 Returns:**
```json
{
  "Asset_ID": "AST-001",
  "RAM": "8GB",
  "Storage": "256GB SSD",
  "Employee_Name": "John Smith",
  "Assigned_To": "EMP-045",
  // ... all other asset fields
}
```

---

### Node 28 & 29: Code Logic For Updating Values In Asset Data

**🎯 What It Does:**  
Smart code that detects whether the upgrade is RAM or Storage and updates the asset record accordingly.

**⚙️ Configuration:**

1. **Add "Code" node**
2. **Paste this JavaScript:**

```javascript
const items = $input.all();

const requestedForRaw = $('Updating Request Log2').first().json["Requested For"];

// normalize text
const requestedFor = requestedForRaw.toLowerCase();

// detect request type
let requestType = null;

if (requestedFor.includes("ram")) {
  requestType = "RAM";
} else if (requestedFor.includes("ssd")) {
  requestType = "Storage";
}

const updatedItems = items.map(item => {
  const data = item.json;

  data.In_Use = Number(data.In_Use) + assignQty;
  data.Available_Qty = Number(data.Available_Qty) - assignQty;

  // safety: prevent negative stock
  if (data.Available_Qty < 0) {
    data.Available_Qty = 0;
  }

  // optional: update status
  if (data.Available_Qty <= data.Min_Stock) {
    data.Status = "LOW";
  }

  return { json: data };
});

return updatedItems;
```

**📊 Example:**
```
Before:
- Total_Qty: 20
- In_Use: 12
- Available_Qty: 8
- Status: OK

After:
- Total_Qty: 20 (unchanged)
- In_Use: 13 (+1)
- Available_Qty: 7 (-1)
- Status: OK (or "LOW" if Available_Qty ≤ Min_Stock)
```

---

### Node 36 & 37: Updating Consumable Sheet

**🎯 What It Does:**  
Writes the updated inventory quantities back to the Consumable Sheet.

**⚙️ Configuration:**

1. **Add "Google Sheets" node**
2. **Operation:** "Update"
3. **Document:** "Consumable Sheet"
4. **Sheet:** "Consumables"
5. **Matching Column:** "Item_ID"
6. **Map all columns** with updated values

---

### Node 38 & 39: Append row in sheet

**🎯 What It Does:**  
Creates a permanent log entry in the Assigned Consumable Log showing this component was assigned to this employee.

**⚙️ Configuration:**

1. **Add "Google Sheets" node**
2. **Operation:** "Append"
3. **Document:** "Assigned Consumable Log"
4. **Sheet:** "Sheet1"
5. **Map columns:**
```
Employee ID → {{ $('Update Asset Data').item.json.Assigned_To }}
Employee Name → {{ $('Update Asset Data').item.json.Employee_Name }}
Item_ID → {{ $json.Item_ID }}
Item_Name → {{ $json.Item_Name }}
```

---

### 🚨 RESTOCK ALERT SECTION

---

### Node 40: Inventory Stock Up Alert

**🎯 What It Does:**  
Sends urgent email to IT Admin when inventory reaches low or critical levels after the assignment.

**⚙️ Configuration:**

1. **Add "Gmail" node**
2. **To:** IT Admin email
3. **Subject:** `Urgent: Inventory Stock Alert - Immediate Restocking Required`
4. **Message:** Professional HTML email with:
   - ⚠️ Critical alert badge
   - Item details (ID, name, category)
   - Current quantities
   - Vendor information
   - Action steps for reordering

**📧 Email Highlights:**
- Red color scheme for urgency
- Complete item details
- Vendor contact info
- Reordering instructions

**💡 When It Triggers:**  
Only executes after the "Low Stock" branch completes all updates. If stock was already low (Available_Qty = 1), and now becomes 0 after assignment, this alert goes out.

---

## 🧪 Testing the Automation

### Pre-Launch Testing Checklist

#### Step 1: Prepare Test Data

1. **Add Test Inventory:**
```
In Consumable Sheet, add:
Item_ID: TEST-001
Item_Name: 16GB RAM (match your form option)
Category: Memory
Total_Qty: 5
In_Use: 3
Available_Qty: 2
Min_Stock: 1
Status: OK
Vendor: Test Vendor
Last_Purchase: Today's date
```

2. **Add Test Employee Asset:**
```
In Asset Data Sheet:
Asset_ID: TEST-AST-001
Employee_Name: Test Employee
Assigned_To: TEST-001
RAM: 8GB
Storage: 256GB SSD
(fill other required fields)
```

3. **Update Email Addresses:**
   - Replace all `voidman.nikk@gmail.com` with your IT Admin email
   - Test with your own email first

#### Step 2: Test Different Scenarios

**Test 1: Approval with Stock Available**
1. Submit form with "16GB RAM" request
2. Wait 1 minute for trigger
3. Check IT Admin email
4. Click "Approve"
5. Verify:
   - ✅ Approval email sent to employee
   - ✅ Request logged as "Approved"
   - ✅ Schedule email sent after wait period
   - ✅ Confirmation email sent to IT Admin
   - ✅ Click "Done"
   - ✅ Asset record updated
   - ✅ Inventory decreased by 1
   - ✅ Consumable log updated

**Test 2: Rejection**
1. Submit form
2. Click "Reject" in approval email
3. Verify:
   - ✅ Rejection email sent to employee
   - ✅ Request logged as "Rejected"
   - ✅ Workflow stops (no further actions)

**Test 3: Low Stock Alert**
1. Manually set Available_Qty = 1 for test item
2. Submit form and approve
3. Complete workflow
4. Verify:
   - ✅ Everything from Test 1 works
   - ✅ Additional restock alert email sent
   - ✅ Status changed to "LOW" in sheet

**Test 4: Out of Stock**
1. Set Available_Qty = 0
2. Submit form and approve
3. Verify:
   - ✅ "Out of Stock" email sent to employee
   - ✅ Workflow stops (no scheduling)

**Test 5: Reminder Loop**
1. Complete normal flow until confirmation email
2. Click "Not Yet"
3. Wait 15 minutes
4. Verify reminder email sent
5. Click "Not Yet" again
6. Verify another reminder after 15 minutes
7. Click "Done" to exit loop

#### Step 3: Verify Data Accuracy

After successful test:

1. **Check Upgradation Request Log:**
   - Request recorded with correct status

2. **Check Asset Data:**
   - RAM/Storage field updated correctly
   - Format: "8GB + 16GB RAM"

3. **Check Consumable Sheet:**
   - In_Use increased by 1
   - Available_Qty decreased by 1
   - Status updated if low

4. **Check Assigned Consumable Log:**
   - New row added with correct details

### Going Live

Once all tests pass:

1. **Clean up test data:**
   - Remove test inventory items
   - Remove test asset records
   - Clear test log entries

2. **Update email addresses:**
   - Replace all test emails with real IT Admin email
   - Verify all email nodes have correct recipients

3. **Activate the workflow:**
   - Toggle "Active" switch ON
   - Monitor first few real executions

4. **Set up monitoring:**
   - Check execution logs daily for first week
   - Review error notifications
   - Gather feedback from IT Admin and employees

---

## 🔧 Troubleshooting

### Common Issues and Solutions

#### Issue 1: Workflow Not Triggering

**Symptoms:**
- Form submitted but workflow doesn't run

**Solutions:**
1. ✅ Check "Active" toggle is ON
2. ✅ Re-authenticate Google account
3. ✅ Verify correct sheet selected in trigger
4. ✅ Test with manual execution

---

#### Issue 2: Component Not Found in Inventory

**Symptoms:**
- Error: "No data found" after filtering consumables

**Causes:**
- Item name in form doesn't match Consumable Sheet exactly

**Solutions:**
1. ✅ Check Item_Name in sheet matches form dropdown EXACTLY
2. ✅ Look for extra spaces: "16GB RAM " vs "16GB RAM"
3. ✅ Check capitalization matches
4. ✅ Verify the item exists in sheet

---

#### Issue 3: Business Day Calculation Incorrect

**Symptoms:**
- Wrong installation date calculated

**Causes:**
- Request date format issue

**Solutions:**
1. ✅ Ensure date field is formatted as Date type
2. ✅ Check date format in "Edit Fields" node
3. ✅ Verify timezone settings in n8n

---

#### Issue 4: Asset Data Not Updating

**Symptoms:**
- RAM/Storage fields don't show upgrade

**Causes:**
- Code not detecting RAM vs SSD correctly
- Matching issue in update node

**Solutions:**
1. ✅ Check requested item contains "ram" or "ssd" (lowercase)
2. ✅ Verify Asset_ID in update node matches correctly
3. ✅ Check all required columns are mapped

---

#### Issue 5: Inventory Not Decreasing

**Symptoms:**
- Available_Qty stays the same

**Causes:**
- Code node not executing
- Update node misconfigured

**Solutions:**
1. ✅ Check code node for errors
2. ✅ Verify Item_ID matching in update
3. ✅ Ensure assignQty = 1 in code
4. ✅ Check field types (number vs string)

---

#### Issue 6: Reminder Loop Never Stops

**Symptoms:**
- Reminders keep coming even after clicking "Done"

**Causes:**
- Condition checking wrong field

**Solutions:**
1. ✅ Verify "Check If Job Confirmed" condition
2. ✅ Ensure checking for boolean `true` not string "true"
3. ✅ Test approval button functionality

---

#### Issue 7: Emails Not Sending

**Symptoms:**
- No emails received

**Solutions:**
1. ✅ Re-authenticate Gmail in n8n
2. ✅ Check Gmail security settings
3. ✅ Verify email addresses are correct
4. ✅ Look in spam folder
5. ✅ Check Gmail sent folder

---

## ❓ FAQ

### General Questions

**Q: Can I customize the 3-business-day scheduling?**  
A: Yes! In the Code nodes (12 & 13), change `daysAdded < 3` to your desired number. Example: `daysAdded < 5` for 5 business days.

**Q: What if an employee needs multiple components?**  
A: They need to submit separate requests for each component. The workflow processes one component per request.

**Q: Can I add more component types?**  
A: Yes! 
1. Add to form dropdown
2. Add to Consumable Sheet inventory
3. No code changes needed

**Q: How do I handle partial upgrades (employee already has some)?**  
A: The current system adds to existing specs. "8GB" becomes "8GB + 16GB RAM" showing the full history.

**Q: What if IT Admin is on vacation?**  
A: Consider adding a backup admin email or delegating approval rights to another team member.

---

### Technical Questions

**Q: Can I change reminder frequency?**  
A: Yes! In the "Wait 15 Minutes" nodes, change the duration. Be careful not to make it too frequent (annoying) or too infrequent (delays).

**Q: Can I add Slack notifications?**  
A: Absolutely! Add Slack nodes after key events:
- After approval/rejection
- When stock is low
- When job is completed

**Q: How do I export reports?**  
A: The Upgradation Request Log sheet can be exported to:
- Excel (File → Download → Excel)
- CSV for analysis
- Google Data Studio for dashboards

**Q: Can I integrate with our CMDB?**  
A: Yes, if your CMDB has an API. Replace Google Sheets nodes with API calls to your CMDB.

**Q: What about warranty tracking?**  
A: Upgrades might void warranties. Consider adding:
- Warranty check before approval
- Warranty field update after upgrade
- Alert if upgrade affects warranty

---

### Inventory Questions

**Q: How often should I audit inventory?**  
A: Monthly physical counts recommended. Compare:
- Sheet totals vs physical count
- In_Use count vs actual assignments

**Q: What's a good Min_Stock level?**  
A: Formula: `(Average monthly usage × Lead time in months) + Safety stock`
Example: 3 per month × 1 month lead time + 2 safety = 5 minimum

**Q: Can the system auto-reorder?**  
A: Not currently, but you could add:
- API call to vendor ordering system
- Auto-create purchase requisition
- Generate PO draft for approval

**Q: What if we have multiple vendors?**  
A: Add vendor selection logic:
- Primary vendor check first
- Fallback to secondary if primary out of stock
- Price comparison code

---

## 📚 Best Practices

### Data Management

#### Keep Inventory Accurate
- 📊 Weekly inventory reviews
- 📊 Monthly physical counts
- 📊 Investigate discrepancies immediately
- 📊 Update vendor info when changed

#### Maintain Clean Data
- ✅ Use consistent naming conventions
- ✅ No special characters in IDs
- ✅ Standardize date formats
- ✅ Regular data validation

#### Set Realistic Min_Stock Levels
- 📈 Track usage patterns
- 📈 Account for lead times
- 📈 Consider seasonality
- 📈 Include safety buffer

---

### Workflow Optimization

#### Email Management
- 📧 Keep templates professional
- 📧 Include all necessary info
- 📧 Make action items clear
- 📧 Test rendering in different email clients

#### Response Times
- ⏱️ Review reminder frequency
- ⏱️ Adjust business day calculations
- ⏱️ Monitor IT Admin response rates
- ⏱️ Optimize wait times

#### Error Handling
- 🛡️ Add try-catch in code nodes
- 🛡️ Set up error notifications
- 🛡️ Log failures for review
- 🛡️ Create manual override procedures

---

### Security & Compliance

#### Access Control
- 🔒 Limit sheet edit access
- 🔒 Use service accounts
- 🔒 Regular access audits
- 🔒 Implement approval hierarchies

#### Audit Trail
- 📝 Never delete log entries
- 📝 Archive old data yearly
- 📝 Keep backup exports
- 📝 Document all customizations

#### Data Privacy
- 🔐 Handle employee data carefully
- 🔐 Comply with privacy regulations
- 🔐 Secure email communications
- 🔐 Regular security reviews

---

## 🎨 Customization Ideas

### Advanced Features

**Multi-Level Approvals:**
```
Add second approval node for expensive upgrades:
- IT Admin approves technical feasibility
- Finance approves budget
- Both required for expensive items
```

**Budget Tracking:**
```
Add to workflow:
- Component pricing in Consumable Sheet
- Budget remaining calculation
- Budget approval thresholds
- Monthly spending reports
```

**Performance Monitoring:**
```
Track metrics:
- Average approval time
- Installation completion rate
- Inventory turnover
- Employee satisfaction scores
```

**Predictive Restocking:**
```
Advanced inventory:
- ML prediction of usage patterns
- Automatic reorder point calculation
- Seasonal adjustment
- Trend analysis
```

---

### Integration Possibilities

**Asset Management Systems:**
- ServiceNow
- Freshservice
- Jira Service Management
- ManageEngine

**Procurement Systems:**
- SAP Ariba
- Coupa
- Oracle Procurement
- Custom ERPs

**Communication Platforms:**
- Microsoft Teams
- Slack
- Discord
- WhatsApp Business

**HR Systems:**
- BambooHR
- Workday
- ADP
- SAP SuccessFactors

---

## 📞 Support & Resources

### Getting Help

#### n8n Resources
- 📖 [n8n Documentation](https://docs.n8n.io)
- 💬 [n8n Community Forum](https://community.n8n.io)
- 🎥 [n8n YouTube Channel](https://www.youtube.com/c/n8n-io)

#### Google Workspace Help
- 📖 [Google Sheets Help](https://support.google.com/sheets)
- 📖 [Google Forms Help](https://support.google.com/forms)
- 📖 [Gmail Help](https://support.google.com/mail)

---

## 📝 Workflow Summary

### Quick Reference

**Trigger:** New upgrade request form submission  
**Frequency:** Checks every minute  
**Average Duration:** 3-5 business days (includes wait time)  
**Total Nodes:** 44  

**Key Branches:**
1. Approval → Stock Available → Schedule → Complete
2. Approval → Low Stock → Schedule → Complete → Alert
3. Approval → Out of Stock → Notify → Hold
4. Rejection → Log → End

---

## 🚦 Quick Start Checklist

### Setup Phase
- [ ] Google Form created with all required fields
- [ ] Form linked to Google Sheet
- [ ] Upgradation Request Log sheet created
- [ ] Consumable Sheet created and populated
- [ ] Asset Data sheet created with employee computers
- [ ] Assigned Consumable Log sheet created
- [ ] n8n account created
- [ ] New workflow created in n8n

### Configuration Phase
- [ ] All 44 nodes added to workflow
- [ ] Google Sheets authenticated
- [ ] Gmail authenticated
- [ ] All email addresses updated (replace test emails)
- [ ] All node settings configured
- [ ] Code nodes tested for RAM/SSD detection
- [ ] Business day calculation tested

### Testing Phase
- [ ] Test inventory items created
- [ ] Test asset record created
- [ ] Approval flow tested (approved request)
- [ ] Rejection flow tested
- [ ] Stock available scenario tested
- [ ] Low stock scenario tested
- [ ] Out of stock scenario tested
- [ ] Reminder loop tested
- [ ] Asset data updates verified
- [ ] Inventory updates verified
- [ ] All logs verified

### Go-Live Phase
- [ ] Test data removed
- [ ] All email addresses verified
- [ ] Workflow activated
- [ ] First real execution monitored
- [ ] IT Admin trained on emails
- [ ] Employees notified of new system
- [ ] Documentation shared with team

---

**🎉 Congratulations!**

You now have a complete, enterprise-grade system configuration upgrade and inventory management automation that will:
- Save hundreds of hours of manual work
- Eliminate forgotten requests
- Maintain accurate inventory
- Ensure timely employee upgrades
- Provide complete audit trails

**Key Benefits:**
- ⚡ 95% reduction in manual processing time
- 🎯 100% request tracking (nothing gets lost)
- 📊 Real-time inventory visibility
- 🔔 Automatic restock alerts
- 📧 Professional communication throughout

---

**Version:** 1.0  
**Last Updated:** January 2026  
**Maintained By:** SolarioTech IT Team  

Happy Automating! 🚀
  const data = item.json;

  if (requestType === "RAM") {
    data.RAM = `${data.RAM} + ${requestedForRaw}`;
  } 
  else if (requestType === "Storage") {
    data.Storage = `${data.Storage} + ${requestedForRaw}`;
  }

  return { json: data };
});

return updatedItems;
```

**📊 Example Results:**

**For RAM Upgrade:**
```
Before: RAM = "8GB"
After:  RAM = "8GB + 16GB RAM"
```

**For Storage Upgrade:**
```
Before: Storage = "256GB SSD"
After:  Storage = "256GB SSD + 512GB SSD"
```

**💡 Why This Format:**  
Shows the upgrade history. Anyone can see what was original and what was added.

---

### Node 30 & 31: Update Asset Data

**🎯 What It Does:**  
Writes the updated RAM/Storage values back to the Asset Data sheet.

**⚙️ Configuration:**

1. **Add "Google Sheets" node**
2. **Operation:** "Update"
3. **Document:** "SolarioTech Asset Data"
4. **Sheet:** "Assets"
5. **Matching Column:** "Asset_ID"
6. **Map ALL columns** (including updated RAM/Storage)

---

### Node 32 & 33: Getting Consumable Data

**🎯 What It Does:**  
Re-fetches the consumable item data to prepare for inventory update.

**⚙️ Configuration:**

1. **Add "Google Sheets" node**
2. **Operation:** "Read"
3. **Filter by** `Item_Name` matching the requested component

---

### Node 34 & 35: Code in JavaScript1 / Code in JavaScript3

**🎯 What It Does:**  
Updates inventory quantities: decreases Available_Qty by 1, increases In_Use by 1, and checks if stock is now low.

**⚙️ Configuration:**

1. **Add "Code" node**
2. **Paste this JavaScript:**

```javascript
const items = $input.all();

// how many items are being assigned
const assignQty = 1; // change if needed

const updatedItems = items.map(item => {