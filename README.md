# Azure Resource Cleanup

Automatically deletes all Azure Resource Groups on a scheduled interval using **GitHub Actions**, **OIDC (OpenID Connect)** authentication, and the **Azure CLI**.

> ⚠️ **Warning**
> This workflow deletes **every Resource Group** in the configured Azure subscription. Use only with a dedicated lab or sandbox subscription

---

## Features

- 🔐 Passwordless authentication using GitHub OIDC
- 🚀 Automated Azure Resource Group cleanup
- ⏰ Scheduled execution every 4 hours
- ▶️ Manual execution using `workflow_dispatch`
- 🔒 Least-privilege Azure RBAC
- 💰 Helps reduce Azure costs by automatically removing unused resources

---

## Workflow

```text
GitHub Actions
       │
       ▼
GitHub OIDC Authentication
       │
       ▼
Azure Login
       │
       ▼
List Resource Groups
       │
       ▼
Delete All Resource Groups
```

---

## Technologies

- GitHub Actions
- Azure CLI
- Microsoft Entra ID (Azure AD)
- OpenID Connect (OIDC)
- Azure RBAC

---

## Prerequisites

- Azure Subscription
- Microsoft Entra App Registration
- Federated Credential (GitHub OIDC)
- Custom Azure RBAC Role
- GitHub Repository Secrets

---

## Required GitHub Secrets

| Secret | Description |
|---------|-------------|
| `AZURE_CLIENT_ID` | Application (Client) ID |
| `AZURE_TENANT_ID` | Directory (Tenant) ID |
| `AZURE_SUBSCRIPTION_ID` | Azure Subscription ID |

---

## Required Permissions

```yaml
permissions:
  id-token: write
  contents: read
```

---

## Workflow Schedule

Runs automatically every **4 hours** and can also be triggered manually.

```yaml
on:
  schedule:
    - cron: '0 */6 * * *'
      timezone: 'Asia/Kolkata'
  workflow_dispatch:
```

---

## Cleanup Command

```powershell
az group list --query "[].name" -o tsv | ForEach-Object {
    az group delete --name $_ --no-wait --yes
}
```

---

## Azure RBAC

The workflow uses a **custom Azure RBAC role** following the **Principle of Least Privilege**.

The service principal has **only** the following permissions:

- Read Resource Groups
- Delete Resource Groups

It **cannot**:

- Create Resource Groups
- Create Azure Resources
- Modify Azure Resources
- Assign Roles
- Perform administrative actions

---

## Repository Structure

```text
.
├── .github
│   └── workflows
│       └── cleanup.yml
└── README.md
```

---

## Security

This project uses **GitHub OIDC** instead of client secrets.

Benefits include:

- No stored passwords or client secrets
- Short-lived access tokens
- Secure trust between GitHub and Azure
- Microsoft-recommended authentication method

---

## Disclaimer

This project is intended for **Azure learning and lab environments only**.

Do **not** use it against production subscriptions unless you fully understand the impact.

---

## License

MIT
