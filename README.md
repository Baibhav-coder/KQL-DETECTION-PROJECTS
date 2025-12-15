# 1.Impossible Travel Detection (KQL)

This project detects "Impossible Travel" events using Kusto Query Language (KQL).  
An impossible travel alert occurs when a user signs in from two different geographic
locations within a time window that is too short to be physically possible.

This type of anomaly is commonly used in SOC environments to detect:
- Account compromise
- Session hijacking
- Stolen credentials
- Bot-driven login attempts
- Suspicious geographic jumps

-----------------------------------------------------------------------------------------------

## 📌 Detection Logic

The logic for impossible travel is based on comparing each user's login
with their previous login:

1. Sort all login events by **User** and **Time**
2. Capture the **previous login location**  
3. Capture the **previous login time**
4. Calculate the **time difference** between current and previous login
5. Flag the event if:
   - The location changed  
   - And the time difference is ≤ 5 minutes  
   - Meaning physical travel is impossible

-----------------------------------------------------------------------------------------------
🛡 MITRE ATT&CK Mapping

| ID        | Technique            |
| --------- | -------------------- |
| **T1078** | Valid Accounts       |
| **T1098** | Account Manipulation |
| **T1556** | Authentication Abuse |

-----------------------------------------------------------------------------------------------

## 🧠 KQL Query Output

<img width="1521" height="165" alt="image" src="https://github.com/user-attachments/assets/9b3e4625-a876-4192-9265-d5c8e2683d7a" />

-----------------------------------------------------------------------------------------------
# 2.Is Rare Location
This project identifies “rare” or “first-time” login locations using Kusto Query Language (KQL).
A rare-location alert occurs when a user logs in from a country they have never logged in from before in their entire login history.

This detection is commonly used by SOC teams to identify:

Credential theft

Malicious login attempts

Attacker lateral movement

First-seen country anomalies

-----------------------------------------------------------------------------------------------

📌 Detection Logic

The logic for rare-location anomaly detection is based on analyzing a user’s historical login patterns:

Sort all login events by User and Time

Retrieve the previous login location

Identify when the user logs in from a new country

Mark it as rare if:

It is not the user’s first login

The location has changed from their earlier history

-----------------------------------------------------------------------------------------------
🛡 MITRE ATT&CK Mapping

| ID            | Technique                                 |
| ------------- | ----------------------------------------- |
| **T1078**     | Valid Accounts                            |
| **T1588.002** | Acquire Infrastructure (Cloud/VPN/Proxy)  |
| **T1606.002** | DNS Traffic — Unusual Geographic Patterns |

-----------------------------------------------------------------------------------------------
🧠 KQL Query Output

<img width="1552" height="397" alt="image" src="https://github.com/user-attachments/assets/d32d61a3-1057-4eff-b2ea-324eacd7d899" />

-----------------------------------------------------------------------------------------------

# 3.Password Spraying Detection (KQL)

This detection identifies password spraying attacks using Kusto Query Language (KQL).
Password spraying occurs when an attacker attempts the same password against multiple user accounts, avoiding account lockouts and increasing the chance of compromise.

This type of analytic is widely used in SOC environments to detect:

Credential stuffing

Stolen password attacks

Early-stage account compromise

Automated bot-driven authentication attempts

Adversaries performing broad login enumeration

Once combined with a successful login, it becomes a high-confidence compromise alert.

-----------------------------------------------------------------------------------------------

📌 Detection Logic

The logic correlates failed spray attempts with a successful login from the same IP:

Filter for failed login attempts

Group by IP to determine attacker behavior

Calculate:

Number of unique users targeted

Total failed attempts

List of attacked users

Last failure time

Flag IPs that targeted 3 or more unique users

Re-join with the full log to check if the IP later had a successful login

Calculate the time difference between last failed attempt and success

Flag if the success occurred within 0–5 minutes, indicating compromise

Produce a summary report of attacker activity

This correlates spray → success → time window to identify true compromises.

-----------------------------------------------------------------------------------------------
🛡 MITRE ATT&CK Mapping

