# SOC-002: Controlled Phishing Simulation & SOC Investigation

A hands-on Blue Team project demonstrating a controlled phishing-awareness scenario, Windows endpoint telemetry collection, and investigation in Splunk Enterprise.

> **Scope note:** This was a benign, local simulation. It did not involve credential collection, malware execution, or a real endpoint compromise.

## Objectives

- Create a fictional phishing email and harmless landing page.
- Observe a browser connection using Sysmon network telemetry.
- Confirm PowerShell Script Block Logging is being ingested into Splunk.
- Build an evidence-based incident timeline and response recommendations.
- Practice clear reporting without overstating what the logs prove.

## Lab environment

- Windows 11 virtual machine
- Splunk Enterprise
- Splunk Universal Forwarder
- Sysmon
- Windows PowerShell Operational logging
- Local landing page hosted at `127.0.0.1:8765`

## Scenario

A fictional email titled `[SOC LAB] Urgent Account Verification Required` contains a link to a controlled local training page. The page explicitly states that it is a harmless awareness exercise and does not request credentials.

The sender uses the test address `it-support@example.test`.

## Key evidence

| Time (26 Oct 2026) | Evidence | Interpretation |
|---|---|---|
| 12:31:57 | Sysmon Event ID 3: `powershell.exe` PID 9168 connected to `127.0.0.1:8765` | Earlier controlled HTTP test |
| 13:41:43 | Sysmon Event ID 3: `msedge.exe` PID 8752 connected to `127.0.0.1:8765` | Browser connection to the simulation page |
| 13:41:46 | Sysmon Event ID 3: same Edge PID and destination | Additional connection to the local service |
| 14:08:56 | PowerShell Event ID 4104 recorded `Write-Output "SOC002_SIMULATED_POST_CLICK_ACTIVITY"` | Confirms script-block logging; not proof that the browser interaction caused this command |


## Investigation queries

The SPL queries are in [`spl-queries/`](spl-queries/).

## Threat-intelligence note

A separate VirusTotal lookup for `example.com` displayed zero vendor detections. This was a benign reference-domain lookup, not a verdict on the local simulation page. 

## Findings

1. Sysmon Event ID 3 recorded Microsoft Edge connecting to the local landing page at `127.0.0.1:8765`.
2. PowerShell Event ID 4104 captured the explicitly generated marker command, showing that Script Block Logging was working.
3. The evidence collected does not demonstrate malware execution, credential theft, persistence, data exfiltration, or endpoint compromise.

## Recommended response in a real incident

1. Preserve the original email, headers, URL, and relevant endpoint logs.
2. Correlate browser, process-creation, DNS, and network telemetry around the reported click.
3. Investigate suspicious process execution or external connections after the click.
4. Contain the endpoint if evidence supports compromise, following organisational procedures.
5. Record findings, confidence, containment actions, and recovery steps.

## Repository contents

- `incident-report.md` — Check it out for a detailed incident report 
- `spl-queries/` — Check it out for reusable splunk searches
- `evidence/` — Check it out for evidence which are in the form of screenshots.

## Reproduce the investigation

1. Set up a Windows lab VM with Sysmon and the Splunk Universal Forwarder.
2. Configure Splunk to receive endpoint events and confirm Sysmon Event ID 3 and PowerShell Event ID 4104 are indexed.
3. Host a harmless landing page on loopback and use a fictional email containing the local link.
4. Open the local page in the lab browser.
5. Run the provided searches and compare the results with the documented evidence.


## Skills demonstrated

- SIEM searching with Splunk SPL
- Windows endpoint telemetry interpretation
- Sysmon network-connection investigation
- PowerShell Script Block Logging
- Timeline construction and incident documentation
- Evidence-based reporting and threat-intelligence
