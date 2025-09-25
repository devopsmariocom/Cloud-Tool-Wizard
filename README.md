# Cloud Tool Wizard

This project provides a **first-run setup wizard** for a lightweight cloud tool deployed as a VM from the Azure Marketplace.  
The wizard helps the user configure a custom domain, DNS records, TLS certificates, and SSO (Azure Entra ID).

---

## User Flow

1. **Deploy from Azure Marketplace**  
   Deploy a VM running Caddy and the Go-based tool.

2. **Access via IP**  
   Access the tool via the VM's IP, which redirects to the wizard.

3. **Wizard – Step 0/3: Enter Domain**  
   Enter the target domain for the tool.

4. **Wizard – Step 1/3: Configure DNS**  
   Create DNS A/AAAA records pointing the domain to the VM's public IP.

5. **Wizard – Step 2/3: Verify DNS & TLS**  
   Verify DNS resolution and TLS certificate issuance.

6. **Wizard – Step 3/3: SSO Setup**  
   Complete Azure Entra ID SSO setup and validation.

7. **Wizard completion**  
   Save configuration and enable the main tool at root.

---

## Architecture

- **Caddy**: front door, handles ACME (TLS certificates) and reverse proxy.  
- **Go tool**: contains the wizard and the main functionality.  
- **SQLite**: persists configuration (`tool_settings`).  
- **Azure Managed Identity / Service Principal**: for API access.  

---

## Security

- Caddy Admin API listens only on `127.0.0.1:2019`.  
- `/wizard` available only until initialization completes.  
- One-time token required for first wizard access (shown in console at deploy time), supporting airgap environments.  
- After wizard completion: enforce HTTPS and enable HSTS.