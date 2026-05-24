# 🟢 Terraform Notes

---

## 🧱 Resource

> **Syntax**

![Reference](https://github.com/user-attachments/assets/879daa3d-9399-44e2-80ca-3a50edc87a84)

```hcl
resource "TYPE" "NAME" {
  argument = value
}
```

| 🟩 Field | Standards | Examples |
|----------|-----------|---------|
| **TYPE** | Standard | `aws_instance` `aws_s3_bucket` `aws_iam_user` |
| **NAME** | No standard — any name you want | `my_fav_server_ec2` `my_fav_s3` `my_super_server` `r15_instance` `whatever_bro` |

| Term | Meaning |
|------|---------|
| **Indentation** | Spaces |
| **Syntax** | Format of the code |

---

## 🔵 Data

```hcl
data "aws_vpc" "existing" {
  id = "vpc-12345678"
}

# Use its CIDR block
output "vpc_cidr" {
  value = data.aws_vpc.existing.cidr_block
}
```

👉 **Data block = read existing AWS info**
_(It will NOT create anything. Only fetches details.)_

---

## 🟡 Commands — Targeted Apply (specific resource only)

> To specifically apply changes to a targeted S3 bucket **(apply changes to only specific resource)**

### Step 1 — Targeted dry-run (plan)

```bash
terraform plan \
  -target=aws_s3_bucket_public_access_block.my_specific_bucket \
  -target=aws_s3_bucket_acl.my_specific_bucket \
  -target=aws_s3_bucket_policy.my_specific_bucket
```

### Step 2 — Targeted apply (touches only this bucket)

```bash
terraform apply \
  -target=aws_s3_bucket_public_access_block.my_specific_bucket \
  -target=aws_s3_bucket_acl.my_specific_bucket \
  -target=aws_s3_bucket_policy.my_specific_bucket
```

> ✅ This applies **only** the targeted resources — EC2s, VPCs, Lambdas, LBs are completely untouched.

---

## 🔴 Prod Safety — Prevent Destroy

- For Prod env safety
- Accidental **destroy** prevention

```hcl
lifecycle {
  prevent_destroy = true
}
```

---

## 🟣 Modules

> Modules are simply a **Folder** which contains a reusable set of resources.

### Main components

- `main.tf`
- `variables.tf`
- `outputs.tf`

### Folder Structure

```
terraform/
├── main.tf              # Calls modules & passes values
├── variables.tf         # Declares root variables
├── terraform.tfvars     # Provides actual values (ONLY HERE)
└── modules/
    ├── ec2/
    │   ├── main.tf      # EC2 resource definition
    │   ├── variables.tf # Declares what EC2 module accepts
    │   └── outputs.tf   # (NO terraform.tfvars here!)
    ├── vpc/
    └── s3/
```

### Defining folder for Modules

```
./Shanthosh/ec2
```

| Symbol | Meaning |
|--------|---------|
| `.` | Current Folder |
| `/` | Folder separation |

### Source Path Examples

```hcl
source = "./modules/vpc"         # Current directory → modules → vpc
source = "../shared/database"    # Parent directory → shared → database
source = "/home/user/terraform"  # Absolute path (starts from root)
source = "~/Documents/code"      # Home directory → Documents → code
```

---

## 🟠 Var Components

- Whatever var components mentioned inside `main.tf` in **root**, should be available in the **module's** `variables.tf`
- Module's `main.tf` can have **more** number of var components than in root `main.tf`

---

_Add more notes below_ 👇
