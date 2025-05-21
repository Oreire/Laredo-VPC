
# Automated Provisioning of VPC including Public & Private Subnets with associated Internet Gateway and Route Tables

## Project Summary

This project automates the provisioning of a custom-built AWS Virtual Private Cloud (VPC) using Terraform, ensuring a scalable, secure, and efficient cloud infrastructure. By eliminating manual intervention, it enables seamless resource management and consistent networking across cloud environments.
The setup begins with defining a VPC that includes DNS support for internal name resolution. It then dynamically provisions both public and private subnets across multiple availability zones, ensuring high availability and fault tolerance. An internet gateway is configured with precise routing rules to facilitate external connectivity, allowing applications to communicate securely with external networks.
Security is a critical aspect of the deployment. Well-defined security groups enforce access controls, regulating inbound and outbound traffic. Rules for HTTP and SSH traffic are implemented to maintain accessibility while ensuring the network remains secure against unauthorized access.
Terraform scripts orchestrate the entire deployment process, ensuring infrastructure consistency and easy modification when needed. By automating provisioning, this project enhances scalability, reduces the risk of configuration errors, and optimizes resource allocation for long-term efficiency.
Would you like help integrating automated scaling or enhancing security controls within this infrastructure? 🚀


# **Automated Provisioning of a Custom-Built VPC with Terraform**
This documentation provides a **step-by-step guide** for **automating the provisioning of a Virtual Private Cloud (VPC)** using **Terraform**. It includes all necessary components such as **subnets, internet gateways, route tables, and security groups** to establish a complete networking setup.

---

## **1️⃣ Introduction**
### **🔹 What is Terraform?**
Terraform is an **Infrastructure as Code (IaC) tool** that allows users to define cloud resources using a declarative configuration language. It simplifies provisioning, updates, and management of cloud infrastructure.

### **🔹 Why Automate VPC Creation?**
Automating VPC setup ensures:
- **Consistency** in resource provisioning.
- **Scalability** for dynamic network management.
- **Faster deployments** without manual intervention.

### **🔹 Prerequisites**
Ensure you have the following installed:
- **Terraform CLI** → [Download Terraform](https://developer.hashicorp.com/terraform/downloads)
- **AWS CLI** → [Download AWS CLI](https://aws.amazon.com/cli/)
- **Valid AWS Credentials** (`aws configure`)

---

## **2️⃣ Define Terraform Variables**
Variables provide flexibility in configuring VPC settings.

### **🔹 File: `variables.tf`**
```hcl
variable "region" {
  description = "AWS region"
  default     = "us-east-1"
}

variable "vpc_cidr" {
  description = "VPC CIDR block"
  default     = "10.0.0.0/16"
}

variable "subnets" {
  type        = list(string)
  description = "Subnet CIDRs"
  default     = ["10.0.1.0/24", "10.0.2.0/24"]
}
```
🚀 **Effect:** Allows **dynamic adjustments** of VPC and subnet configurations.

---

## **3️⃣ Create a VPC**
### **🔹 File: `vpc.tf`**
```hcl
resource "aws_vpc" "custom_vpc" {
  cidr_block = var.vpc_cidr
  enable_dns_support = true
  enable_dns_hostnames = true

  tags = {
    Name = "CustomVPC"
  }
}
```
🚀 **Effect:** Creates a **custom VPC** with DNS support for internal name resolution.

---

## **4️⃣ Provision Subnets**
### **🔹 File: `subnets.tf`**
```hcl
resource "aws_subnet" "custom_subnets" {
  count = length(var.subnets)
  vpc_id = aws_vpc.custom_vpc.id
  cidr_block = element(var.subnets, count.index)
  availability_zone = data.aws_availability_zones.available.names[count.index]

  tags = {
    Name = "Subnet-${count.index}"
  }
}
```
🚀 **Effect:** Dynamically provisions **multiple subnets** across availability zones.

---

## **5️⃣ Create an Internet Gateway & Route Table**
### **🔹 File: `network.tf`**
```hcl
resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.custom_vpc.id

  tags = {
    Name = "CustomIGW"
  }
}
```
🚀 **Effect:** Enables **internet access**.

Define **route table**:
```hcl
resource "aws_route_table" "public_rt" {
  vpc_id = aws_vpc.custom_vpc.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }

  tags = {
    Name = "PublicRouteTable"
  }
}
```
🚀 **Effect:** Directs all traffic to the **internet gateway**.

---

## **6️⃣ Associate Subnets with Route Table**
```hcl
resource "aws_route_table_association" "public_assoc" {
  count = length(var.subnets)
  subnet_id = element(aws_subnet.custom_subnets[*].id, count.index)
  route_table_id = aws_route_table.public_rt.id
}
```
🚀 **Effect:** Connects **subnets to the public route table**, ensuring external connectivity.

---

## **7️⃣ Deploy Security Groups**
Define security group for **HTTP & SSH access**:
### **🔹 File: `security.tf`**
```hcl
resource "aws_security_group" "web_sg" {
  vpc_id = aws_vpc.custom_vpc.id

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "WebSecurityGroup"
  }
}
```
🚀 **Effect:** Allows **HTTP and SSH access** from anywhere.

---

## **8️⃣ Initialize & Apply Terraform**
Run Terraform commands:
```sh
terraform init
terraform apply -auto-approve
```
🚀 **Effect:** Deploys the **VPC, subnets, gateway, and security groups**.

---

## **9️⃣ Verify Deployment**
Check created resources:
```sh
terraform state list
aws ec2 describe-vpcs --query "Vpcs[*].VpcId"
kubectl get nodes
```
Expected output:
```
VPC ID: vpc-xxxxxxxx
Subnet IDs: subnet-xxxxxxxx, subnet-yyyyyyyy
Security Groups: sg-xxxxxxxx
```

---

## **🔹 Conclusion**
This Terraform setup ensures **automated VPC provisioning** with:
- **Scalable subnets**
- **Network routing**
- **Security configurations**
- **Automated infrastructure management**

Would you like help **integrating EC2 provisioning within the VPC or automating Terraform state management**? 🚀




Create Virtual Private Cloud (VPC) with specified CIDR

Create 3 Public & 3 Private subnets in two AZs for High Availability and Fault Tolerance

Create IGW and attach to the VPC 

Create Route Tables for Public and Private Subnets

Overall, the Automation of the provisioning of custom-built VPC with Terraform ensures consistency, scalability, and efficient infrastructure deployment.

