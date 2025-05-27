
# 🚀 Episode 3 – Build a Secure Google Cloud Network 🔐
Part of my **8 Reasons Why GCP** series, this episode focuses on creating a secure, scalable, and access-controlled Google Cloud environment. The setup includes a bastion host for internal SSH access, a public-facing web app (Juice-Shop), and carefully configured firewall rules.

---

## 🧠 What’s Covered
- Configuring **VPC networks** and **subnets**
- Setting up **Compute Engine instances**
- Enabling **Cloud IAP** for SSH tunneling
- Defining **Firewall Rules** with **network tags**
- Securing SSH access paths and HTTP exposure
- IAM and **least-privilege design**

---

## 🛠️ Commands Used in This Project

### 1️⃣ **Create Firewall Rule: Allow SSH to Bastion via IAP**
```
gcloud compute firewall-rules create allow-ssh-iap-bastion-ql-969   --network=acme-vpc   --direction=INGRESS   --action=ALLOW   --rules=tcp:22   --source-ranges=35.235.240.0/20 \  # Google IAP IP Range
  --target-tags=permit-ssh-iap-ingress-ql-969   --priority=1000
```

### 2️⃣ **Create Firewall Rule: Allow HTTP to Juice-Shop from World**
```
gcloud compute firewall-rules create allow-http-juice-shop-ql-969   --network=acme-vpc   --direction=INGRESS   --action=ALLOW   --rules=tcp:80   --source-ranges=0.0.0.0/0 \  # Open to the world
  --target-tags=permit-http-ingress-ql-969   --priority=1000
```

### 3️⃣ **Create Firewall Rule: Allow SSH from Bastion (acme-mgmt-subnet) to Juice-Shop**
```
gcloud compute firewall-rules create allow-ssh-from-mgmt-ql-969   --network=acme-vpc   --direction=INGRESS   --action=ALLOW   --rules=tcp:22   --source-ranges=192.168.10.0/24 \  # Replace with actual subnet range of acme-mgmt-subnet
  --target-tags=permit-ssh-internal-ingress-ql-969   --priority=1000
```

### 4️⃣ **Start the Bastion Host**
```
gcloud compute instances start bastion-vm-name --zone=us-east4-b
```

### 5️⃣ **SSH to Bastion Host via IAP**
```
gcloud compute ssh bastion-vm-name   --tunnel-through-iap   --zone=us-east4-b
```

### 6️⃣ **SSH to Juice-Shop from Bastion (Internal IP)**
```
ssh student-00-xxxxxxx@INTERNAL_IP_OF_JUICE_SHOP
```
- Replace `student-00-xxxxxxx` with your actual username.
- Replace `INTERNAL_IP_OF_JUICE_SHOP` with the private IP of the juice-shop instance.

---

## 🔐 Key Notes
- **No public IPs** are assigned to bastion or juice-shop instances.
- **Cloud IAP** manages SSH access to bastion securely.
- **Juice-Shop** exposes **HTTP** (tcp:80) publicly, but nothing else.
- **Firewall rules** use **network tags** for precise targeting.
- Remember to **remove overly permissive rules** if any exist.

---

## 📂 Resources
- 🔗 [Google IAP IP ranges](https://cloud.google.com/iap/docs/using-tcp-forwarding#iap_tcp_forwarding_ip_ranges)
- 🔗 [GCP VPC Firewall Rules Documentation](https://cloud.google.com/vpc/docs/firewalls)
- 🔗 [8 Reasons Why GCP Series on GitHub](https://github.com/YourUsername/8-Reasons-Why-GCP)

---

## 🏆 Outcome
✅ Secure cloud architecture with **least-privilege access**  
✅ Proper **firewall rules** and **network segmentation**  
✅ Bastion access controlled by **IAP**  
✅ Juice-Shop ready for **HTTP access**, with SSH locked down

---

Happy Cloud Building! 🚀
