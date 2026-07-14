# HIMS File Manager

**OPD Document Management System** — a Windows desktop app for securely storing, organizing, and tracking scanned patient/OPD records, with role-based access, an audit trail, and a date-based calendar view for uploads.

Current version: **3.0.1**

---

## Features

- 📁 **File & folder management** — upload, download, rename, delete, lock, and preview files, with a recycle bin for recovery
- 🗓️ **Calendar-based uploads** — browse and file documents by date via a dedicated calendar picker
- 🔐 **Role-based permissions** — SuperAdmin, DataManager, RecordControllScan, OPDStaff, CertificationStaff, StatisticianStaff, and Auditor roles, each mapped to its own SQL Server login so access is enforced by the database itself, not just the UI
- 🔑 **PIN-protected locking** for sensitive files/folders
- 💬 **Built-in chat** with @mentions and notifications (toast + sound)
- 🟢 **Live presence** — see who else is online
- 📊 **Admin dashboard** and **audit log**
- 🖼️ **Image preview** and **print preview**
- 🖨️ **Watermarking** on downloads/prints (toggle in Settings)
- 💡 **Suggestions box** for user feedback
- 🗄️ **Database schema export**
- 🛠️ **Remote access troubleshooting tools**
- 🔄 **Automatic update checks** via GitHub Releases (see below)

---

## Tech Stack

- **.NET 10** (Windows Forms, `net10.0-windows`)
- **Microsoft.Data.SqlClient** — SQL Server connectivity
- **BCrypt.Net-Next** — password hashing
- **NAudio** — notification sounds
- **System.Text.Json** + `HttpClient` — GitHub Releases API for update checks (no extra dependency needed)

---

## Requirements

- Windows 10/11
- [.NET 10 Desktop Runtime](https://dotnet.microsoft.com/download) (or the SDK, if building from source)
- SQL Server (Express or higher) — tested against SQL Server with `hims_srs` as the target database

---

## Getting Started

### 1. Set up the database

Run the schema script against your SQL Server instance:

```
db/hims_srs_master_structure-with-calendar.sql
```

This creates the `hims_srs` database, tables, and the roles referenced by the app (`hims_auth`, `hims_superadmin`, `hims_datamanager`, `hims_recordscan`, `hims_opdstaff`, `hims_certstaff`, `hims_statstaff`, `hims_auditor`).

> ⚠️ **Before you commit or publish this repo publicly:** `AppConfig.cs` currently ships with hardcoded fallback passwords for every SQL Server role. If this repo is public (required for the update checker's unauthenticated API calls), rotate all of those passwords on your SQL Server instance and blank out the fallback values in `AppConfig.cs`, relying on `hims_config.json` (local, per-machine) for real credentials instead.

### 2. Configure the connection

On first run, the app creates/reads `hims_config.json` next to the executable for:
- Server IP, ports, and database name
- Bootstrap login credentials
- Per-role SQL Server logins
- Storage root folder for uploaded files (defaults to a `HIMS_Storage` folder next to the `.exe` if left blank)

You can also edit these from **Settings** inside the app (SuperAdmin only) once logged in.

### 3. Build & run

```
git clone https://github.com/itszaheerlgs/HIMS-File-Manager-Enterprise.git
cd HIMS-File-Manager-Enterprise
dotnet build
dotnet run
```

Or open `HIMS_FileManager.sln` in Visual Studio 2022+ and press F5.

### 4. Publish a build

Use one of the included publish profiles under `Properties/PublishProfiles/` (e.g. *Win10-11 x64, self-contained*) via **Build → Publish** in Visual Studio, or:

```
dotnet publish -c Release -r win-x64 --self-contained true
```

---

## Checking for Updates

The app checks GitHub Releases for newer versions two ways:

- **Automatically** — a quiet check runs on login; if a newer release exists, a dialog offers to open the download link. If you're already current, nothing appears.
- **Manually** — click **⟳ Check for Updates** on the toolbar anytime.

### How it works

`UpdateService.cs` calls:
```
GET https://api.github.com/repos/{owner}/{repo}/releases/latest
```
and compares the release's tag (`vX.Y.Z`) against the running app's own `<Version>` from `HIMS_FileManager.csproj`.

### Shipping a new version

1. Bump `<Version>` in `HIMS_FileManager.csproj`.
2. Build/publish the app.
3. On GitHub, go to **Releases → Draft a new release**:
   - Tag it `v<version>` (must match step 1, e.g. `v3.0.2`)
   - Attach the built `.exe` (or a `.zip` of the publish output)
   - Click **Publish release** (not just save as draft — drafts are invisible to the update checker)

The repo must be **Public** for this to work without adding a GitHub token to the app.

---

## Project Structure

```
HIMS_FileManager/
├── FileManagerForm.cs / .Designer.cs   # Main window
├── LoginForm.cs                        # Authentication
├── DashboardAdmin.cs                   # Admin dashboard
├── CheckCalUpload.cs                   # Calendar-based upload picker
├── Formchat.cs                         # Built-in chat
├── Recyclebinform.cs                   # Recycle bin
├── Auditlogform.cs / Auditlogger.cs    # Audit log UI + logging service
├── PermissionService.cs                # Role → permission matrix
├── PresenceService.cs                  # Online/offline presence
├── Notificationservice.cs / Notificationtoastform.cs  # Toast + sound notifications
├── WatermarkService.cs                 # Download/print watermarking
├── UpdateService.cs                    # GitHub Releases update checker
├── SettingsForm.cs                     # DB connection + app settings
├── AppConfig.cs / DbConfig.cs          # Configuration & connection management
├── FileStorage.cs                      # Physical file storage handling
├── db/
│   └── hims_srs_master_structure-with-calendar.sql
└── icon/                               # UI icons
```

---

## License

Copyright © 2026 Dether S. Lagos, Zaheer Lagos. All rights reserved.

This software and its source code are proprietary and confidential.
Unauthorized copying, modification, distribution, or use of this
software, in whole or in part, is strictly prohibited without prior
written permission from the copyright holders.

This system is developed for internal use by [Facility/Organization Name]
and is not licensed for public or third-party use.## License

Copyright © 2026 Dether S. Lagos, Zaheer Lagos. All rights reserved.

This software and its source code are proprietary and confidential.
Unauthorized copying, modification, distribution, or use of this
software, in whole or in part, is strictly prohibited without prior
written permission from the copyright holders.

This system is developed for internal use by [Facility/Organization Name]
and is not licensed for public or third-party use.
---

## Credits

Developed by **Dether / Zaheer Lagos**
