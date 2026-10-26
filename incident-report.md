# SOC Incident Report: SOC-002 Controlled Phishing Simulation

**Date:** 26 October 2026  
**Environment:** Windows 11 virtual machine, Sysmon, Splunk Enterprise  
**Classification:** Simulated phishing interaction  
**Status:** Evidence collected; no compromise demonstrated

## 1. Executive summary

A controlled phishing scenario was created using a fictional IT-support email and a harmless local landing page. The page was hosted on the lab endpoint at `127.0.0.1:8765` and did not request or collect credentials.

Splunk was used to investigate endpoint telemetry forwarded from Sysmon and Windows PowerShell Operational logging. Sysmon Event ID 3 recorded Microsoft Edge connecting to the local landing page. PowerShell Event ID 4104 captured an explicitly generated marker command, confirming that Script Block Logging was being ingested.

The evidence demonstrates a simulated browser connection and functioning PowerShell script-block logging. It does not establish malware execution, credential theft, data exfiltration, or actual endpoint compromise.

## 2. Scenario and scope

The fictional email subject was:

`[SOC LAB] Urgent Account Verification Required`

It contained an account-verification link pointing to a local training page. The sender used the reserved example address `it-support@example.test`.

The investigation reviewed the browser's connection to the local service, relevant endpoint logs, PowerShell script-block logging, and a separate benign reference-domain lookup in VirusTotal.

## 3. Evidence timeline

| Time | Evidence | Interpretation |
|---|---|---|
| 12:31:57 | Sysmon Event ID 3: `powershell.exe`, PID 9168, connected to `127.0.0.1:8765` | Earlier controlled HTTP test |
| 13:41:43 | Sysmon Event ID 3: `msedge.exe`, PID 8752, connected to `127.0.0.1:8765` | Browser connection to the simulation page |
| 13:41:46 | Sysmon Event ID 3: same Edge PID and destination | Additional connection to the local service |
| 14:08:56 | PowerShell Event ID 4104 recorded `Write-Output "SOC002_SIMULATED_POST_CLICK_ACTIVITY"` | Confirms script-block logging; this later marker is not proof of a consequence of the browser interaction |

The `System` process (PID 4) also appeared at the same connection timestamps. Those rows are associated with the same connection events and should not be described as additional browser clicks.

## 4. Technical findings

### Finding 1 — Browser connection to the controlled landing page

Sysmon Event ID 3 recorded `msedge.exe` connecting to `127.0.0.1` on port `8765`. This supports the conclusion that the browser connected to the local simulation service.

### Finding 2 — PowerShell logging was operational

PowerShell Operational Event ID 4104 captured the explicitly generated marker command. This demonstrated that script-block telemetry was being ingested into Splunk.

### Finding 3 — Threat-intelligence lookup

A separate VirusTotal lookup for `example.com` displayed zero vendor detections. This is a benign reference-domain result, not a verdict on the local simulation page or proof that a phishing indicator was cleared. Zero detections do not guarantee that a URL is safe.

## 5. Limitations

- The landing page was hosted on loopback and was not an external phishing site.
- No credentials were collected and no malware was executed.
- The observed connection events do not prove exploitation or compromise.
- The PowerShell marker was generated separately and later than the browser interaction.
- The VirusTotal lookup concerned `example.com`, not the simulation page.
- The evidence reviewed does not establish malicious external communication, persistence, or data exfiltration.

## 6. Recommended response for a real suspected phishing incident

1. Preserve the original email, headers, URL, attachments, and relevant endpoint logs.
2. Extract and validate the actual URL, sender, headers, and any attachments.
3. Correlate browser, process-creation, DNS, and network events around the reported click.
4. Investigate suspicious process execution or external connections following the click.
5. Contain the endpoint if evidence supports compromise, following the organisation's incident-response procedure.
6. Document findings, confidence, containment actions, and recovery steps.

## 7. Conclusion

This exercise demonstrated a repeatable workflow for creating a controlled phishing scenario, collecting endpoint telemetry, searching logs in Splunk, and recording evidence. The results support a simulated browser interaction with a local landing page and successful PowerShell script-block logging. They do not demonstrate a real security compromise.

## Project summary

A controlled phishing simulation and SOC investigation using Windows 11, Sysmon, PowerShell Operational logging, and Splunk Enterprise, with an evidence timeline, investigation queries, and incident-response recommendations.