| ID            | Technique                         |
| ------------- | --------------------------------- |
| **T1110.003** | Password Spraying                 |
| **T1078**     | Valid Accounts (Compromise Phase) |
| **T1110**     | Credential Access                 |
| **T1589**     | Identity Information Gathering    |

-----------------------------------------------------------------------------------------------
🧠 KQL Query Output

<img width="1522" height="198" alt="image" src="https://github.com/user-attachments/assets/25b3b06e-8af5-43bc-8e99-24ab773755da" />



<img width="1552" height="297" alt="image" src="https://github.com/user-attachments/assets/a5ff4068-fdff-4204-a0fb-b4f3917077cc" />


-----------------------------------------------------------------------------------------------
# 4.OAuth Token Misuse Detection (KQL)

This project detects OAuth Token Misuse / Token Hijacking using Kusto Query Language (KQL).
OAuth token misuse occurs when a stolen or compromised token is used from different geographic locations or IP addresses within a short time window — something a legitimate user cannot physically do.

This type of anomaly is commonly used in SOC environments to detect:

OAuth token theft

Session hijacking

Attacker reusing stolen bearer tokens

API abuse

Impossible OAuth travel

Multi-country token replay

-----------------------------------------------------------------------------------------------

📌 Detection Logic

The logic for OAuth token misuse is based on analyzing how a single token is used over time:

Identify tokens used from multiple IP addresses

Identify tokens used from multiple geographic locations

Reconstruct the timeline of each token’s activity

Capture the previous location for each token

Capture the previous usage time

Calculate the time difference between the two uses

Flag the event if:

The location changed

And the time difference is < 5 minutes

Meaning the token was used in a physically impossible way

Strong indicator of token theft or hijacking

-----------------------------------------------------------------------------------------------

🛡 MITRE ATT&CK Mapping

| ID        | Technique                    |
| --------- | ---------------------------- |
| **T1078** | Valid Accounts               |
| **T1539** | Steal Web Session Cookie     |
| **T1550** | Use of Authentication Tokens |
| **T1556** | Authentication Abuse         |

-----------------------------------------------------------------------------------------------
🧠 KQL Query Output

<img width="1552" height="401" alt="image" src="https://github.com/user-attachments/assets/a5d8e424-17da-4350-8715-ee3d5820b9c6" />

-------------------------------------------------------------------------------------------------

# 5. Detection of MFA Push-Bombing via Burst Analysis and Approval Correlation (KQL)

This project detects MFA fatigue / push-bombing attacks using Kusto Query Language (KQL).
Push-bombing occurs when an attacker repeatedly triggers MFA prompts hoping the victim mistakenly approves one, a tactic used in modern breaches including LAPSUS$, Scattered Spider, and multiple access-broker groups.

This detection identifies:

High-frequency MFA challenge bursts

Multiple IP or location origins (botnet or VPN abuse)

MFA approvals occurring inside the burst window

Strong indicators of account compromise

-----------------------------------------------------------------------------------------------

📌 Detection Logic

The logic for detecting MFA fatigue attacks is based on analyzing bursts of MFA challenges:

Count MFA challenges in 5-minute windows

Detect unusual diversity in IP addresses

Detect unusual diversity in geographical locations

Extract the attack window (start and end timestamps)

Correlate with any MFA approval events

Flag the attack if:

The user receives > 5 MFA challenges in 5 minutes

AND the challenges come from multiple IPs or locations

AND an MFA_Success occurs inside the attack window

This represents a high-confidence push-bombing compromise, where the attacker successfully abused MFA fatigue.

🛡 MITRE ATT&CK Mapping

| ID        | Technique                                      |
| --------- | ---------------------------------------------- |
| **T1621** | Multi-Factor Authentication Request Generation |
| **T1078** | Valid Accounts                                 |
| **T1110** | Brute Force (MFA Push Abuse Variant)           |
| **T1098** | Account Manipulation                           |


-----------------------------------------------------------------------------------------------

🧠 KQL Query Output

<img width="1452" height="152" alt="image" src="https://github.com/user-attachments/assets/992161cf-22b8-4be7-b4ad-dfb1df1f46d6" />
