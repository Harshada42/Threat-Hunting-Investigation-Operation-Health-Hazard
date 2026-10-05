# Threat Hunting Investigation: Operation Health Hazard

## Overview
This repository contains the structured investigation notes, timeline reconstruction, and forensic analysis for **Operation Health Hazard**, a simulated supply chain compromise scenario. The investigation tracks a multi-stage attack involving a trojanized third-party npm package, automated payload deployment via obfuscated PowerShell scripts, and registry-based persistence on a developer endpoint (`paw-tom`)[cite: 40].

---

## Incident Summary

* **Target Host:** `paw-tom`[cite: 40]
* **Target User:** `tom@pawpress.me`[cite: 40]
* **Incident Date:** June 21, 2025[cite: 40]
* **Attack Vector:** Software Supply Chain Compromise (Malicious npm Package)[cite: 40]
* **Outcome:** Investigation successfully validated and hypothesis proven[cite: 44].

---

## Attack Chain Reconstruction

### Stage 1: Initial Access
* **Tactic:** Initial Access (`TA0001`)[cite: 40]
* **Technique:** Supply Chain Compromise (`T1195`)[cite: 40]
* **Timestamp:** June 21, 2025, 10:58:24 AM[cite: 40]
* **Description:** An attacker leveraged a compromised third-party npm package (`healthchk-lib@1.0.1`) during routine dependency installation (`npm install`) to gain initial access to host `paw-tom`[cite: 40].

![Initial Access Stage](initial%20access.png)

---

### Stage 2: Execution
* **Tactic:** Execution (`TA0002`)[cite: 41]
* **Technique:** Command and Scripting Interpreter (`T1059`)[cite: 41]
* **Timestamp:** June 21, 2025, 10:58:27 AM[cite: 41]
* **Description:** The package's postinstall lifecycle script executed an obfuscated command string via `cmd.exe` and `powershell.exe`, utilizing hidden windows and encoded commands to download a secondary payload (`SystemHealthUpdater.exe`) into the user's `%APPDATA%` directory[cite: 41].

![Execution Stage](execution.png)

---

### Stage 3: Persistence
* **Tactic:** Persistence (`TA0003`)[cite: 42]
* **Technique:** Boot or Logon Autostart Execution (`T1547`)[cite: 42]
* **Timestamp:** June 21, 2025, 11:03:00 AM[cite: 42]
* **Description:** The attacker established persistence by creating a Run registry key (`Windows Update Monitor`) configured to automatically execute the encoded PowerShell payload upon system startup and user logon[cite: 42].

![Persistence Stage](persistence.png)

---

## Indicators of Compromise (IOCs)

| Indicator Type | Value / Artifact |
| :--- | :--- |
| **Malicious Package** | `healthchk-lib@1.0.1`[cite: 43] |
| **Payload Binary** | `%APPDATA%\SystemHealthUpdater.exe`[cite: 43] |
| **C2 Download URL** | `http://global-update.windows.thm/SystemHealthUpdater.exe`[cite: 43] |
| **Execution Flags** | `powershell.exe -NoP -W Hidden -EncodedCommand`[cite: 43] |
| **Persistence Registry Key** | `HKCU\Software\Microsoft\Windows\CurrentVersion\Run\Windows Update Monitor`[cite: 43] |

![Indicators of Compromise](Indicators%20of%20Compromise%20(IOCs).png)

---

## Scenario Completion & Verification

![Scenario Completion](scenario%20completion.png)

---

## Remediation & Recommendations
1. **Endpoint Isolation:** Immediately isolate host `paw-tom` from the network to prevent potential lateral movement or data exfiltration.
2. **Artifact Removal:** Delete the malicious binary (`SystemHealthUpdater.exe`) from `%APPDATA%` and remove the unauthorized Run key from the Windows Registry.
3. **Credential Rotation:** Revoke active sessions and rotate credentials for the affected user account (`tom@pawpress.me`)[cite: 40].
4. **Perimeter Defense:** Block communication to the adversary infrastructure domain (`global-update.windows.thm`) at the network gateway[cite: 43].
5. **Supply Chain Audit:** Perform a comprehensive audit of all project dependencies and software bill of materials (SBOM) across development environments to ensure no other repositories contain unauthorized third-party packages.
