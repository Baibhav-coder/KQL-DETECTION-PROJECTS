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
-----------------------------------------------------------------------------------------------

## 🧠 KQL Query Output

<img width="1521" height="165" alt="image" src="https://github.com/user-attachments/assets/9b3e4625-a876-4192-9265-d5c8e2683d7a" />

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
🧠 KQL Query Output

<img width="1552" height="397" alt="image" src="https://github.com/user-attachments/assets/d32d61a3-1057-4eff-b2ea-324eacd7d899" />

