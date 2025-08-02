## 📦 **Backup & Recovery in Azure**

---

## 🔄 **Backup vs Disaster Recovery (DR)**

| **Backup**                            | **Disaster Recovery (DR)**                       |
| ------------------------------------- | ------------------------------------------------ |
| Focuses on **data protection**        | Focuses on **business continuity**               |
| Simple copy of data for recovery      | Ensures app/service availability after an outage |
| Data-level recovery (files, VMs, DBs) | Site-level failover & replication                |
| Uses Azure Backup service             | Uses Azure Site Recovery (ASR)                   |

---

## 🛡️ **Azure Backup Service Overview**

* Azure Backup provides **backup for Azure VMs, Files, SQL, SAP HANA**.
* Centralized management via **Recovery Services Vaults**.
* Supports **long-term retention**.
* Secure with **encryption and RBAC access**.

---

## ⚙️ **Steps to Configure Azure VM Backup**

### ✅ **Pre-requisites:**

* Azure Subscription
* Azure VM running
* Azure CLI or Portal access

### ✅ **Steps:**

1. **Create Recovery Services Vault**
2. **Configure Backup Policy**
3. **Enable Backup for VM**
4. **Perform Manual Backup (On-Demand)**
5. **Restore VM from Recovery Point**

---

### 📌 **Azure CLI Commands for VM Backup**

```bash
# Login to Azure
az login

# Create Resource Group
az group create --name MyBackupRG --location eastus

# Create Recovery Services Vault
az backup vault create \
  --resource-group MyBackupRG \
  --name MyRecoveryVault \
  --location eastus

# Enable Backup for VM
az backup protection enable-for-vm \
  --vault-name MyRecoveryVault \
  --resource-group MyBackupRG \
  --vm MyVMName \
  --policy-name DefaultPolicy

# Trigger Backup Immediately
az backup protection backup-now \
  --vault-name MyRecoveryVault \
  --resource-group MyBackupRG \
  --item-name MyVMName \
  --backup-management-type AzureIaasVM \
  --workload-type VM
```

---

## 🏢 **Azure Site Recovery (ASR) Overview**

* Azure Site Recovery provides **disaster recovery orchestration**.
* Replicates VMs across **Azure Regions or On-Premises to Azure**.
* Supports **failover, failback, test failover** scenarios.
* Works with **Recovery Services Vaults**.

---

## ⚙️ **Steps to Configure Azure Site Recovery (VM Replication & DR Setup)**

### ✅ **Pre-requisites:**

* Azure VM deployed.
* Recovery Services Vault.
* Replication Policy created.

### ✅ **Steps:**

1. **Create Recovery Services Vault** (if not already created)
2. **Enable Replication for Azure VM**
3. **Perform Test Failover**
4. **Failover (In case of Disaster)**
5. **Failback Post-Recovery**

---

### 📌 **Azure CLI Commands for Site Recovery**

```bash
# Enable Replication for Azure VM
az backup vault backup-properties set \
  --name MyRecoveryVault \
  --resource-group MyBackupRG \
  --backup-storage-redundancy GeoRedundant

az site-recovery protectable-item list \
  --resource-group MyBackupRG \
  --vault-name MyRecoveryVault \
  --fabric-name Azure \
  --container-name MyBackupRG

az site-recovery replication-protected-item create \
  --resource-group MyBackupRG \
  --vault-name MyRecoveryVault \
  --fabric-name Azure \
  --protection-container-name MyBackupRG \
  --name MyVMName \
  --policy-id <PolicyID>

# Trigger Test Failover
az site-recovery recovery-plan test-failover \
  --name MyRecoveryPlan \
  --vault-name MyRecoveryVault \
  --resource-group MyBackupRG \
  --failover-direction PrimaryToSecondary

# Trigger Actual Failover
az site-recovery replication-protected-item failover-commit \
  --resource-group MyBackupRG \
  --vault-name MyRecoveryVault \
  --fabric-name Azure \
  --protection-container-name MyBackupRG \
  --name MyVMName
```

---

## ♻️ **Restore Virtual Machine (Backup Recovery Steps)**

### ✅ **Via Azure Portal Steps:**

1. Go to **Recovery Services Vault** > **Backup Items**.
2. Select **Azure Virtual Machine** > Choose VM.
3. Click on **Restore VM**.
4. Select **Restore Point** > Restore as:

   * **Create new VM**
   * **Restore Disks**
5. Review and **Initiate Restore**.

### ✅ **Via Azure CLI Restore Command:**

```bash
az backup recoverypoint list \
  --resource-group MyBackupRG \
  --vault-name MyRecoveryVault \
  --item-name MyVMName \
  --backup-management-type AzureIaasVM \
  --workload-type VM

# Use Recovery Point ID from above to trigger Restore
az backup restore restore-disks \
  --resource-group MyBackupRG \
  --vault-name MyRecoveryVault \
  --container-name IaasVMContainer;iaasvmcontainerv2;MyVMName \
  --item-name MyVMName \
  --recovery-point-id <RecoveryPointID> \
  --storage-account MyStorageAccount
```

---

## 📝 **Important Points Summary:**

* **Backup** ensures data protection; **DR** ensures service availability.
* **Azure Backup** is used for **data-level restores (files, VMs, DBs)**.
* **Azure Site Recovery** is for **business continuity (replicating apps/VMs)**.
* **Recovery Services Vault** is the centralized control for both.
* Always perform **Test Failover** in ASR to validate DR readiness.
* Use **Geo-Redundant Storage (GRS)** for backup vaults in production.

---

Do you want me to also give you a **README.md file structure with all these codes ready for a GitHub repo?** (Backup & DR Project Structure)?
