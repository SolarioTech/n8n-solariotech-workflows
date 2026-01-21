# SolarioTech Employee Onboarding Automation

A comprehensive n8n workflow automation that streamlines the entire employee onboarding process, from creating employee records to assigning IT assets automatically.

## 📋 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Prerequisites](#-prerequisites)
- [Setup Instructions](#-setup-instructions)
- [Workflow Architecture](#-workflow-architecture)
- [Testing](#-testing)
- [Troubleshooting](#-troubleshooting)
- [FAQ](#-faq)
- [Customization Ideas](#-customization-ideas)
- [Best Practices](#-best-practices)

## 🎯 Overview

This automation workflow handles the complete employee onboarding process at SolarioTech. When a new employee joins the company, this system automatically:

- ✅ Creates a unique Employee ID
- ✅ Adds them to the employee database
- ✅ Checks if they need any IT assets (laptops, monitors, etc.)
- ✅ Assigns available assets based on their role
- ✅ Sends confirmation emails to the employee
- ✅ Notifies the IT team via Slack
- ✅ Maintains a complete log of all asset assignments
- ✅ Alerts management if no assets are available

**The best part?** All of this happens automatically in seconds without manual spreadsheet tracking or email sending!

## ✨ Features

### Automated Processes
- **Smart Employee ID Generation**: Automatically creates sequential IDs (EMP-001, EMP-002, etc.)
- **Role-Based Asset Assignment**: Assigns assets according to predefined company policies
- **Multi-Channel Notifications**: Email confirmations and Slack notifications
- **Comprehensive Logging**: Maintains historical records of all assignments
- **Inventory Management**: Real-time updates to asset availability

### Built-in Safeguards
- **Availability Checks**: Verifies asset availability before assignment
- **Alert System**: Notifies management when assets are unavailable
- **Status Tracking**: Monitors onboarding progress
- **Data Validation**: Ensures data integrity across all systems

## 📦 Prerequisites

### Required Accounts & Access
- **n8n account** (cloud or self-hosted)
- **Google account** with access to Google Sheets and Gmail
- **Slack workspace** with permission to create integrations
- Admin access to create and edit Google Sheets

### Required Google Sheets

You'll need to create 4 Google Sheets with the following structure:

#### 1. Employee Data Sheet
**Tabs Required:**
- `Form Responses 4` (for new employee form submissions)
- `Employees` (main employee database)

**Columns:**
```
Employee_ID | Full_Name | Email | Role | Department | Work_Type | 
DOJ | Location | Status | Onboarding_Status | Asset_Assigned | Remarks
```

#### 2. Asset Policy Sheet
**Columns:**
```
Role | Role_ID | Asset_Type | Required
```

**Example Data:**
```
React Native Developer     | RN | Laptop | Yes
Web Developer     | RJ | Laptop | Yes
Backend Developer  | NJ | Tablet | Yes
```

#### 3. Asset Inventory Sheet
**Columns:**
```
Asset_ID | Category | Brand | Model | CPU | RAM | Storage | Serial_No | 
Purchase_Date | Warranty_End | Asset_Status | Assigned_To | Employee_Name | 
Assigned_Date | Condition | Location | Remarks
```

**Important:** For unassigned assets:
- Set `Assigned_To` = The Role_ID it's meant for
- Set `Employee_Name` = "Available"

#### 4. Asset Allocation Log Sheet
**Columns:**
```
Assignment_ID | Employee_ID | Asset_ID | Date | Status
```

## 🚀 Setup Instructions

### Step 1: Google Sheets Setup

1. Create all 4 required Google Sheets
2. Add column headers exactly as specified above
3. (Optional) Create a Google Form linked to "Form Responses 4" tab with fields:
   - Full Name
   - Email
   - Role
   - Department
   - Work Type
   - Date of Joining
   - Location
   - Status
   - Onboarding Status

4. Populate the Asset Policy Sheet with your company roles
5. Add your asset inventory with proper Role_ID assignments

### Step 2: n8n Workflow Setup

1. Log into your n8n account
2. Create a new workflow: "SolarioTech Employee Onboarding Flow"
3. Add and configure the following nodes:

#### Node Configuration Summary

| Node # | Node Type | Purpose |
|--------|-----------|---------|
| 1 | Google Sheets Trigger | Monitors form submissions |
| 2 | Set (Data Configurations) | Structures form data |
| 3 | Google Sheets (Read) | Fetches existing employees |
| 4 | Code | Generates unique Employee ID |
| 5 | Google Sheets (Append) | Adds new employee to database |
| 6 | IF | Checks onboarding status |
| 7 | Google Sheets (Read) | Fetches asset policy |
| 8 | Google Sheets (Read) | Searches asset inventory |
| 9 | IF | Checks asset availability |
| 10 | Code | Prepares asset assignment data |
| 11 | Google Sheets (Update) | Updates asset status |
| 12 | Google Sheets (Update) | Updates employee record |
| 13 | Gmail | Sends confirmation email |
| 14 | Slack | Posts IT notification |
| 15 | Google Sheets (Read) | Fetches allocation log |
| 16 | Google Sheets (Append) | Logs asset assignment |
| 17 | Gmail | Sends no-asset alert |

### Step 3: Connect Your Accounts

1. **Google Sheets**: Authenticate and grant necessary permissions
2. **Gmail**: Connect your email account
3. **Slack**: Authenticate and invite the n8n bot to your IT channel

### Step 4: Configure Key Nodes

#### Employee ID Generation Code (Node 4)
```javascript
const lastEntry = $input.all().pop();
const lastEntryEmployeeId = lastEntry?.json?.Employee_ID;
const lastEntryNumber = Number(lastEntryEmployeeId.split("-")[1]);
const newEmployeeId = `EMP-${String(lastEntryNumber + 1).padStart(3, "0")}`;

const dataConfigurations = $("Data Configurations").all();
const updatedDataConfigurations = dataConfigurations.map((item) => {
  item.json.Employee_ID = newEmployeeId;
  return item;
});

return updatedDataConfigurations;
```

#### Asset Assignment Logic (Node 10)
```javascript
const employeeData = $('Data Configurations').item.json;
const policyData = $('Asset Policy Check').item.json;
const assetItems = $input.all();

const roleId = policyData.Role_ID;
const employeeId = employeeData.Employee_ID;
const employeeName = employeeData.Full_Name;

const availableAssets = assetItems.filter(item => {
  const asset = item.json;
  return (
    asset.Assigned_To === roleId &&
    asset.Employee_Name === 'Available'
  );
});

if (availableAssets.length === 0) {
  return [{
    json: {
      status: 'NO_ASSET_AVAILABLE',
      Employee_ID: employeeId,
      Full_Name: employeeName,
      message: 'No available assets for this role'
    },
    pairedItem: 0
  }];
}

const selectedAsset = availableAssets[0].json;
const selectedAssetIndex = availableAssets[0].pairedItem;

const today = new Date().toLocaleDateString('en-GB', {
  day: '2-digit',
  month: 'short',
  year: 'numeric'
}).replace(/ /g, '/');

const updatedAsset = {
  ...selectedAsset,
  Asset_Status: 'In-Use',
  Assigned_To: employeeId,
  Employee_Name: employeeName,
  Assigned_Date: today
};

return [{
  json: updatedAsset,
  pairedItem: selectedAssetIndex !== undefined ? selectedAssetIndex : 0
}];
```

## 🔍 Workflow Architecture

### Process Flow

```
New Employee Form Submission
    ↓
Data Configuration
    ↓
Fetch Existing Employees
    ↓
Generate Employee ID
    ↓
Add to Employee Database
    ↓
Check Onboarding Status
    ↓
┌─────────────────┴─────────────────┐
│                                   │
Pending                         Completed
│                                   │
Fetch Asset Policy                 END
│
Search Asset Inventory
│
Check Availability
│
┌──────────┴──────────┐
│                     │
Available         Unavailable
│                     │
Assign Asset      Send Alert Email
│                     │
Update Records        END
│
Send Confirmations
│
Log Assignment
│
END
```

## 🧪 Testing

### Pre-Launch Testing

1. **Prepare Test Data**
   - Create a test employee entry
   - Ensure at least one "Available" asset exists
   - Use your own email for testing

2. **Manual Execution**
   - Click "Execute Workflow" in n8n
   - Monitor each node for errors
   - Verify data flow

3. **Validation Checklist**
   - [ ] Employee ID generated correctly
   - [ ] Employee added to database
   - [ ] Asset assigned successfully
   - [ ] Confirmation email received
   - [ ] Slack notification posted
   - [ ] Allocation log updated

4. **Test Edge Cases**
   - No available assets (verify alert email)
   - Completed onboarding status (verify workflow stops)
   - Multiple available assets (verify single assignment)

### Going Live

1. Toggle the workflow to "Active"
2. Monitor first few real executions
3. Review execution logs regularly

## 🔧 Troubleshooting

### Common Issues & Solutions

#### Workflow Not Triggering
**Symptoms:** No execution when form is submitted

**Solutions:**
- Verify workflow is set to "Active"
- Re-authenticate Google account
- Check trigger poll interval
- Test with manual execution

#### Employee ID Generation Fails
**Symptoms:** Error in code node

**Solutions:**
- Add seed employee (EMP-000) if sheet is empty
- Verify column name is exactly "Employee_ID"
- Check for proper data type formatting

#### Asset Not Being Assigned
**Symptoms:** No asset allocated despite availability

**Solutions:**
- Verify Employee_Name = "Available" (exact match)
- Check Role_ID consistency across sheets
- Ensure Assigned_To field matches Role_ID

#### Emails Not Sending
**Symptoms:** No confirmation emails received

**Solutions:**
- Re-authenticate Gmail credentials
- Check email address format
- Verify Gmail security settings
- Review sent folder

#### Slack Messages Not Posting
**Symptoms:** No Slack notifications

**Solutions:**
- Re-authenticate Slack
- Invite n8n bot to channel: `/invite @n8n`
- Verify channel name is correct
- Check bot permissions

#### Data Not Writing to Sheets
**Symptoms:** Records not updating

**Solutions:**
- Remove sheet protection
- Verify exact column name matches
- Check sheet/tab selection
- Review permissions

## ❓ FAQ

### General Questions

**Q: Can I customize the Employee ID format?**  
A: Yes! Modify the code in Node 4. Change the prefix from `EMP-` to your preference, or adjust padding from 3 to 4 digits.

**Q: Can I assign multiple assets to one employee?**  
A: The current workflow assigns one asset. Multiple asset assignment requires modifying the code logic to loop through asset types.

**Q: What if an employee doesn't need assets?**  
A: Set their Onboarding_Status to "Completed" in the form. They'll be added to the database without asset assignment.

**Q: Can I add approval steps?**  
A: Yes! Add a "Wait" node with approval options, or create conditional checks for manager approval.

**Q: How do I handle asset returns?**  
A: Create a separate off-boarding workflow that updates Asset_Status to "Available" when employee status changes to "Inactive".

### Technical Questions

**Q: What happens if the workflow fails mid-execution?**  
A: n8n logs all executions. You can review the log, fix the issue, and re-run safely without data duplication.

**Q: Can I use other email providers?**  
A: Yes! n8n supports Outlook, SMTP, SendGrid, Mailgun, and others. Replace the Gmail node accordingly.

**Q: Can I integrate with HR software?**  
A: Yes! n8n connects to BambooHR, Workday, and other systems via API. Replace the trigger with your HR system's webhook.

**Q: How do I backup my workflow?**  
A: Click the three-dot menu → Download. Save the JSON file for reimport if needed.

## 🎨 Customization Ideas

### Additional Notifications
- Microsoft Teams integration
- Discord webhooks
- SMS via Twilio
- Push notifications

### Reporting Dashboards
- Daily/weekly onboarding summaries
- Pending asset assignment reports
- Inventory utilization metrics

### Advanced Logic
- Department-specific asset rules
- Location-based shipping
- Manager approval for expensive assets
- Automatic user account creation

### System Integrations
- **Jira/Asana**: Create onboarding tasks
- **Active Directory**: Auto-create user accounts
- **Door Access**: Grant building access
- **Ticketing Systems**: Create IT setup tickets

## 📚 Best Practices

### Data Management
- Use consistent date formatting
- Avoid special characters in IDs
- Maintain role name consistency across sheets
- Regular data validation checks

### Maintenance
- Monthly inventory reviews
- Archive old allocation logs quarterly
- Update asset policies as roles evolve
- Document all customizations

### Security
- Limit Google Sheets edit permissions
- Never share workflow credentials
- Use separate test/production environments
- Regular access audits

### Monitoring
- Weekly execution log reviews
- Set up error notifications
- Maintain workflow backups
- Track success metrics

## 📞 Support

### Resources
- [n8n Community Forum](https://community.n8n.io)
- [n8n Documentation](https://docs.n8n.io)
- [Google Sheets Help](https://support.google.com/sheets)

### Getting Help
1. Check this README first
2. Review execution logs for errors
3. Search the n8n community forum
4. Contact your IT team for technical setup

## 📄 License

This automation workflow is provided as-is for use within SolarioTech. Modify and customize according to your organization's needs.

## 🙏 Acknowledgments

Built with [n8n](https://n8n.io) - the workflow automation platform.

---

**Version:** 1.0  
**Last Updated:** January 2026  
**Maintained By:** SolarioTech IT Team

Happy automating! 🚀