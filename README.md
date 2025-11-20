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

#3.Password Spraying Detection (KQL)

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
