# CokaCola-Cloud-Engineer

## Azure VM Deployment with Terraform, Ansible, and Azure DevOps Pipeline

A complete Infrastructure as Code (IaC) solution for deploying and configuring Azure Virtual Machines using Terraform for provisioning, Ansible for configuration management, and Azure DevOps for CI/CD automation.

## 📋 Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Repository Structure](#repository-structure)
- [Getting Started](#getting-started)
- [Running the Pipeline](#running-the-pipeline)
- [Configuration](#configuration)
- [Outputs](#outputs)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)

## 🎯 Overview

This project demonstrates enterprise-grade DevOps practices for Azure infrastructure deployment:

- **Infrastructure as Code**: Terraform manages all Azure resources
- **Configuration Management**: Ansible handles VM configuration and software installation
- **CI/CD Pipeline**: Azure DevOps automates the entire deployment process
- **Idempotency**: Safe to run multiple times without unintended changes
- **Modularity**: Easy to extend and customize for different environments

## ✅ Prerequisites

Before running this project, ensure you have:

### Required Tools
- **Azure Subscription**: Active Azure account with appropriate permissions
- **Terraform**: Version 1.6.0 or higher
- **Ansible**: Version 2.9 or higher with `azure.azcollection` installed
- **Azure CLI**: For authentication and resource management
- **Azure DevOps Account**: For pipeline execution

### Azure Service Principal
Create a service principal for Terraform and Ansible:
```bash
az ad sp create-for-rbac --name "terraform-sp" --role="Contributor" --scopes="/subscriptions/{subscription-id}"
```

### SSH Key Pair
Generate an SSH key for VM access:
```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/azure_vm_key
```

## 📁 Repository Structure

```
CokaCola-Cloud-Engineer/
├── terraform/
│   ├── main.tf              # Main infrastructure definition
│   ├── variables.tf         # Input variables
│   └── outputs.tf           # Output values
├── ansible/
│   └── playbook.yml         # VM configuration playbook
├── azure-pipelines.yml      # CI/CD pipeline definition
└── README.md                # This file
```

## 🚀 Getting Started

### 1. Clone the Repository
```bash
git clone https://github.com/el3ashe2/CokaCola-Cloud-Engineer.git
cd CokaCola-Cloud-Engineer
```

### 2. Configure Variables
Create a `terraform.tfvars` file in the `terraform/` directory:
```hcl
resource_group_name = "rg-myapp-prod"
location            = "East US"
vm_name             = "vm-myapp-001"
admin_username      = "azureuser"
ssh_public_key      = "ssh-rsa AAAAB3NzaC1yc2E... your-key-here"
```

### 3. Initialize Terraform
```bash
cd terraform
terraform init
terraform validate
terraform plan
```

### 4. Deploy Infrastructure
```bash
terraform apply -auto-approve
```

### 5. Configure VM with Ansible
```bash
cd ../ansible
ansible-playbook -i inventory playbook.yml
```

## 🔄 Running the Pipeline

### Azure DevOps Setup

1. **Create a New Project** in Azure DevOps

2. **Set Up Service Connection**:
   - Go to Project Settings → Service connections
   - Create new Azure Resource Manager connection
   - Use Service Principal authentication

3. **Configure Pipeline Variables**:
   ```
   TF_STATE_RG: terraform-state-rg
   TF_STATE_STORAGE: tfstatestorage
   SSH_PUBLIC_KEY: $(your-ssh-public-key)
   ```

4. **Create Pipeline**:
   - Select Azure Repos Git
   - Choose existing Azure Pipelines YAML file
   - Select `azure-pipelines.yml`

5. **Run Pipeline**:
   - Click "Run pipeline"
   - Monitor stages: Validate → Plan → Deploy

### Pipeline Stages

- **Validate**: Checks Terraform syntax and configuration
- **Plan**: Generates execution plan showing what will be created
- **Deploy**: Applies infrastructure changes and runs Ansible configuration

## ⚙️ Configuration

### Terraform Variables

| Variable | Description | Default | Required |
|----------|-------------|---------|----------|
| `resource_group_name` | Azure resource group name | `rg-terraform-demo` | No |
| `location` | Azure region | `East US` | No |
| `prefix` | Resource naming prefix | `tfvm` | No |
| `vm_name` | Virtual machine name | `demo-vm` | No |
| `vm_size` | VM size/SKU | `Standard_B2s` | No |
| `admin_username` | Admin username for VM | `azureuser` | No |
| `ssh_public_key` | SSH public key for access | - | **Yes** |

### Ansible Configuration

The playbook installs and configures:
- Essential utilities (curl, wget, git, vim, htop)
- Docker and Docker Compose
- Application directory structure
- Security updates

Customize the playbook in `ansible/playbook.yml` to add more tasks.

## 📤 Outputs

After successful deployment, Terraform provides:

```
resource_group_name = "rg-terraform-demo"
vm_name            = "demo-vm"
public_ip_address  = "20.1.2.3"
private_ip_address = "10.0.1.4"
vm_id              = "/subscriptions/.../virtualMachines/demo-vm"
```

### Connecting to Your VM
```bash
ssh azureuser@<public_ip_address> -i ~/.ssh/azure_vm_key
```

## 🐛 Troubleshooting

### Common Issues

**Issue**: Terraform state lock error
```
Solution: Remove the state lock manually or wait for it to expire
terraform force-unlock <lock-id>
```

**Issue**: Ansible connection timeout
```
Solution: Verify NSG rules allow SSH (port 22) and VM is running
az vm list --resource-group <rg-name> --output table
```

**Issue**: Pipeline authentication failure
```
Solution: Verify service principal credentials and permissions
az login --service-principal -u <app-id> -p <password> --tenant <tenant-id>
```

### Validation Commands

```bash
# Check Terraform syntax
terraform fmt -check

# Validate Terraform configuration
terraform validate

# Test Ansible playbook syntax
ansible-playbook --syntax-check playbook.yml

# Check Azure resources
az resource list --resource-group <rg-name> --output table
```

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 📞 Contact & Support

For questions, issues, or suggestions, please open an issue in this repository.

---

**Developed by Ahmed Aamer** | Cloud Engineer | DevOps Specialist
