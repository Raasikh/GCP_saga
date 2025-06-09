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

### 🔹 Task 3: Import VM Instances into Terraform
We imported two Compute Engine VM instances that were created manually outside of Terraform and added them to our configuration.

#### Commands Used:
```bash
terraform import module.instances.google_compute_instance.tf-instance-1 projects/PROJECT_ID/zones/ZONE/instances/tf-instance-1
terraform import module.instances.google_compute_instance.tf-instance-2 projects/PROJECT_ID/zones/ZONE/instances/tf-instance-2
```

---

### 🔹 Task 4: Update VM Configuration
- Modified `machine_type` for both instances to `e2-standard-2`.
- Added a third instance `tf-instance-816008` (later destroyed in Task 5).

#### Code Snippet (in modules/instances/instances.tf):
```hcl
machine_type = "e2-standard-2"
```

---

### 🔹 Task 5: Destroy Unneeded Instance
- Removed `tf-instance-816008` from configuration and ran:
```bash
terraform apply
```

---

### 🔹 Task 6: Use Terraform Registry Module for VPC
- Integrated `terraform-google-modules/network/google` version `6.0.0`
- Created VPC `tf-vpc-946376` with two subnets:
  - `subnet-01` (10.10.10.0/24)
  - `subnet-02` (10.10.20.0/24)

#### Example Configuration:
```hcl
module "vpc" {
  source  = "terraform-google-modules/network/google"
  version = "6.0.0"
  project_id   = var.project_id
  network_name = "tf-vpc-946376"
  routing_mode = "GLOBAL"
  subnets = [
    { subnet_name = "subnet-01", subnet_ip = "10.10.10.0/24", subnet_region = "us-central1" },
    { subnet_name = "subnet-02", subnet_ip = "10.10.20.0/24", subnet_region = "us-central1" }
  ]
}
```

---

### 🔹 Task 7: Connect Instances to VPC Subnets
- Updated `network_interface` for each instance:
  - `tf-instance-1` → `subnet-01`
  - `tf-instance-2` → `subnet-02`

#### Code Snippet (within instances.tf):
```hcl
network_interface {
  network    = "tf-vpc-946376"
  subnetwork = "subnet-01" # or subnet-02 accordingly
}
```

---

## 🧠 Key Takeaways

- Learned how to import unmanaged resources into Terraform.
- Re-provisioned and destroyed infrastructure cleanly.
- Leveraged modules from Terraform Registry to follow best practices.
- Connected infrastructure using VPC and subnet concepts.

---

## 💻 Useful Commands

```bash
terraform init
terraform plan
terraform apply
terraform destroy
terraform import
```
- Test connectivity between VM instances.
- Destroy infrastructure safely when done.

Stay tuned for **Part 2** in the next update! 🚀

