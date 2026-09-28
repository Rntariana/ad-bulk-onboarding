# AD Bulk Employee Onboarding (PowerShell)

A PowerShell script that automates new-hire onboarding. It reads a CSV file, creates Active Directory user accounts, adds them to security groups, and provisions a private home folder for each user. Every run produces a CSV audit log.

## Features
- CSV-driven: one row per new hire
- Duplicate detection (skips users that already exist)
- Security group assignment
- Home folder creation with per-user Modify permissions
- `-WhatIf` dry-run support to preview changes safely
- Per-user error handling, so one bad row doesn't stop the batch
- Timestamped CSV log of every run

## Requirements
- Windows PowerShell 5.1+ or PowerShell 7
- Domain-joined machine with the RSAT Active Directory module
- Permission to create AD users and write to the file share

## Usage

Dry run first (changes nothing):
```powershell
.\New-BulkOnboarding.ps1 -CsvPath .\new_hires.sample.csv -HomeRoot '\\FS01\Home' -WhatIf
```

Real run:
```powershell
.\New-BulkOnboarding.ps1 -CsvPath .\new_hires.sample.csv -HomeRoot '\\FS01\Home' `
    -DefaultOU 'OU=Staff,DC=contoso,DC=local' -UpnSuffix 'contoso.local'
```

You will be prompted for the initial password.

## CSV format

| Column | Required | Notes |
|---|---|---|
| FirstName, LastName | Yes | Username is created as `first.last` |
| Department, Title | Yes | |
| OU | No | Overrides the default OU |
| Groups | No | Semicolon-separated, e.g. `GG-Finance;GG-VPN-Users` |

## How it works
1. Validates the CSV and required columns
2. Builds the username and checks whether it already exists
3. Creates the AD account (enabled, must change password at first logon)
4. Adds the user to the listed groups
5. Creates the home folder and grants the user Modify rights
6. Writes a summary and a CSV log

## Screenshots

**Dry run (`-WhatIf`)**
![Dry run](screenshots/dryrun.png)

**Users created in Active Directory**
![AD users](screenshots/ad-users.png)

**Home folders created**
![Home folders](screenshots/folders.png)

## Security notes
- The initial password is entered as a SecureString and is never written to disk or the log.
- Accounts must change their password at first logon.
- The repo contains sample data only. No real employee data.

## Tested in
Lab environment: Windows Server 2022 domain controller, Windows 11 client.

## Author
Your Name, [LinkedIn](https://linkedin.com/in/your-profile)
