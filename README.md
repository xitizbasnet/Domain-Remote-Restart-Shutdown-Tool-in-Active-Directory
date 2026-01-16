# 🖥️ Domain Remote Restart & Shutdown Tool in Active Directory

---

## 📌 Overview

Managing a large Active Directory (AD) domain can become complex when routine maintenance requires restarting or shutting down multiple computers simultaneously. Manual handling is inefficient, error-prone, and risky—especially when critical servers are involved.

This repository documents a **professional, automated, and scalable domain power management tool** built using **Batch scripting and PowerShell**, designed specifically for **Windows Server and Active Directory environments**.

The tool enables IT administrators to:

* 🔍 Automatically discover all domain-joined computers
* 🛡️ Safely exclude servers or sensitive systems
* 🔁 Perform bulk restart or shutdown operations
* 📊 Receive real-time success and failure feedback

This approach avoids blind execution and introduces **control, visibility, and operational safety**, making it suitable for **enterprise IT operations**.

---

## 🎯 Why This Tool Matters

This solution is critical for IT professionals because it is:

* **Scalable** – Works with any number of domain computers
* **Safe** – Built-in exclusion mechanism protects servers
* **Transparent** – Live execution status per machine
* **Auditable** – Predictable, repeatable operational flow
* **Administrator-Controlled** – Requires elevated privileges

---

## 🧰 Technical Requirements

Before using this tool, ensure the following:

* Windows Server environment with **Active Directory**
* RSAT / Active Directory PowerShell module installed
* Administrator privileges on the domain
* PowerShell execution allowed for AD cmdlets

---

## 📁 Script Creation Procedure

### ✏️ Step 1: Create the Script File

1. Right-click on the **Desktop**
2. Select **New → Text Document**
3. Rename the file using a clear and descriptive name

**Recommended name:**

```
Domain_Restart_Tool
```

Using a descriptive filename reduces operational mistakes and improves maintainability.

---

### 📋 Step 2: Insert the Script Code

To prevent syntax errors, copy and paste the script **exactly as shown below** into the file.

> ⚠️ Do not modify the code unless you fully understand the impact in a production domain.

```
@echo off
setlocal enabledelayedexpansion
title Domain Remote Power Tool (Multi-Exclude Fix)

echo ===================================================
echo [STEP 1] ACTIVE DIRECTORY COMPUTER LIST
echo ===================================================
powershell -Command "Get-ADComputer -Filter * | Select-Object -ExpandProperty Name"
echo ---------------------------------------------------

echo ===================================================
echo [STEP 2] SET EXCLUSIONS
echo ===================================================
echo Type the name(s) to EXCLUDE. 
echo IMPORTANT: Separate names with a PIPE symbol only.
echo Example: SERVER^|DODO^|AGENT1
echo.
set /p "USER_EXCLUDE=Enter name(s) to skip: "

:: If empty, we use a dummy value that won't match anything
if "%USER_EXCLUDE%"=="" (set "FINAL_FILTER=NONE_SPECIFIED") else (set "FINAL_FILTER=%USER_EXCLUDE%")

echo.
echo ===================================================
echo [STEP 3] CHOOSE ACTION
echo ===================================================
:choice
set /P c=Type [R] to Restart, [S] to Shutdown, or [N] to Cancel: 
if /I "%c%" EQU "R" set "PS_CMD=Restart-Computer" & goto :EXECUTE
if /I "%c%" EQU "S" set "PS_CMD=Stop-Computer" & goto :EXECUTE
if /I "%c%" EQU "N" goto :END
goto :choice

:EXECUTE
echo.
echo ===================================================
echo [STEP 4] EXECUTION RESULTS
echo ===================================================

:: We use quotes around the filter to prevent the Batch crash
powershell -Command "$pcs = Get-ADComputer -Filter * | Where-Object { $_.Name -notmatch '%FINAL_FILTER%' } | Select-Object -ExpandProperty Name; foreach ($pc in $pcs) { Write-Host \"Attempting on $pc... \" -NoNewline; try { %PS_CMD% -ComputerName $pc -Force -ErrorAction Stop; Write-Host \"[SUCCESS]\" -ForegroundColor Green } catch { Write-Host \"[FAILED]\" -ForegroundColor Red } }"

echo ---------------------------------------------------
echo Operation Finished.
pause
goto :END

:END
Exit
```

---

## 🔍 Script Breakdown (How It Works)

### 🧭 STEP 1 – Domain Computer Discovery

This section queries **Active Directory** and lists all domain-joined computers:

```
Get-ADComputer -Filter * | Select-Object -ExpandProperty Name
```

✔ Ensures real-time, accurate inventory directly from AD

---

### 🛡️ STEP 2 – Exclusion Configuration

Administrators can protect critical systems (e.g., domain controllers, application servers) by excluding them from execution.

* Multiple exclusions are supported
* Names must be separated using the pipe (`|`) symbol

**Example:**

```
AdServer01|AdServer02|CCTCServer
```

---

### 🔘 STEP 3 – Action Selection

The interactive menu allows the administrator to choose:

* **R** → Restart computers
* **S** → Shutdown computers
* **N** → Cancel operation

The selected option dynamically maps to the correct PowerShell command.

---

### 🚀 STEP 4 – Execution & Live Reporting

Each computer is processed individually with immediate feedback:

* 🟢 **[SUCCESS]** – Command accepted by the remote machine
* 🔴 **[FAILED]** – Machine unreachable or blocked

Common failure causes include:

* Device powered off
* Network disconnection
* Firewall restrictions

---

## 💾 Converting to Executable Format

To make the script executable:

1. Click **File → Save As**
2. Choose the target location (e.g., Desktop)
3. Change the extension from `.txt` to `.bat`
4. Set **Save as type** to **All Files**
5. Save the file

---

## ▶️ Running the Tool

1. Right-click the `.bat` file
2. Select **Run as administrator**

> 🔐 Administrator privileges are mandatory for domain-wide operations.

---

## 📊 Example Execution Result

| Category               | Count |
| ---------------------- | ----- |
| Total Computers        | 5     |
| Excluded               | 2     |
| Offline                | 2     |
| Successfully Restarted | 1     |

This reporting provides **instant operational insight**, allowing administrators to take corrective action immediately.

---

## 🏆 Professional Use Case

This tool is suitable for:

* Patch management cycles
* Maintenance windows
* Large-scale endpoint administration
* Enterprise Active Directory environments

It represents a **professional-grade automation solution** for Windows-based infrastructure management.

---

## 📄 Documentation Metadata

* **Maintained by:** IT Department
* **Audience:** System Administrators / Infrastructure Engineers
* **Repository Type:** Internal / Enterprise GitHub
* **Usage:** Authorized IT Operations Only

---

 
