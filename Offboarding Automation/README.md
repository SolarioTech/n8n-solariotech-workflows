# SolarioTech Employee Offboarding Automation

A comprehensive n8n workflow automation that streamlines the complete employee offboarding process, from asset retrieval to employee record management.

## 📋 Table of Contents

- [Overview](#overview)
- [What This Automation Does](#what-this-automation-does)
- [How It Works](#how-it-works)
- [Prerequisites](#prerequisites)
- [Google Sheets Setup](#google-sheets-setup)
- [Step-by-Step Setup Guide](#step-by-step-setup-guide)
- [Understanding Each Node](#understanding-each-node)
- [Testing the Automation](#testing-the-automation)
- [Troubleshooting](#troubleshooting)
- [FAQ](#faq)
- [Best Practices](#best-practices)

## 🎯 Overview

When an employee leaves SolarioTech, there are many tasks that need to be completed - retrieving company assets, updating records, notifying HR, and maintaining proper logs. This automation handles all of these tasks automatically, ensuring nothing falls through the cracks during the offboarding process.

## ✨ What This Automation Does

This workflow automatically handles employee offboarding by:

- ✅ **Monitoring offboarding requests** from a Google Form
- ✅ **Validating employee status** (checks if employee has resigned and has assets)
- ✅ **Retrieving asset information** assigned to the departing employee
- ✅ **Updating asset records** to mark them as available for reassignment
- ✅ **Removing employee records** from the active employee database
- ✅ **Logging offboarding history** for audit and compliance purposes
- ✅ **Sending email notifications** to HR team about completed offboarding
- ✅ **Ensuring data integrity** across all systems

**The Result:** A smooth, error-free offboarding process that takes seconds instead of hours!

## 🔄 How It Works

Think of this automation as a smart HR assistant that handles all offboarding paperwork automatically. Here's the complete journey:

### The Process Flow

```
Employee Offboarding Form Submitted
          ↓
Check Employment Status & Asset Assignment
          ↓
    [Is Employee Resigned?]
    [Has Assets Assigned?]
          ↓
         YES
          ↓
Fetch Asset Policy for Employee's Role
          ↓
Configure Employee & Asset Data
          ↓
Find Assets Assigned to This Employee
          ↓
Update Asset Status to "Available"
          ↓
Retrieve Employee Record
          ↓
Delete Employee from Active Database
          ↓
Verify Deletion Success
          ↓
Log Offboarding in History Sheet
          ↓
Send Confirmation Email to HR
          ↓
PROCESS COMPLETE ✓
```

### What Happens Automatically

1. **Form Submission**: HR fills out an offboarding form with employee details
2. **Validation**: System checks if the employee is marked as "Resigned" and has assets
3. **Asset Lookup**: Finds all assets currently assigned to this employee
4. **Asset Release**: Updates asset status to "Available" for future assignments
5. **Record Removal**: Removes employee from the active employee database
6. **History Logging**: Creates a permanent record in the offboarding log
7. **Notification**: Sends confirmation email to HR team

All of this happens in **under 10 seconds** with zero manual intervention!

## 📦 Prerequisites

### Required Accounts & Access

- **n8n account** (cloud at n8n.io or self-hosted)
- **Google Workspace account** with access to:
  - Google Sheets
  - Gmail
  - Google Forms (optional but recommended)
- **Admin permissions** to create and edit Google Sheets

### Required Skills

✅ **No coding required!**  
✅ **No technical expertise needed!**  
✅ **Just follow the step-by-step instructions**

If you can use Google Sheets and follow instructions, you can set this up!

## 📊 Google Sheets Setup

You'll need to create **4 Google Sheets** with specific structures. Don't worry - I'll explain each one in detail!

### Sheet 1: Employee Offboarding Form (Form Responses)

**Purpose:** Captures offboarding requests from HR

**How to Create:**
1. Go to Google Forms
2. Create a new form titled "Employee Offboarding Form"
3. Add these fields:
   - Employee ID (Short answer)
   - Full Name (Short answer)
   - Role (Short answer)
   - Department (Short answer)
   - Employment Status (Dropdown: Active, Resigned, Terminated)
   - Assets Assigned (Dropdown: Yes, No)
4. Click "Responses" → "Create Spreadsheet"
5. This creates a sheet with "Form Responses 1" tab

**Sheet Structure:**
```
Timestamp | Employee ID | Full Name | Role | Department | Employment Status | Assets Assigned
```

**Example Data:**
```
1/21/2026 10:30 | EMP-045 | John Smith | Software Engineer | Engineering | Resigned | Yes
```

---

### Sheet 2: SolarioTech Employee Data

**Purpose:** Main database of all active employees

**Tabs Required:**
- `Employees` (active employee records)

**Columns:**
```
Employee_ID | Full_Name | Email | Role | Department | Work_Type | 
DOJ | Location | Status | Onboarding_Status | Asset_Assigned | Remarks
```

**Example Data:**
```
EMP-045 | John Smith | john.smith@solariotech.com | Software Engineer | Engineering | Office | 01-Jan-2024 | New York | Active | Completed | Yes | -
```

**Important Notes:**
- This is where active employees are stored
- When offboarding completes, the employee is REMOVED from this sheet
- Keep this sheet updated with all active employees

---

### Sheet 3: SolarioTech Asset Data

**Purpose:** Inventory of all company assets

**Tab Required:**
- `Assets` (asset inventory)

**Columns:**
```
Asset_ID | Category | Brand | Model | CPU | RAM | Storage | Serial_No | 
Purchase_Date | Warranty_End | Asset_Status | Assigned_To | Employee_Name | 
Assigned_Date | Condition | Location | Remarks
```

**Example Data (Assigned Asset):**
```
AST-001 | Laptop | Dell | Latitude 5420 | i7-1165G7 | 16GB | 512GB SSD | DL12345 | 
15-Mar-2023 | 15-Mar-2026 | In-Use | EMP-045 | John Smith | 01-Jan-2024 | 
Good | New York | -
```

**Example Data (After Offboarding):**
```
AST-001 | Laptop | Dell | Latitude 5420 | i7-1165G7 | 16GB | 512GB SSD | DL12345 | 
15-Mar-2023 | 15-Mar-2026 | In-Use | ROLE-001 | Available | - | 
Good | New York | -
```

**Key Fields:**
- `Asset_Status`: Always "In-Use" (don't change during offboarding)
- `Assigned_To`: Changes from Employee_ID to Role_ID after offboarding
- `Employee_Name`: Changes from employee's name to "Available"

---

### Sheet 4: Asset Policy

**Purpose:** Defines which roles get which types of assets

**Tab Required:**
- `Asset_Policy` (role-to-asset mapping)

**Columns:**
```
Role | Role_ID | Asset_Type | Required
```

**Example Data:**
```
React Native Developer     | RN | Laptop | Yes
Web Developer          | RJ | Laptop | Yes
Backend Developer     | NJ | Laptop | Yes

```

**Why This Is Important:**
When an asset is released during offboarding, the system needs to know which role it belongs to (ROLE-001, ROLE-002, etc.) so it can be properly categorized for future assignment.

---

### Sheet 5: Offboarding Employee Log

**Purpose:** Historical record of all offboarding events

**Tab Required:**
- `Sheet1` (default tab is fine)

**Columns:**
```
Employee_Name | Department | Role | Assigned_Asset | DOE (Date of Exit)
```

**Example Data:**
```
John Smith | Engineering | Software Engineer | Retrieved | 21-Jan-2026
```

**Important Notes:**
- This sheet is **append-only** (records are never deleted)
- Use for compliance, audits, and historical tracking
- The "Assigned_Asset" column automatically shows "Retrieved"

---

## 🚀 Step-by-Step Setup Guide

### Phase 1: Prepare Your Google Sheets

#### Step 1: Create the Google Form
1. Go to [Google Forms](https://forms.google.com)
2. Click "Blank" to create a new form
3. Title it: "Employee Offboarding Form"
4. Add the following questions:

**Question 1: Employee ID**
- Type: Short answer
- Required: Yes

**Question 2: Full Name**
- Type: Short answer
- Required: Yes

**Question 3: Role**
- Type: Short answer
- Required: Yes

**Question 4: Department**
- Type: Short answer
- Required: Yes

**Question 5: Employment Status**
- Type: Dropdown
- Options: Active, Resigned, Terminated
- Required: Yes

**Question 6: Assets Assigned**
- Type: Dropdown
- Options: Yes, No
- Required: Yes

5. Click the "Responses" tab
6. Click the green Sheets icon to "Create Spreadsheet"
7. Name it: "Employee Offboarding Form"

#### Step 2: Create Remaining Sheets

Create these sheets manually:

1. **SolarioTech Employee Data**
   - Add "Employees" tab with all required columns
   - Populate with your current active employees

2. **SolarioTech Asset Data**
   - Add "Assets" tab with all required columns
   - List all company assets with current assignments

3. **Asset Policy**
   - Add "Asset_Policy" tab
   - Define all roles and their required assets

4. **Offboarding Employee Log**
   - Create empty sheet with column headers
   - This will auto-populate as offboardings occur

#### Step 3: Verify Your Data

Before proceeding, make sure:
- [ ] All column names match exactly (including underscores)
- [ ] Asset Policy has all company roles listed
- [ ] Asset Data shows which employees have which assets
- [ ] Employee Data has all active employees
- [ ] Form is linked to a Google Sheet

---

### Phase 2: Setting Up the n8n Workflow

#### Step 1: Create New Workflow

1. Log into your n8n account
2. Click **"New Workflow"**
3. Name it: **"SolarioTech OffBoarding Workflow"**
4. Save the workflow

#### Step 2: Add Nodes One by One

Now we'll add each node. I'll explain what each one does and how to configure it.

---

## 🔧 Understanding Each Node

Let me walk you through every single node in this workflow. By the end, you'll understand exactly what each piece does!

---

### Node 1: Google Sheets Trigger

**🎯 What It Does:**  
This is the starting point. It watches your offboarding form for new submissions and kicks off the entire workflow.

**🔍 How It Works:**  
Every minute, this node checks if someone filled out the offboarding form. When it finds a new row (new offboarding request), it starts the workflow.

**⚙️ Configuration Steps:**

1. **Add the Node:**
   - Click the **"+"** button in n8n
   - Search for "Google Sheets Trigger"
   - Click to add it

2. **Connect Your Google Account:**
   - Click **"Connect Account"**
   - Sign in with your Google account
   - Grant necessary permissions

3. **Select Your Form Response Sheet:**
   - **Document:** Select "Employee Offboarding Form"
   - **Sheet:** Select "Form Responses 1"
   - **Event:** Choose "On Row Added"
   - **Poll Time:** Select "Every Minute"

4. **Test It:**
   - Fill out your form once
   - Wait 1 minute
   - You should see data appear in the node

**📊 What Data It Provides:**
```json
{
  "Timestamp": "1/21/2026 10:30:00",
  "Employee ID": "EMP-045",
  "Full Name": "John Smith",
  "Role": "Software Engineer",
  "Department": "Engineering",
  "Employment Status": "Resigned",
  "Assets Assigned": "Yes"
}
```

---

### Node 2: Checking Employee Status

**🎯 What It Does:**  
This is a decision node that checks TWO things:
1. Is the employee's status "Resigned"?
2. Do they have assets assigned?

If BOTH are true, the workflow continues. If either is false, the workflow stops.

**🔍 How It Works:**  
Think of this as a smart gate. It only lets the workflow continue if:
- Employment Status = "Resigned" AND
- Assets Assigned = "Yes"

**⚙️ Configuration Steps:**

1. **Add the Node:**
   - Click **"+"** after the Google Sheets Trigger
   - Search for "IF"
   - Click to add it

2. **Connect It:**
   - Drag a line from Google Sheets Trigger to this node

3. **Set Up First Condition:**
   - **Value 1:** `{{ $json["Employment Status"] }}`
   - **Operation:** "Equal"
   - **Value 2:** `Resigned`

4. **Add Second Condition:**
   - Click **"Add Condition"**
   - **Combinator:** "AND"
   - **Value 1:** `{{ $json["Assets Assigned "] }}`
   - **Operation:** "Equal"
   - **Value 2:** `Yes`

5. **Set Options:**
   - Enable "Case Sensitive"
   - Enable "Strict Type Validation"

**📊 What It Checks:**
```
IF (Employment Status = "Resigned") AND (Assets Assigned = "Yes")
  → Continue to next node ✓
ELSE
  → Stop workflow ✗
```

**💡 Why This Matters:**  
We don't want to process offboarding for:
- Employees who are still active
- Employees who never had assets
- Test submissions

This node ensures we only process legitimate offboarding cases.

---

### Node 3: Reading Asset Policy For Role_ID

**🎯 What It Does:**  
This node reads your Asset Policy sheet to find out what Role_ID corresponds to the departing employee's role.

**🔍 How It Works:**  
When someone with the role "Software Engineer" leaves, this node looks up the Asset Policy and finds that "Software Engineer" = "ROLE-001". We need this Role_ID to properly categorize the returned asset.

**⚙️ Configuration Steps:**

1. **Add the Node:**
   - Click **"+"** after the IF node (True branch)
   - Search for "Google Sheets"
   - Click to add it

2. **Connect Google Account** (if not already connected)

3. **Configure Settings:**
   - **Operation:** "Read"
   - **Document:** Select "Asset Policy"
   - **Sheet:** Select "Asset_Policy"
   - **Options:** Leave empty (read all rows)

**📊 What Data It Provides:**
```json
[
  {
    "Role": "Software Engineer",
    "Role_ID": "ROLE-001",
    "Asset_Type": "Laptop",
    "Required": "Yes"
  },
  {
    "Role": "Data Analyst",
    "Role_ID": "ROLE-002",
    "Asset_Type": "Laptop",
    "Required": "Yes"
  }
  // ... all other roles
]
```

**💡 Why This Matters:**  
When we release an asset back to the pool, we need to mark it with the correct Role_ID so the onboarding automation knows which new employees can get this asset.

---

### Node 4: Data Configuration Logic

**🎯 What It Does:**  
This is a smart code node that takes data from the offboarding form and the asset policy, then combines them into a clean, organized structure.

**🔍 How It Works:**  
Think of this as a data organizer. It:
1. Takes employee info from the form
2. Takes asset policy info from the previous node
3. Matches the employee's role to find their Role_ID
4. Packages everything neatly for the next steps

**⚙️ Configuration Steps:**

1. **Add the Node:**
   - Click **"+"** after Reading Asset Policy node
   - Search for "Code"
   - Click to add it

2. **Paste This Code:**

```javascript
// Get employee data from the form submission
const employee = $('Checking Employee Status').item.json;

// Get all asset policy rows
const assetPolicies = $('Reading Asset Policy For Role_ID').all().map(item => item.json);

// Extract the employee's role
const role = employee.Role;

// Find the matching policy for this role (case-insensitive search)
const matchedPolicy = assetPolicies.find(
  policy => policy.Role.trim().toLowerCase() === role.trim().toLowerCase()
);

// If no matching role found, throw an error
if (!matchedPolicy) {
  const availableRoles = assetPolicies.map(p => p.Role).join(', ');
  throw new Error(`Role not found in Asset Policy: ${role}. Available roles: ${availableRoles}`);
}

// Return organized data
return [
  {
    json: {
      Employee_ID: employee["Employee ID"],
      Employee_Name: employee["Full Name"],
      Employee_Role: employee.Role,
      Employee_Department: employee.Department,
      Employment_Status: employee["Employment Status"],
      Assets_Assigned: employee["Assets Assigned "],
      Role_ID: matchedPolicy.Role_ID
    }
  }
];
```

**📊 What Data It Provides:**
```json
{
  "Employee_ID": "EMP-045",
  "Employee_Name": "John Smith",
  "Employee_Role": "Software Engineer",
  "Employee_Department": "Engineering",
  "Employment_Status": "Resigned",
  "Assets_Assigned": "Yes",
  "Role_ID": "ROLE-001"
}
```

**💡 Why This Matters:**  
This node standardizes all the data so the rest of the workflow can easily access what it needs. It's like organizing your desk before starting work - everything is where you need it!

---

### Node 5: Filtering The Data From Asset Data

**🎯 What It Does:**  
This node searches your asset inventory to find which assets are currently assigned to the departing employee.

**🔍 How It Works:**  
It looks through all your company assets and filters to find only those where the "Assigned_To" field matches the departing employee's ID.

**⚙️ Configuration Steps:**

1. **Add the Node:**
   - Click **"+"** after Data Configuration Logic
   - Search for "Google Sheets"
   - Click to add it

2. **Configure Settings:**
   - **Operation:** "Read"
   - **Document:** Select "SolarioTech Asset Data"
   - **Sheet:** Select "Assets"
   
3. **Add Filter:**
   - Click **"Add Filter"**
   - **Column:** `Assigned_To`
   - **Value:** `{{ $json.Employee_ID }}`

**📊 What Data It Returns:**
```json
{
  "Asset_ID": "AST-001",
  "Category": "Laptop",
  "Brand": "Dell",
  "Model": "Latitude 5420",
  "CPU": "i7-1165G7",
  "RAM": "16GB",
  "Storage": "512GB SSD",
  "Serial_No": "DL12345",
  "Purchase_Date": "15-Mar-2023",
  "Warranty_End": "15-Mar-2026",
  "Asset_Status": "In-Use",
  "Assigned_To": "EMP-045",
  "Employee_Name": "John Smith",
  "Assigned_Date": "01-Jan-2024",
  "Condition": "Good",
  "Location": "New York",
  "Remarks": ""
}
```

**💡 Why This Matters:**  
We need to know exactly which assets this employee has so we can:
1. Update their status
2. Make them available for new employees
3. Keep accurate inventory records

---

### Node 6: Updating Asset Data

**🎯 What It Does:**  
This node updates the asset record to show it's no longer assigned to the departing employee and is now available for reassignment.

**🔍 How It Works:**  
It takes the asset(s) found in the previous step and updates two key fields:
- **Assigned_To:** Changes from Employee_ID (e.g., "EMP-045") to Role_ID (e.g., "ROLE-001")
- **Employee_Name:** Changes from the person's name to "Available"

**⚙️ Configuration Steps:**

1. **Add the Node:**
   - Click **"+"** after Filtering The Data From Asset Data
   - Search for "Google Sheets"
   - Click to add it

2. **Configure Settings:**
   - **Operation:** "Update"
   - **Document:** Select "SolarioTech Asset Data"
   - **Sheet:** Select "Assets"
   - **Matching Column:** "Asset_ID"

3. **Map the Columns:**

Map each field exactly as shown:

```
Asset_ID → {{ $json.Asset_ID }}
Category → {{ $json.Category }}
Brand → {{ $json.Brand }}
Model → {{ $json.Model }}
CPU → {{ $json.CPU }}
RAM → {{ $json.RAM }}
Storage → {{ $json.Storage }}
Serial_No → {{ $json.Serial_No }}
Purchase_Date → {{ $json.Purchase_Date }}
Warranty_End → {{ $json.Warranty_End }}
Asset_Status → In-Use
Assigned_To → {{ $('Data Configuration Logic').item.json.Role_ID }}
Employee_Name → Available
Condition → {{ $json.Condition }}
Location → {{ $json.Location }}
Remarks → {{ $json.Remarks }}
```

**📊 Before vs After:**

**Before:**
```
Assigned_To: EMP-045
Employee_Name: John Smith
```

**After:**
```
Assigned_To: ROLE-001
Employee_Name: Available
```

**💡 Why This Matters:**  
This makes the asset available for the onboarding automation. When a new Software Engineer joins, the onboarding system will see this laptop is assigned to "ROLE-001" and the employee name is "Available", so it can assign it to the new hire.

---

### Node 7: Reading Employee Data

**🎯 What It Does:**  
This node reads the full employee record from your Employee Database to get all their information before we delete them.

**🔍 How It Works:**  
It searches the Employee Database for the departing employee using their Employee_ID and retrieves their complete record.

**⚙️ Configuration Steps:**

1. **Add the Node:**
   - Click **"+"** after Updating Asset Data
   - Search for "Google Sheets"
   - Click to add it

2. **Configure Settings:**
   - **Operation:** "Read"
   - **Document:** Select "SolarioTech Employee Data"
   - **Sheet:** Select "Employees"

3. **Add Filter:**
   - Click **"Add Filter"**
   - **Column:** `Employee_ID`
   - **Value:** `{{ $('Filtering The Data From Asset Data').item.json.Assigned_To }}`

**📊 What Data It Returns:**
```json
{
  "Employee_ID": "EMP-045",
  "Full_Name": "John Smith",
  "Email": "john.smith@solariotech.com",
  "Role": "Software Engineer",
  "Department": "Engineering",
  "Work_Type": "Office",
  "DOJ": "01-Jan-2024",
  "Location": "New York",
  "Status": "Active",
  "Onboarding_Status": "Completed",
  "Asset_Assigned": "Yes",
  "Remarks": "",
  "row_number": 5
}
```

**💡 Why This Matters:**  
We need the complete employee record for two reasons:
1. To know which row to delete (using row_number)
2. To have all the details for the offboarding log and email notification

---

### Node 8: Delete rows or columns from sheet

**🎯 What It Does:**  
This node removes the employee's record from the active Employee Database.

**🔍 How It Works:**  
It uses the row number from the previous step to delete the exact row where this employee's data is stored.

**⚙️ Configuration Steps:**

1. **Add the Node:**
   - Click **"+"** after Reading Employee Data
   - Search for "Google Sheets"
   - Click to add it

2. **Configure Settings:**
   - **Operation:** "Delete"
   - **Document:** Select "SolarioTech Employee Data"
   - **Sheet:** Select "Employees"
   - **Delete:** "Row"
   - **Row Index:** `{{ $json.row_number }}`

**📊 What It Returns:**
```json
{
  "success": true
}
```

**💡 Why This Matters:**  
Once an employee has left and their assets are retrieved, they should no longer appear in the active employee database. This keeps your data clean and accurate.

**⚠️ Important Note:**  
The employee data is NOT lost! We're about to save it in the Offboarding Log before sending the final email.

---

### Node 9: If (Success Check)

**🎯 What It Does:**  
This is a safety check that verifies the employee deletion was successful before proceeding.

**🔍 How It Works:**  
It checks if the deletion returned `success: true`. Only if successful does it continue to log the offboarding and send emails.

**⚙️ Configuration Steps:**

1. **Add the Node:**
   - Click **"+"** after Delete rows or columns from sheet
   - Search for "IF"
   - Click to add it

2. **Configure Condition:**
   - **Value 1:** `{{ $json.success }}`
   - **Operation:** "Equal"
   - **Value 2:** `true` (boolean, not string)

**📊 What It Checks:**
```
IF (Deletion Success = true)
  → Continue to logging and email ✓
ELSE
  → Stop workflow (something went wrong) ✗
```

**💡 Why This Matters:**  
This ensures we don't log an offboarding or send a completion email if the employee deletion failed. It's a data integrity safeguard.

---

### Node 10: Append row in sheet (Offboarding Log)

**🎯 What It Does:**  
This node creates a permanent historical record of the offboarding in your Offboarding Log sheet.

**🔍 How It Works:**  
It adds a new row to the log with the employee's information and marks the assets as "Retrieved".

**⚙️ Configuration Steps:**

1. **Add the Node:**
   - Click **"+"** after If node (True branch)
   - Search for "Google Sheets"
   - Click to add it

2. **Configure Settings:**
   - **Operation:** "Append"
   - **Document:** Select "Offboarding Employee Log"
   - **Sheet:** Select "Sheet1" (or your log sheet name)

3. **Map the Columns:**
```
Employee_Name → {{ $('Reading Employee Data').item.json.Full_Name }}
Department → {{ $('Reading Employee Data').item.json.Department }}
Role → {{ $('Reading Employee Data').item.json.Role }}
Assigned_Asset → Retrieved
```

**📊 What Gets Logged:**
```
Employee_Name: John Smith
Department: Engineering
Role: Software Engineer
Assigned_Asset: Retrieved
(Date is auto-added by Google Sheets timestamp)
```

**💡 Why This Matters:**  
This creates an audit trail of all offboarding activities. You can use this for:
- Compliance reports
- HR analytics
- Reviewing past offboardings
- Asset retrieval verification

---

### Node 11: Send a message (Email Notification)

**🎯 What It Does:**  
This node sends a professional email to the HR team confirming that the offboarding process has been completed successfully.

**🔍 How It Works:**  
It compiles all the employee and asset information into a nicely formatted HTML email and sends it to your HR team's email address.

**⚙️ Configuration Steps:**

1. **Add the Node:**
   - Click **"+"** after Append row in sheet
   - Search for "Gmail"
   - Click to add it

2. **Connect Gmail Account:**
   - Click "Connect Account"
   - Sign in with your Gmail
   - Grant necessary permissions

3. **Configure Email:**
   - **Operation:** "Send"
   - **To:** `your-hr-email@solariotech.com` (replace with your HR email)
   - **Subject:** `Employee Offboarding Completed - Assets Retrieved`

4. **Email Message (HTML Format):**

```html
<html>
<body>
  <p>Dear HR Team,</p>
  
  <p>This is to inform you that the offboarding process has been successfully completed for the following employee:</p>
  
  <table style='border-collapse: collapse; margin: 20px 0;'>
    <tr>
      <td style='padding: 8px; border: 1px solid #ddd;'>
        <strong>Employee Name:</strong>
      </td>
      <td style='padding: 8px; border: 1px solid #ddd;'>
        {{ $('Reading Employee Data').item.json.Full_Name }}
      </td>
    </tr>
    <tr>
      <td style='padding: 8px; border: 1px solid #ddd;'>
        <strong>Employee ID:</strong>
      </td>
      <td style='padding: 8px; border: 1px solid #ddd;'>
        {{ $('Reading Employee Data').item.json.Employee_ID }}
      </td>
    </tr>
    <tr>
      <td style='padding: 8px; border: 1px solid #ddd;'>
        <strong>Department:</strong>
      </td>
      <td style='padding: 8px; border: 1px solid #ddd;'>
        {{ $('Reading Employee Data').item.json.Department }}
      </td>
    </tr>
    <tr>
      <td style='padding: 8px; border: 1px solid #ddd;'>
        <strong>Role:</strong>
      </td>
      <td style='padding: 8px; border: 1px solid #ddd;'>
        {{ $('Reading Employee Data').item.json.Role }}
      </td>
    </tr>
    <tr>
      <td style='padding: 8px; border: 1px solid #ddd;'>
        <strong>Asset Status:</strong>
      </td>
      <td style='padding: 8px; border: 1px solid #ddd;'>
        Retrieved and Available for Reassignment
      </td>
    </tr>
  </table>
  
  <p>All assigned company assets have been successfully retrieved and are now available for allocation to new employees.

The employee record has been updated accordingly in the system.

Best regards,
SolarioTech Offboarding System
```

**💡 Why This Matters:**  
This email provides HR with:
- Confirmation that offboarding completed successfully
- Employee details for their records
- Confirmation that assets are available for reassignment
- Professional documentation of the process

---

## 🧪 Testing the Automation

### Pre-Launch Testing Checklist

Before activating the workflow, you need to test it thoroughly to ensure everything works correctly.

#### Step 1: Prepare Test Data

1. **Create a Test Employee in Your Employee Database:**
```
Employee_ID: EMP-TEST-001
Full_Name: Test Employee
Email: test@solariotech.com
Role: Software Engineer
Department: Testing
Work_Type: Office
DOJ: 01-Jan-2026
Location: New York
Status: Active
Onboarding_Status: Completed
Asset_Assigned: Yes
Remarks: TEST RECORD - DO NOT USE
```

2. **Create a Test Asset:**
```
Asset_ID: AST-TEST-001
Category: Laptop
Brand: Test Brand
Model: Test Model
CPU: Test CPU
RAM: 16GB
Storage: 512GB
Serial_No: TEST123
Purchase_Date: 01-Jan-2026
Warranty_End: 01-Jan-2027
Asset_Status: In-Use
Assigned_To: EMP-TEST-001
Employee_Name: Test Employee
Assigned_Date: 01-Jan-2026
Condition: Good
Location: New York
Remarks: TEST ASSET
```

3. **Ensure Asset Policy is Complete:**
   - Verify "Software Engineer" role exists with a Role_ID

#### Step 2: Manual Test Run

1. **Fill Out the Offboarding Form:**
   - Employee ID: `EMP-TEST-001`
   - Full Name: `Test Employee`
   - Role: `Software Engineer`
   - Department: `Testing`
   - Employment Status: `Resigned`
   - Assets Assigned: `Yes`

2. **Wait and Watch:**
   - Wait 1-2 minutes for the trigger to fire
   - Go to your n8n workflow
   - Click on "Executions" in the left sidebar
   - You should see a new execution

3. **Verify Each Step:**

Click on the execution to see details. Check each node:

✅ **Google Sheets Trigger** - Did it capture the form data?  
✅ **Checking Employee Status** - Did it pass the conditions?  
✅ **Reading Asset Policy** - Did it find the role?  
✅ **Data Configuration Logic** - Is the data structured correctly?  
✅ **Filtering Asset Data** - Did it find the test asset?  
✅ **Updating Asset Data** - Is the asset now marked "Available"?  
✅ **Reading Employee Data** - Did it fetch the employee record?  
✅ **Delete rows or columns** - Was the employee removed?  
✅ **If** - Did the success check pass?  
✅ **Append row in sheet** - Was the log updated?  
✅ **Send a message** - Did you receive the email?

#### Step 3: Verify Results in Google Sheets

1. **Check Asset Inventory:**
   - Open "SolarioTech Asset Data"
   - Find asset AST-TEST-001
   - Verify:
     - `Assigned_To` changed from "EMP-TEST-001" to "ROLE-001"
     - `Employee_Name` changed from "Test Employee" to "Available"

2. **Check Employee Database:**
   - Open "SolarioTech Employee Data"
   - Verify "Test Employee" is NO LONGER in the Employees tab

3. **Check Offboarding Log:**
   - Open "Offboarding Employee Log"
   - Verify a new row was added with:
     - Employee_Name: Test Employee
     - Department: Testing
     - Role: Software Engineer
     - Assigned_Asset: Retrieved

4. **Check Your Email:**
   - Verify you received the offboarding completion email
   - Check all details are correct

#### Step 4: Test Edge Cases

**Test Case 1: Active Employee (Should Not Process)**
- Fill form with Employment Status = "Active"
- Verify workflow stops at the IF node
- No changes should occur

**Test Case 2: No Assets Assigned (Should Not Process)**
- Fill form with Assets Assigned = "No"
- Verify workflow stops at the IF node
- No changes should occur

**Test Case 3: Role Not in Asset Policy (Should Error)**
- Fill form with a role not in your Asset Policy
- Verify you get a clear error message
- Fix by adding the role to Asset Policy

### Going Live

Once all tests pass:

1. **Remove all test data:**
   - Delete test employee from database
   - Delete test asset from inventory
   - Mark test log entries clearly

2. **Activate the workflow:**
   - Toggle the "Active" switch in n8n
   - The workflow will now run automatically

3. **Monitor first real execution:**
   - Watch the first few real offboardings closely
   - Verify everything works as expected

4. **Set up monitoring:**
   - Check execution logs weekly
   - Review error notifications
   - Keep backup of workflow configuration

---

## 🔧 Troubleshooting

### Common Issues and Solutions

#### Issue 1: Workflow Not Triggering

**Symptoms:**
- Form submitted but workflow doesn't run
- No new executions appear

**Possible Causes:**
- Workflow is not "Active"
- Google credentials expired
- Wrong sheet selected in trigger

**Solutions:**
1. ✅ Check the "Active" toggle (top right of workflow) is ON
2. ✅ Re-authenticate your Google account
3. ✅ Verify trigger is pointing to correct sheet
4. ✅ Test with manual execution first

---

#### Issue 2: Workflow Stops at "Checking Employee Status"

**Symptoms:**
- Execution shows the workflow stopped at the IF node
- No further nodes execute

**Possible Causes:**
- Employment Status is not exactly "Resigned"
- Assets Assigned is not exactly "Yes"
- Extra spaces in the form responses

**Solutions:**
1. ✅ Check form dropdown values match exactly
2. ✅ In the IF node, verify field names match your form
3. ✅ Check for extra spaces: `"Assets Assigned "` vs `"Assets Assigned"`
4. ✅ Test with known good data

---

#### Issue 3: "Role not found in Asset Policy" Error

**Symptoms:**
- Code node shows error
- Error message lists available roles

**Possible Causes:**
- Employee's role doesn't exist in Asset Policy
- Role name has different spelling or capitalization
- Extra spaces in role names

**Solutions:**
1. ✅ Add the role to your Asset Policy sheet
2. ✅ Check spelling matches exactly
3. ✅ Remove extra spaces from role names
4. ✅ The code does case-insensitive matching, so "SOFTWARE ENGINEER" = "Software Engineer"

---

#### Issue 4: No Assets Found

**Symptoms:**
- "Filtering The Data From Asset Data" returns empty
- Nothing to update

**Possible Causes:**
- Asset's `Assigned_To` field doesn't match Employee_ID
- Employee doesn't actually have any assets
- Wrong Employee_ID in form

**Solutions:**
1. ✅ Verify asset inventory shows correct `Assigned_To` value
2. ✅ Check Employee_ID matches exactly (case-sensitive)
3. ✅ Confirm employee actually has assets assigned

---

#### Issue 5: Employee Not Deleted

**Symptoms:**
- Employee still appears in database
- Deletion node shows error

**Possible Causes:**
- Sheet is protected
- Wrong sheet selected
- Row number incorrect

**Solutions:**
1. ✅ Remove sheet protection
2. ✅ Verify correct sheet is selected
3. ✅ Check "Reading Employee Data" returns correct row_number
4. ✅ Test with a known employee record

---

#### Issue 6: Email Not Sending

**Symptoms:**
- No email received
- Gmail node shows error

**Possible Causes:**
- Gmail credentials expired
- Wrong email address
- Gmail security settings

**Solutions:**
1. ✅ Re-authenticate Gmail in n8n
2. ✅ Verify email address is correct
3. ✅ Check Gmail security settings
4. ✅ Look in Gmail sent folder
5. ✅ Check spam folder

---

#### Issue 7: Multiple Assets Assigned

**Symptoms:**
- Employee has multiple assets
- Only one gets updated

**Current Behavior:**
- The workflow processes ONE asset per employee
- If an employee has multiple assets (laptop + monitor), only the first one found will be updated

**Solution:**
This workflow is designed for single-asset employees. For multiple assets:

**Option 1: Run offboarding multiple times**
- Submit the form once for each asset
- Each submission processes one asset

**Option 2: Modify the workflow (Advanced)**
- Change "Filtering The Data From Asset Data" to loop through all assets
- Update the code to handle multiple items
- Requires intermediate n8n knowledge

---

## ❓ FAQ

### General Questions

**Q: What happens if I accidentally offboard someone?**  
A: The employee is removed from the active database, but their record is saved in the Offboarding Log. You would need to:
1. Manually re-add them to the Employee Database
2. Manually reassign assets in the Asset Inventory
3. Consider adding an "Undo" or approval workflow

**Q: Can this handle employees with no assets?**  
A: Yes! If you submit the form with "Assets Assigned" = "No", the workflow stops at the first IF check and doesn't process anything. The employee won't be removed from the database.

**Q: What if an employee doesn't return their asset?**  
A: This workflow assumes assets are returned. You might want to add:
- A "Asset Return Status" field in the form
- Conditional logic to mark assets as "Not Returned"
- Email alerts to management for unreturned assets

**Q: Can I offboard multiple employees at once?**  
A: Each form submission processes one employee. For bulk offboarding:
- Submit the form once for each employee, OR
- Modify the workflow to read from a different sheet with multiple rows

**Q: How long does the offboarding process take?**  
A: Usually 5-10 seconds from form submission to email notification (plus the 1-minute trigger check time).

---

### Technical Questions

**Q: Can I customize the email template?**  
A: Yes! Edit the HTML in the "Send a message" node. You can:
- Change colors and styling
- Add your company logo
- Include additional fields
- Modify the message content

**Q: Can I send notifications to Slack instead of email?**  
A: Absolutely! Replace or duplicate the Gmail node with a Slack node:
1. Add "Slack" node after logging
2. Connect your Slack workspace
3. Select a channel
4. Format your message
5. Test and activate

**Q: What if I use Office 365 instead of Gmail?**  
A: n8n supports multiple email providers:
- Microsoft Outlook
- SendGrid
- SMTP (generic)
- Replace the Gmail node with your preferred provider

**Q: Can I integrate this with our HRIS system?**  
A: Yes! If your HR system has an API or webhook:
1. Replace the Google Form trigger with your HR system's webhook
2. Map the data fields accordingly
3. Test the integration

**Q: How do I backup this workflow?**  
A: In n8n:
1. Click the three-dot menu (⋮)
2. Select "Download"
3. Save the JSON file
4. Store it securely
5. You can reimport it anytime

**Q: Can I add approval steps?**  
A: Yes! Add a "Wait" node or webhook between steps:
1. After the first IF check, add a "Wait" node
2. Configure it to wait for approval
3. Send approval request to manager
4. Only proceed when approved

---

### Data & Compliance Questions

**Q: How long is data retained in the Offboarding Log?**  
A: Forever, unless you manually delete it. For compliance:
- Set up automatic archival (monthly/yearly)
- Export to long-term storage
- Follow your company's data retention policy

**Q: Is this GDPR compliant?**  
A: The workflow itself is just automation. For GDPR:
- Ensure you have legal basis for processing
- Implement data deletion upon request
- Add consent tracking if needed
- Consult with your legal team

**Q: What permissions do I need?**  
A: You need:
- Edit access to all Google Sheets
- Gmail sending permissions
- n8n workflow creation rights
- Admin rights (recommended)

**Q: Can I track who initiated each offboarding?**  
A: Yes! Add a field to your form for:
- "Initiated By" (HR person's name)
- This gets logged automatically
- Modify the log sheet to include this field

---

## 📚 Best Practices

### Data Management

#### Keep Your Data Clean
- ✅ Use consistent date formats across all sheets
- ✅ Avoid special characters in Employee_IDs and Asset_IDs
- ✅ Use exact role names (no variations)
- ✅ Regularly audit your Asset Policy sheet

#### Regular Maintenance
- 📅 **Weekly:** Review execution logs for errors
- 📅 **Monthly:** Audit offboarding logs
- 📅 **Quarterly:** Archive old offboarding records
- 📅 **Yearly:** Review and update Asset Policy

#### Data Validation
- ✅ Set up data validation in Google Sheets
- ✅ Use dropdowns for critical fields
- ✅ Require all fields in the form
- ✅ Add field descriptions in forms

---

### Security

#### Access Control
- 🔒 Limit Google Sheets edit access to HR only
- 🔒 Never share n8n credentials
- 🔒 Use service accounts for automation
- 🔒 Regularly review who has access

#### Separate Environments
- 🧪 Create a test environment for changes
- 🏭 Keep production workflow separate
- 📝 Document all changes
- ✅ Test thoroughly before deploying

#### Audit Trail
- 📊 Enable logging in n8n
- 📊 Keep the Offboarding Log indefinitely
- 📊 Regularly review execution history
- 📊 Set up error notifications

---

### Monitoring

#### Set Up Alerts
```
Create email alerts for:
- Workflow execution failures
- Missing data errors
- Unexpected behavior
- Daily/weekly summary reports
```

#### Regular Reviews
- Check execution logs weekly
- Review error patterns
- Identify optimization opportunities
- Gather user feedback

#### Performance Tracking
- Monitor average execution time
- Track success rate
- Count monthly offboardings
- Identify bottlenecks

---

### Workflow Optimization

#### Keep It Simple
- Don't add unnecessary complexity
- Document every customization
- Test changes thoroughly
- Keep backup of working version

#### Error Handling
- Add try-catch in code nodes
- Implement retry logic
- Send error notifications
- Log errors for review

#### Documentation
- Keep this README updated
- Document custom changes
- Train new team members
- Share knowledge

---

## 🎨 Customization Ideas

### Enhanced Notifications

**Slack Integration:**
```
Add Slack node to notify:
- IT team about asset retrieval
- Department head about employee exit
- Facilities about desk/office cleanup
```

**Multi-Channel Alerts:**
```
Send notifications via:
- Email to HR
- Slack to IT
- Teams to Management
- SMS for critical items
```

---

### Advanced Features

**Exit Interview Tracking:**
```
Add to workflow:
- Exit interview scheduling
- Feedback collection
- Survey links
- Manager notifications
```

**Asset Condition Assessment:**
```
Before marking "Available":
- Asset condition check
- Maintenance requirements
- Repair needs
- Replacement recommendations
```

**Department-Specific Workflows:**
```
Different processes for:
- IT department (access revocation)
- Sales (CRM cleanup)
- Finance (system access)
- Executive level
```

---

### Reporting & Analytics

**Monthly Reports:**
```
Create automated reports showing:
- Total offboardings
- Assets retrieved
- Department breakdown
- Trends over time
```

**Dashboard Integration:**
```
Connect to:
- Google Data Studio
- Tableau
- Power BI
- Custom dashboards
```

---

### Integration Possibilities

**HR Systems:**
- BambooHR
- Workday
- ADP
- Namely

**Asset Management:**
- Snipe-IT
- Asset Panda
- InvGate
- ServiceNow

**Communication:**
- Microsoft Teams
- Discord
- WhatsApp Business
- Telegram

**Project Management:**
- Jira (close tickets)
- Asana (remove from projects)
- Monday.com
- Trello

---

## 📞 Support & Resources

### Getting Help

#### n8n Resources
- 📖 [n8n Documentation](https://docs.n8n.io)
- 💬 [n8n Community Forum](https://community.n8n.io)
- 🎥 [n8n YouTube Channel](https://www.youtube.com/c/n8n-io)
- 🐦 [n8n Twitter](https://twitter.com/n8n_io)

#### Google Sheets Help
- 📖 [Google Sheets Help Center](https://support.google.com/sheets)
- 🎓 [Google Sheets Training](https://edu.google.com/teacher-center/training/sheets)

#### Gmail API
- 📖 [Gmail API Documentation](https://developers.google.com/gmail/api)
- 🔧 [Gmail API Console](https://console.cloud.google.com)

---

### Troubleshooting Steps

1. **Check the execution log** in n8n first
2. **Review this README** for common issues
3. **Search the n8n community forum** for similar problems
4. **Test each node individually** to isolate the issue
5. **Contact your IT team** for technical setup help
6. **Create a support ticket** with n8n if needed

---

### Best Places to Ask Questions

**For n8n-specific issues:**
- n8n Community Forum
- n8n Discord server

**For Google Sheets questions:**
- Google Workspace support
- Stack Overflow

**For workflow design:**
- n8n community
- Your IT team
- Automation consultants

---

## 📄 Workflow Summary

### Quick Reference

**Trigger:** New row in Employee Offboarding Form  
**Frequency:** Checks every minute  
**Duration:** 5-10 seconds per execution  
**Requirements:** Resigned employee with assets  

**Steps:**
1. Form submission detected
2. Validate employee status
3. Look up role policy
4. Configure data
5. Find assigned assets
6. Update asset to "Available"
7. Read employee record
8. Delete from active database
9. Verify deletion
10. Log in offboarding sheet
11. Send email to HR

---

## 📝 Version History

**Version 1.0** - Initial Release
- Complete offboarding automation
- Asset retrieval and tracking
- Email notifications
- Offboarding logging

---

## 🙏 Acknowledgments

This workflow was built using:
- [n8n](https://n8n.io) - Workflow automation platform
- [Google Workspace](https://workspace.google.com) - Sheets, Forms, Gmail
- Community feedback and best practices

---

## 📜 License

This automation workflow is provided as-is for use within SolarioTech. Modify and customize according to your organization's needs and policies.

---

## 🚦 Quick Start Checklist

Use this checklist to ensure you haven't missed anything:

### Setup Phase
- [ ] Google Form created with all required fields
- [ ] Form linked to Google Sheet
- [ ] Employee Data sheet created with all columns
- [ ] Asset Data sheet created and populated
- [ ] Asset Policy sheet created with all roles
- [ ] Offboarding Log sheet created
- [ ] n8n account created
- [ ] New workflow created in n8n

### Configuration Phase
- [ ] All 11 nodes added to workflow
- [ ] Google Sheets authenticated
- [ ] Gmail authenticated
- [ ] All node settings configured
- [ ] All column mappings completed
- [ ] Email address updated in final node

### Testing Phase
- [ ] Test employee created
- [ ] Test asset created
- [ ] Test form submitted
- [ ] All nodes executed successfully
- [ ] Asset marked as "Available"
- [ ] Employee removed from database
- [ ] Log entry created
- [ ] Email received

### Go-Live Phase
- [ ] Test data removed
- [ ] Workflow activated
- [ ] First real execution monitored
- [ ] Team trained on process
- [ ] Documentation shared

---

**🎉 Congratulations!**

You've now set up a complete employee offboarding automation that will save your team countless hours and ensure consistent, error-free offboarding every time!

---

**Version:** 1.0  
**Last Updated:** January 2026  
**Maintained By:** SolarioTech IT Team  

Happy Automating! 🚀d are now available for allocation to new employees.</p>
  
  <p>The employee record has been updated accordingly in the system.</p>
  
  <p>Best regards,<br>
  SolarioTech Offboarding System</p>
</body>
</html>
```

**📧 Sample Email Output:**

```
Subject: Employee Offboarding Completed - Assets Retrieved

Dear HR Team,

This is to inform you that the offboarding process has been successfully completed for the following employee:

┌─────────────────────┬──────────────────────────┐
│ Employee Name:      │ John Smith               │
│ Employee ID:        │ EMP-045                  │
│ Department:         │ Engineering              │
│ Role:               │ Software Engineer        │
│ Asset Status:       │ Retrieved and Available  │
└─────────────────────┴──────────────────────────┘

All assigned company assets have been successfully retrieved an