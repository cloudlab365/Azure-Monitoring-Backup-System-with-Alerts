# 🚀 Deployment Guide: Azure Monitoring Backup System with Alerts  
### Using Log Analytics, Azure Monitor, and Recovery Services Vault Diagnostics

---

## 1️⃣ **Prerequisites**
Before deployment, ensure the following are in place:

- An **Azure subscription** with contributor or owner permissions  
- At least one **Recovery Services Vault (RSV)**  
- Existing **backup items** (VMs, file shares, SQL, etc.)  
- A **Log Analytics Workspace** (or permission to create one)  
- Azure Monitor access for alert rule creation  
- RBAC roles:  
  - *Backup Contributor*  
  - *Monitoring Contributor*  

---

## 2️⃣ **Create or Select a Log Analytics Workspace**
### **Azure Portal**
1. Go to **Log Analytics Workspaces**.  
2. Select **Create**.  
3. Choose:  
   - Subscription  
   - Resource Group  
   - Workspace Name  
   - Region  
4. Click **Review + Create**.

### **Why this matters**  
This workspace becomes the central hub for all backup logs, queries, dashboards, and alerts.

---

## 3️⃣ **Enable Diagnostic Settings on Recovery Services Vault**
This is the core step — it streams backup logs into Log Analytics.

### **Steps**
1. Open your **Recovery Services Vault**.  
2. Navigate to **Monitoring** → **Diagnostic Settings**.  
3. Select **Add diagnostic setting**.  
4. Name the setting (e.g., `RSV-Backup-Logs`).  
5. Under **Categories**, enable:  
   - `AzureBackupReport`  
   - `AzureBackupJobs`  
   - `AzureBackupAlerts`  
   - `AzureBackupPolicy`  
6. Under **Destination**, choose:  
   - **Send to Log Analytics Workspace**  
7. Select your workspace.  
8. Save.

### **Outcome**  
Backup events now flow into Log Analytics in near real‑time.

---

## 4️⃣ **Verify Log Ingestion**
### **Run a basic KQL query**
```kql
AzureDiagnostics
| where Category == "AzureBackupReport"
| take 50
```

If results appear, ingestion is working.

---

## 5️⃣ **Deploy KQL Queries for Monitoring**
Create a library of queries for:

### **Backup Failures**
```kql
AzureDiagnostics
| where Category == "AzureBackupReport"
| where Status != "Success"
| project TimeGenerated, Resource, OperationName, Status, Message
```

### **Missed Backups**
```kql
AzureDiagnostics
| where Category == "AzureBackupReport"
| summarize LastBackup=max(TimeGenerated) by Resource
| where LastBackup < ago(24h)
```

### **Restore Job Tracking**
```kql
AzureDiagnostics
| where Category == "AzureBackupReport"
| where OperationName == "Restore"
| project TimeGenerated, Resource, Status, Message
```

These queries will be used for alerts and dashboards.

---

## 6️⃣ **Create Azure Monitor Alerts**
You will create alerts based on KQL queries.

### **Steps**
1. Go to **Azure Monitor** → **Alerts**.  
2. Select **Create Alert Rule**.  
3. Under **Scope**, choose your **Log Analytics Workspace**.  
4. Under **Condition**, choose **Custom log search**.  
5. Paste your KQL query (e.g., failed backups).  
6. Set **Alert Logic**:  
   - Frequency: 5–15 minutes  
   - Lookback: 5–60 minutes  
   - Trigger when **result count > 0**  
7. Under **Action Group**, configure:  
   - Email  
   - Teams webhook  
   - ITSM connector  
   - SMS  
8. Name the alert rule (e.g., `Backup-Failure-Alert`).  
9. Save.

### **Outcome**  
You now receive real‑time alerts for backup failures or anomalies.

---

## 7️⃣ **Build a Monitoring Workbook (Dashboard)**
### **Steps**
1. Go to **Azure Monitor** → **Workbooks**.  
2. Select **New**.  
3. Add visualizations:  
   - Line charts for backup success/failure trends  
   - Tables for failed jobs  
   - Pie charts for backup coverage  
4. Use your KQL queries as data sources.  
5. Save the workbook to a shared resource group.

### **Outcome**  
A live dashboard for backup health and compliance.
