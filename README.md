# SolarioTech Workflows

## Overview

SolarioTech Workflows is a comprehensive automation suite built with **n8n** designed to streamline critical IT operations and employee management processes. This repository contains workflow configurations for **Employee Onboarding**, **Employee Offboarding**, and **Asset Upgrade Management** automation.

These workflows automate repetitive tasks, ensure consistency in processes, and improve operational efficiency by integrating with Google Sheets, Gmail, and other enterprise tools.

---

## Repository Structure

### 📁 Onboarding Automation
**Directory:** `Onboarding Automation/`

**Purpose:** Automates the complete employee onboarding process

**Files:**
- `SolarioTech Onboarding Flow.json` - Main onboarding workflow configuration

**Key Features:**
- Automated employee data entry into company systems
- Asset allocation and tracking
- Email notifications to IT and HR teams
- Equipment assignment logging
- Welcome communications to new employees
- Integration with employee databases and asset tracking systems

**Process Flow:**
1. New employee data is captured
2. Assets are automatically assigned based on role and department
3. Inventory is updated in real-time
4. Confirmation emails are sent to relevant stakeholders
5. Onboarding log is maintained for audit purposes

---

### 📁 Offboarding Automation
**Directory:** `Offboarding Automation/`

**Purpose:** Automates the complete employee offboarding process

**Files:**
- `SolarioTech OffBoarding Workflow.json` - Main offboarding workflow configuration

**Key Features:**
- Automated asset recovery and deactivation
- Employee data cleanup and archival
- Access revocation notifications
- Asset condition assessment
- Exit communication workflows
- Equipment return tracking
- Department handover documentation

**Process Flow:**
1. Offboarding request is initiated
2. Assigned assets are identified and flagged for recovery
3. IT team receives automated notifications for access removal
4. Asset condition is documented upon return
5. Employee data is archived
6. Final offboarding confirmation is logged

---

### 📁 Asset Upgradtion Automation
**Directory:** `Assest Upgradtion Automation/`

**Purpose:** Automates system configuration upgrade requests and consumable inventory management

**Files:**
- `Consumable + Inventory.json` - Upgrade request and inventory tracking workflow
- `Consumable + Inventory.json` - Consumable management configuration

**Key Features:**
- Automated system upgrade request processing
- Approval workflow for IT administrators
- Performance impact assessment
- Inventory management with real-time stock tracking
- Automatic consumable assignment to employees
- Stock level monitoring and reorder alerts
- Installation scheduling and confirmation
- Asset specification updates

**Process Flow:**
1. Employee submits system upgrade request (RAM, SSD, etc.)
2. Request is sent to IT Admin for approval review
3. IT Admin approves or rejects the request
4. Upon approval, system checks inventory availability
5. If in stock:
   - Installation is scheduled
   - Employee receives confirmation email
   - IT team receives job assignment
   - Upon completion, inventory is updated
   - Consumable assignment log is maintained
6. If out of stock:
   - Employee is notified of delay
   - Stock alert is sent to IT Admin for procurement
   - Process resumes when inventory is replenished
7. Low stock warnings trigger automatic reorder alerts

---

## Configuration Guide

### Prerequisites
- n8n instance running
- Google Sheets API configured
- Gmail API configured with service account credentials
- Employee database connected

### Setup Instructions

#### 1. Google Sheets Integration
Update the spreadsheet document IDs in the workflow configuration:
- Replace empty `documentId` values with your Google Sheet IDs
- Update `sheetName` values with your actual sheet names
- Ensure API credentials are configured in n8n

#### 2. Email Configuration
Configure email settings for notifications:
- Replace placeholder email addresses with actual IT Admin email
- Update distribution lists for HR and department heads
- Customize email templates as needed

#### 3. Credential Management
1. In n8n, go to **Credentials**
2. Configure:
   - **Google Sheets OAuth2 API** - for spreadsheet access
   - **Gmail OAuth2 API** - for email notifications
3. Test connections before deploying workflows

---

## Workflow Customization

### Modifying Email Templates
Email templates are embedded in the workflow JSON. To customize:
1. Open the workflow JSON file
2. Locate the `message` parameter in email nodes
3. Edit the HTML content as needed
4. Update subject lines in the `subject` parameter

### Adjusting Approval Workflows
- Modify approval conditions in the **If** nodes
- Update approval options (labels, email recipients)
- Adjust timeout settings for approval requests

### Inventory Configuration
- Update stock level thresholds in consumable management nodes
- Modify item categorization and tracking fields
- Adjust reorder alert recipients and timing

---

## Security & Best Practices

⚠️ **Important Security Notes:**
- All spreadsheet IDs and personal email addresses have been removed from this repository
- Before deploying: Update all empty credential fields with your actual credentials
- Use environment variables for sensitive data
- Rotate credentials regularly
- Restrict n8n access to authorized personnel only

### Best Practices:
1. **Backup Regularly** - Maintain backups of workflow configurations
2. **Test in Staging** - Test all changes in a staging environment first
3. **Monitor Logs** - Review n8n execution logs for errors or anomalies
4. **Audit Trail** - Keep records of workflow modifications and approvals
5. **Access Control** - Limit access to workflow editing to trusted administrators

---

## Deployment

### Export/Import Workflows
1. **Export:** In n8n, go to Workflow → Download as JSON
2. **Import:** Create new workflow → Import from file → Select JSON file
3. **Activate:** Once imported, activate the workflow to start automation

### Scheduling
- Workflows trigger on specific events (new data entries in Google Sheets)
- Configure polling intervals in the trigger nodes
- Set up notification schedules for reports

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Workflow not triggering | Check polling time settings and Google Sheets permissions |
| Email not sending | Verify Gmail credentials and ensure authorized email addresses |
| Inventory not updating | Check Google Sheets access and confirm sheet names match configuration |
| Asset not assigned | Verify employee data format and asset category mappings |
| Stock alert not received | Confirm recipient email addresses and check notification settings |

---

## Support & Maintenance

For assistance with these workflows:
1. Check the n8n documentation: https://docs.n8n.io
2. Review workflow logs in the n8n interface
3. Test individual nodes to identify issues
4. Consult with your IT team or system administrator

---

## Version History

- **v1.0** (January 2026) - Initial release
  - Onboarding workflow
  - Offboarding workflow
  - Asset upgrade and inventory management system

---

## License & Usage

These workflows are proprietary to SolarioTech and intended for internal use only. Unauthorized reproduction, distribution, or modification is prohibited.

---

**Last Updated:** January 21, 2026
