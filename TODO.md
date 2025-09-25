# TODO List

## 1. Basic Infrastructure
- [ ] Prepare systemd services for `caddy` and the Go tool with `--resume` for config persistence.  
  - [ ] **Test:** Restart the VM and verify Caddy keeps the last configured domain.

- [ ] Restrict Caddy Admin API to `127.0.0.1:2019`.  
  - [ ] **Test:** External request to Admin API must fail (connection refused).

---

## 2. Wizard – Step 0/3: Enter Domain
- [ ] Implement input form for domain entry.  
  - [ ] **Test:** After submission, DB contains `domain = tool.example.com`.

---

## 3. Wizard – Step 1/3: Configure DNS
- [ ] Retrieve VM public IP from Azure IMDS (`169.254.169.254`).  
- [ ] Display user instructions: create A record → VM IP.  
  - [ ] **Test:** Wizard displays the correct IP (compare with `curl ifconfig.me`).

---

## 4. Wizard – Step 2/3: Verify DNS & TLS
- [ ] Implement **Check again** button to validate:  
  - Resolve domain (A/AAAA)  
  - Verify IP matches VM  
  - Confirm TLS certificate issued (staging → production)  
- [ ] On success, change button to **Continue → SSO**.  
  - [ ] **Test:**  
    - Create A record for the domain.  
    - Click **Check again** → status changes to OK.  
    - Verify button redirects to `https://<domain>/wizard/sso`.

---

## 5. Wizard – Step 3/3: SSO Setup
- [ ] Integrate with Azure Entra ID (OIDC, PKCE).  
- [ ] Validate login flow – save `tenant_id`, `client_id`, `redirect_uri`.  
  - [ ] **Test:**  
    - Log in with a Microsoft account.  
    - Verify wizard shows ID token claims.  
    - After success, SSO configuration is persisted.

---

## 6. Wizard Completion
- [ ] Set `initialized_at` in DB.  
- [ ] Lock `/wizard` (accessible only for admin role later).  
- [ ] Redirect user to `/` (tool’s main functionality).  
  - [ ] **Test:**  
    - `/wizard` returns 403.  
    - `/` serves the tool.  
    - Login via SSO works.

---

## 7. Security Enhancements
- [ ] Enable HSTS after wizard completion.  
  - [ ] **Test:** Verify `Strict-Transport-Security` header in HTTPS responses.  

- [ ] Add rate-limiting for `/login` and `/oauth/*`.  
  - [ ] **Test:** Multiple login attempts from one IP → tool returns 429 once the limit is exceeded.

- [ ] Implement one-time token for the first wizard access.  
  - [ ] **Test:** Access without token → 401; with token → wizard opens.