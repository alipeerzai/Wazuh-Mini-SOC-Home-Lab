
# Wazuh Mini SOC Home Lab

## Overview
This repository documents a small home lab Security Operations Centre (SOC) build using Wazuh SIEM. The aim was to deploy a SIEM, onboard a Linux endpoint, validate telemetry ingestion, and perform basic event triage using real endpoint activity.

The lab is isolated and used only for defensive learning and monitoring.

## Lab architecture
Wazuh SIEM server: Wazuh all in one VM (manager, indexer, dashboard)  
Endpoint: Kali Linux 2025.3 with Wazuh agent (agent name: kali-endpoint, agent id: 001)

## Objectives
Deploy and access the Wazuh dashboard successfully.  
Onboard one endpoint and confirm keep alive and inventory visibility.  
Generate controlled security relevant events on the endpoint.  
Locate events in Threat Hunting, open full document details, and record triage context.  
Capture screenshots as evidence for a portfolio.

## Setup summary
### Wazuh dashboard access
The Wazuh VM was configured with bridged networking to allow browser access from the host. After service validation, the Wazuh interface was accessible and ready for endpoint onboarding.

### Endpoint onboarding (Kali)
The Wazuh agent was installed on Kali and configured to communicate with the Wazuh manager. Service status was verified as active and the agent successfully connected to the server over TCP 1514.

## Evidence collected
Add your screenshots to the `screenshots` folder and link them here.

1. Agent status showing kali-endpoint (001) as Active  
<img width="948" height="811" alt="Agent status " src="https://github.com/user-attachments/assets/06c29a04-6aee-4cb4-be27-726e3bf8e5ae" />

2. Threat Hunting events list showing sudo and PAM activity  
<img width="957" height="972" alt="screenshot A " src="https://github.com/user-attachments/assets/91b4914d-252e-4363-8c3a-54a500d71d87" />

3. Document details showing sudo activity and command context  
Rule id 5403, “First time user executed the sudo command”  
<img width="923" height="841" alt="Screenshot 2026-01-07 160754" src="https://github.com/user-attachments/assets/928349b2-c645-4603-8a8c-56b1b750e0f0" />


4. Document details showing user creation activity  
Rule id 5902, useradd event creating `socdemo`  
<img width="928" height="832" alt="Screenshot 2026-01-07 161013" src="https://github.com/user-attachments/assets/109ec3cc-2b3b-4354-b8f5-95c2ee5c8fa2" />


5. Document details showing password change for `socdemo` (PAM)  
<img width="928" height="832" alt="Screenshot 2026-01-07 161013" src="https://github.com/user-attachments/assets/73918c41-7d55-4803-a4b5-82eb34018d6a" />


6. Document details showing package installation logs (dpkg)  
<img width="906" height="805" alt="apt package" src="https://github.com/user-attachments/assets/6aaa4b12-ee93-45e9-8215-1a1bd2d6369f" />


7. Dashboard summary showing event and authentication counts  
<img width="1907" height="850" alt="scs aa" src="https://github.com/user-attachments/assets/5b267287-cc07-4695-aa2e-277ee7fdcbbd" />


## Controlled tests performed
The following actions were executed on the Kali endpoint to generate auditable security events:

1. Privilege usage and authentication telemetry  
A sudo command was executed to validate privileged activity logging and session correlation.

2. Local account creation  
A new user `socdemo` was created to validate account management telemetry and persistence relevant signals.

3. Credential update  
The password for `socdemo` was changed to generate PAM authentication events and confirm visibility of credential operations.

4. Package installation activity  
A package installation was performed to validate dpkg logging and demonstrate software change visibility.

## Mini triage report
### Triage summary
During the analysis window, the endpoint `kali-endpoint` generated low severity events consistent with controlled administrative activity. The dashboard showed total event ingestion with authentication successes and a small number of authentication failures, with no high severity alerts recorded in the same period.

### Key events reviewed
1. Privileged access via sudo  
Rule id 5403 recorded first time sudo execution, including user context, TTY, working directory, and command line. In a production environment, first time sudo use can indicate potential privilege escalation, so it should be correlated with authorisation and recent authentication history.

2. User account creation  
Rule id 5902 recorded creation of a new local user `socdemo`, including UID, GID, home directory, and shell. Unexpected local user creation is a high value signal for persistence and should be reviewed for legitimacy.

3. Password change event  
PAM logs recorded a password change for `socdemo`, confirming the account was configured for use. In a real environment, this would be correlated with account ownership, change approval, and any subsequent privileged actions.

4. Package installation log  
dpkg logs recorded software installation activity. Software changes are important for threat hunting because attackers may install tooling, but they also occur during normal maintenance, so integrity and provenance checks are essential.

### What I would do next in a real SOC
Confirm whether the privileged action and account creation were authorised.  
Review recent login history and failed authentication attempts for the same user.  
Check for follow on persistence activity such as cron jobs, new services, or modified sudoers rules.  
Validate package sources and review recent outbound network connections for suspicious destinations.  
If unapproved, isolate the host and reset credentials, then begin a full incident workflow.

## Skills demonstrated
SIEM deployment and initial validation.  
Endpoint onboarding and agent health verification.  
Linux authentication and privilege monitoring (sudo and PAM).  
Event investigation using full log context and rule metadata.  
Security reporting and evidence based documentation.

## Next improvements
Onboard a Windows endpoint and enable Sysmon for higher fidelity endpoint telemetry.  
Create one tuned detection rule or noise reduction filter and document before and after alert volume.  
Expand to a second endpoint and build a small incident timeline report.

## Repository structure
screenshots  
Contains portfolio screenshots exported from the Wazuh interface.

report  
Contains the mini triage report in a standalone file if needed.

## Notes on safe sharing
Screenshots should avoid exposing credentials, tokens, or any sensitive internal identifiers. Local lab IP addresses may be blurred if preferred.

