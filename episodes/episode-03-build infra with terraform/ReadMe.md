# 🚀 EPISODE 4: Build Infrastructure with Terraform on Google Cloud 🌐

This repository documents my progress in the **Terraform Challenge Lab** as part of my series "8 Reasons Why GCP."  
This is **Part 1**, where I focus on setting up the Terraform configuration files and initializing the workspace.

---

## 🏗️ Part 1: Terraform Setup and Prerequisites

In Part 1, I achieved the following:
✅ Created Terraform directory structure with modular files for `instances` and `storage`.  
✅ Defined `variables.tf` in the root and module directories with `region`, `zone`, and `project_id`.  
✅ Configured `main.tf` with the Terraform block and Google provider.  
✅ Initialized the Terraform workspace for the project.

---

### 📁 Directory Structure
```
main.tf
variables.tf
modules/
├── instances/
│   ├── instances.tf
│   ├── outputs.tf
│   └── variables.tf
└── storage/
    ├── storage.tf
    ├── outputs.tf
    └── variables.tf
```

---

### 🛠️ Setup Instructions

1️⃣ Clone the repository:
```bash
git clone <repo-url>
cd <repo-folder>
```

2️⃣ Run the setup script to create the structure and files:
```bash
bash setup.sh
```

3️⃣ Initialize Terraform:
```bash
terraform init
terraform validate
```

---

### 📚 What's Next (Part 2 Preview)
- Import existing VM instances into Terraform.
- Create a VPC network and two subnets.
- Add a firewall rule to allow HTTP ingress.
- Configure remote backend using a GCS bucket.
- Test connectivity between VM instances.
- Destroy infrastructure safely when done.

Stay tuned for **Part 2** in the next update! 🚀

