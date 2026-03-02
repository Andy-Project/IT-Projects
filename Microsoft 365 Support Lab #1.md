## Objective

Practicing solving situations regarding users using Microsoft 365 and simulating tickets.

---

## Environment

- **Admin VM:** Windows 11 Enterprise (browser for Admin Center)
- **User VM:** Windows 11 Pro
- **Ticketing system:** Spiceworks
- **Microsoft 365 tenant:** Licenses and users configured

---

## Task 1: Create User(s) for the Virtual Company

### Created the user in Microsoft 365 Admin Center

- Navigated to:  
  `Users → Active Users → + Add user`
- Entered user information
- Assigned a temporary password to mimic real-world security protocols

<img width="776" height="361" alt="image" src="https://github.com/user-attachments/assets/31267f78-f34d-4756-9714-c8a04a2db2d5" />

---

### Test user login on the Windows 11 Client VM

- During Windows setup, selected **“Set up for school/work.”**
- Entered the user’s company email (Microsoft 365 work account)
- Logged in successfully and was prompted to update the password
- For lab purposes, used a simple password: `Helloworld$`
<img width="778" height="551" alt="Screenshot 2026-03-01 212115" src="https://github.com/user-attachments/assets/93fb58a7-74bd-48a2-a067-30eaa5a73c58" />


---

### Complete Windows authentication setup

- Windows prompted for Windows Hello (face, fingerprint, or PIN)
- Clicked **Next** → directed to 2FA setup
- Completed MFA via Microsoft Authenticator
- Set PIN as user: `147258`

---

### Verify the user appears on the sign-in screen

- The user now shows up on the Windows sign-in screen
- Logged in as Zach Blanket using the new credentials → successful
<img width="780" height="475" alt="Screenshot 2026-03-01 212155" src="https://github.com/user-attachments/assets/5b5b0715-eeb1-4084-8b03-775f9bc2fd47" />
<img width="781" height="587" alt="Screenshot 2026-03-01 212200" src="https://github.com/user-attachments/assets/0937c52d-b272-4de9-817a-888b60287897" />

---

## Task 2: Disabling 2FA

Disabling authentication to make the lab easier.

- Logged into Microsoft Entra Admin Center
- Navigated to:  
  `Overview → Entra ID → Properties → Manage Security Defaults`
- Clicked the drop-down and selected **Disabled**

---

## Troubleshooting Encounter #1 – Login Blocked by RDP Permission Error

After returning to the lab, I attempted to log into the Windows 11 VM using Zach’s employee account and received the following error:

> “To sign in remotely, you need the right to sign in through Remote Desktop Services.”

This was unexpected because I was accessing the VM directly through the Hyper-V console, not using Remote Desktop from another machine. Windows was classifying the session as a Remote Desktop logon type.

---

### Analysis

During Windows installation, I selected:

- Domain join instead  
- Work/School account  
- Signed in using Zach’s Microsoft 365 email  

This likely resulted in the device being either:

- Joined to Microsoft Entra ID  
- Registered with the tenant  
- Or configured with identity-based logon restrictions  

Because I did not verify the device's join state before reinstalling, I cannot definitively confirm which state the device was in.

However, the following was confirmed:

- No separate local administrator account was created.
- The employee account did not have the required logon rights.
- Windows classified the session as requiring Remote Desktop Services permissions.
- No recovery administrator account was available to adjust local security settings.

Although I had full tenant-level administrative rights in Microsoft 365 / Entra, those permissions did not grant local administrative control over the VM.

This is because cloud administrative roles do not override local Windows security policy. Device-level permissions must be explicitly assigned on the system itself unless centrally managed.

The Microsoft 365 / Entra admin can manage:

- Users  
- Groups  
- Devices  
- Cloud security policies  

But cannot bypass local logon restrictions unless the device is properly joined and managed.

Due to the lack of a recovery administrator account, reinstalling Windows was the fastest resolution.

---

## Architecture Correction

After reinstalling Windows:

- The system was intentionally configured as a standalone device.
- A dedicated local administrator account (`Admin`) was created first to ensure recovery access would always be available.

The Windows 11 VM is **not joined** to:

- Microsoft Entra ID  
- Active Directory  
<img width="232" height="70" alt="Screenshot 2026-03-01 212324" src="https://github.com/user-attachments/assets/2686bc8e-f91c-4a7a-a975-ead19b286914" />

In a standalone configuration:

- Windows authenticates only against local user accounts.
- It cannot authenticate against a centralized identity provider.
- Microsoft 365 email accounts cannot be used for Windows sign-in.

For this lab, employee accounts must be created as local Windows user profiles.

---

## Task 3: User Creation

To properly configure the employee account:

- Opened **Computer Management**
- Navigated to:  
  `Local Users and Groups → Users`
- Created new user: Zach
- Configured credentials
- Disabled **“User must change password at next logon”** for lab simplicity

Because Zach is logging in locally, no Remote Desktop permissions are required.

The earlier RDP permission error likely occurred because signing in with a Microsoft 365 work account during setup caused Windows to classify the session with identity policies similar to a Remote Desktop logon. On a standalone VM, these policies conflicted with the lack of local permissions, producing the error.

<img width="789" height="296" alt="Screenshot 2026-03-01 212404" src="https://github.com/user-attachments/assets/aa98157d-fd98-4562-9735-84aecdc77a66" />

---

## Task 4: Sign in to Microsoft 365 as Zach

- Opened browser → `portal.office.com`
- Signed in with Zach’s Microsoft 365 account
<img width="770" height="475" alt="Screenshot 2026-03-01 212554" src="https://github.com/user-attachments/assets/88e011b5-0a33-4c6d-8629-c81361caa7cb" />

- Verified access to:
  - Outlook  
  - Teams  
  - OneDrive  

The employee has access to all assigned services.

---

## Task 5: Password Reset Ticket for Zach

### Scenario

Zach attempts to log in → fails → Calls IT support.

---

### Ticket Creation

- Created a ticket in Spiceworks
- Contact: Zach Blanket
- Assigned To: IT Support (Lab Admin)

<img width="665" height="636" alt="Screenshot 2026-03-01 212629" src="https://github.com/user-attachments/assets/0f794029-fc0f-4ac5-9d80-1bbe0136efb5" />

---

### Resolution

- Went into the Microsoft 365 Admin Center
- Navigated to:  
  `Users → Zach Blanket → Reset password`
- Set password: `Helloworld$`
- Signed the user out of all sessions
- Tested the new password and successfully signed in
- Closed the ticket in Spiceworks
