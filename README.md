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

## 🧠 KQL Query Output

<img width="1521" height="165" alt="image" src="https://github.com/user-attachments/assets/9b3e4625-a876-4192-9265-d5c8e2683d7a" />



