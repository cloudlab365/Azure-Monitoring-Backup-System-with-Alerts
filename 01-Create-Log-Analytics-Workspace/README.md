# 01 Log Analytics workspace collecting metrics from VMs, SQL, and storage

## Objective
This guide walks through building a complete monitoring and backup solution using Azure Monitor, Log Analytics, Alerts, Backup Vaults, and Dashboards.


---

## 1. Prerequisites
- Active [Azure Free Trial](https://azure.microsoft.com/free/)
- Contributor or Owner role on the subscription/resource group
- Basic understanding of Azure VMs, SQL databases, and storage accounts
- Azure Portal or CLI access

---

##  2. Create a Resource Group (This will contain all monitoring and backup resources.)

### Step 1 — Navigate to Resource Groups
From the Azure Portal home screen:
> **Home → Resource Groups → Create**

This opens the *Create a Resource Groups* wizard.
| Setting | Value |
|----------|--------|
| **Subscription** | Azure subscription 1 |
| **Resource group** | `rg-monitoring-lab` |
> **Click → Review  → Create**

![Create a Resource Group - Basics Tab](../images/1.Create%20VM.png)


---

### Step 2 — Create a Log Analytics Workspace (This will collect metrics & logs from VMs, SQL, and Storage.)
Fill out the fields as follows:

From the Azure Portal home screen:
> **Search: Log Analytics workspaces → Create**

| Setting | Value |
|----------|--------|
| **Subscription** | Azure subscription 1 |
| **Resource group** | `rg-monitoring-lab` |
| **Workspace Name** | `law-monitoring` |
| **Region** | same region |
> **Click → Review  → Create**


---

###  Step 3 — Connect Virtual Machines to Log Analytics
> **Open your VM: Go to Extensions + applications → Add Log Analytics agent (MMA) or Azure Monitor Agent (AMA)**
> **Select your law-monitoring workspace**


---

### Step 4 — Connect Azure SQL to Log Analytics
> **Open Azure SQL Server: Go to Diagnostic settings → Click Add diagnostic setting**
> **Enable: SQLInsights → Automatic tuning → QueryStoreRuntimeStatistics (or similar logs)**
> **Send to: Log Analytics workspace → Select: law-monitoring**


---

### Step 5 — Connect Storage Accounts to Log Analytics
> **Open your Storage Account: Go to Diagnostic settings + Add a new setting → Add Log Analytics agent (MMA) or Azure Monitor Agent (AMA)**
> **Enable: Read + Write + Delete logs → Send to Log Analytics workspace → law-monitoring**


---

