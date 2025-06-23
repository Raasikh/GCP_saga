# Episode 7: Mitigating Threats and Vulnerabilities with Security Command Center

This guide covers the steps taken in the lab to configure Google Cloud's Security Command Center (SCC) for Cymbal Bank and mitigate threats effectively.

---

## 🛠️ Steps and Commands

### 1. Open Security Command Center
- Navigate to: `Security > Security Command Center > Findings`

### 2. Set Time Range
- Set time range to **Last 180 days** in all SCC interfaces to ensure correct findings.

### 3. Create Mute Rules
- Navigate to **Security Command Center > Mute Rules**
- Create the following rules:

**Rule 1**:
```
Name: muting-flow-log-findings
Query: category="FLOW_LOGS_DISABLED"
```

**Rule 2**:
```
Name: muting-audit-logging-findings
Query: category="AUDIT_LOGGING_DISABLED"
```

**Rule 3**:
```
Name: muting-admin-sa-findings
Query: category="ADMIN_SERVICE_ACCOUNT"
```

### 4. Fix SSH and RDP Firewall Rules
- Navigate to `VPC Network > Firewall`
- Find firewall rules allowing:
  - **Port 22 (SSH)**
  - **Port 3389 (RDP)**
- Edit the **source IP range** from:
```
0.0.0.0/0 ➝ 35.235.240.0/20
```

### 5. Reserve Static IP for VM
- Go to `Compute Engine > VM instances > cls-vm > Edit`
- Expand **Network Interfaces** section
- Reserve a **Static IP** and name it `static-ip`
- Click **Save**

### 6. Launch Web Security Scan
- Open the application using:
```
http://<STATIC_EXTERNAL_IP>:8080
```
- Go to `Security > Security Command Center > Web Security Scanner`
- Create a new scan using the app URL

### 7. Export Findings to GCS
- Create a bucket:
```
Name: scc-export-bucket-qwiklabs-gcp-01-a3fbd1054bd7
Location: us-central1 (regional)
```

- Run export command:
```bash
gcloud scc findings export create findings-export \
  --destination=gcs://scc-export-bucket-qwiklabs-gcp-01-a3fbd1054bd7/findings.jsonl \
  --format=jsonl \
  --all-time
```

---

## ✅ Outcome
- Noise reduced with mute rules ✅
- High-risk firewall misconfigurations mitigated ✅
- App-level vulnerabilities scanned ✅
- All findings exported for long-term auditing ✅
