## 1. Objective

To create a reproducible, secure architecture for Discord servers utilizing Role-Based Access Control (RBAC) and the Principle of Least Privilege (PoLP).

## 2. Scope of Work

* **Identity & Access Management (IAM):** 
* Restructuring `@everyone` and cosmetic roles to eliminate permission inheritance leaks.
* Implementing a gated verification mechanism (e.g., CAPTCHA/bot verification) to transition users from `@everyone` to `@member`.
* **Privileged Access Management (PAM):** 
* Defining strict boundaries and separation of duties for Staff (Moderator, and Administrator) roles.  
* Enforcing Two-Factor Authentication (2FA) for all accounts with administrative or moderation capabilities.
* **Third-Party Risk Management:** 
* Auditing bot permissions and establishing a "Minimum Viable Permission" matrix to prevent over-privileged integrations.
  
## 3. Threat Model

* **Unauthenticated Spam/Reconnaissance:** Restricting the `@everyone` role to a dedicated "intake" or landing channel to prevent server-wide spam and user enumeration.
* **Privilege Escalation:** Preventing standard users from obtaining moderation capabilities through cosmetic role stacking or inheritance flaws.
* **Supply Chain Attacks:** Limiting the blast radius and data exposure if a third-party bot or integration is compromised.
* **Gateway Compromise:** The verification bot acts as a single point of failure. If the bot's credentials are leaked or its host infrastructure is compromised, attackers can bypass the `@everyone` sandbox and flood the server with unauthenticated accounts.

## 4. Role Permission Matrix (Role Based Access Control)

To make this template easy to implement, the table below is categorized exactly as it appears in the Discord Role Settings interface. 

### Intended server profile: 

This template is intended for public servers, where unknown users can freely come in. Use of verification is non-negotiable to promote to @member. Furthermore, some permissions could be denied and implemented in a "trusted member" role

If your community is private and has vetting done before sending invites, you may at your discretion de-escalate the @member permissions to @everyone and not use a verification bot.

### **Legend:** 

* ✅ = Permitted 
* ❌ = Denied 
* ⚠️ = Restricted via Channel Overrides (Read-Only or Sandboxed) 
*  - = Discretionary / Subject to Community Needs

### General Server Permissions

*Note: The Quarantine Role operates as a negative modifier applied to users violating community standards, effectively sandboxing their capabilities without requiring removal.*

| Permission               | @everyone | @member | @mod | @admin | Quarantine Role |
| :----------------------- | :-------: | :-----: | :--: | :----: | :-------------: |
| **View Channels**        |   ❌/⚠️*   |    ✅    |  ✅   |   ✅    |       ⚠️        |
| **Manage Channels**      |     ❌     |    ❌    |  ❌   |   ✅    |        ❌        |
| **Manage Roles**         |     ❌     |    ❌    |  ❌   |   ✅    |        ❌        |
| **Create Expressions**   |     ❌     |    -    |  -   |   -    |        ❌        |
| **Manage Expressions**   |     ❌     |    ❌    |  -   |   -    |        ❌        |
| **View Audit Log**       |     ❌     |    ❌    |  -   |   ✅    |        ❌        |
| **View Server Insights** |     ❌     |    ❌    |  -   |   -    |        ❌        |
| **Manage Webhooks**      |     ❌     |    ❌    |  ❌   |   ✅    |        ❌        |
| **Manage Server**        |     ❌     |    ❌    |  ❌   |   ✅    |        ❌        |

*\*Note: For `@everyone`, visibility is strictly limited via channel overrides to the designated "intake/verification" channel only. This limits bot spam before verification is complete.*

###  Membership Permissions

| Permission                            | @everyone | @member | @mod | @admin | Quarantine Role |
| :------------------------------------ | :-------: | :-----: | :--: | :----: | :-------------: |
| **Create Invite**                     |     ❌     |    ❌    |  -   |   -    |        ❌        |
| **Change Nickname**                   |     ❌     |    ❌    |  -   |   -    |        ❌        |
| **Manage Nicknames**                  |     ❌     |    ❌    |  ✅   |   ✅    |        ❌        |
| **Kick, Approve, and Reject Members** |     ❌     |    ❌    |  ✅   |   ✅    |        ❌        |
| **Ban Members**                       |     ❌     |    ❌    |  ✅   |   ✅    |        ❌        |
| **Timeout Members**                   |     ❌     |    ❌    |  ✅   |   ✅    |        ❌        |

Note: Nicknames can be used for homoglyph-based moderation evasion; by preventing easy user-searches, management may deem it necessary to change a username to Latin-script characters. Allowing members to change their nickname is not recommended in very large servers.

###  Text Channel Permissions

| Permission                                  | @everyone | @member | @mod | @admin | Quarantine Role |
| :------------------------------------------ | :-------: | :-----: | :--: | :----: | :-------------: |
| **Send Messages and Create Posts**          |     ❌     |    -    |  -   |   ✅    |       -        |
| **Send Messages in Threads and Posts**      |     ❌     |    -    |  -   |   ✅    |       -        |
| **Create Public Threads**                   |     ❌     |    -    |  -   |   ✅    |       -       |
| **Create Private Threads**                  |     ❌     |    -    |  -   |   ✅    |       -        |
| **Embed Links**                             |     ❌     |    -    |  -   |   -    |        ❌        |
| **Attach Files**                            |     ❌     |    -    |  -   |   -    |        ❌        |
| **Add Reactions**                           |     ❌     |    -    |  -   |   -    |        -        |
| **Use External Emoji**                      |     ❌     |    -    |  -   |   -    |        -        |
| **Mention @everyone, @here, and All Roles** |     ❌     |    ❌    |  -   |   ✅    |        ❌        |
| **Manage Messages**                         |     ❌     |    ❌    |  -   |   ✅    |        ❌        |
| **Pin Messages**                            |     ❌     |    ❌    |  -   |   -    |                 |
| **Bypass Slowmode**                         |     ❌     |    ❌    |  -   |   ✅    |        ❌        |
| **Manage Threads and Posts**                |     ❌     |    ❌    |  ✅   |   ✅    |        ❌        |
| **Read Message History**                    |   ❌/⚠️*   |    -    |  ✅   |   ✅    |        -        |
| **Send Text-to-Speech Messages**            |     ❌     |    -    |  -   |   -    |        -        |
| **Send Voice Messages**                     |     ❌     |    -    |  -   |   -    |        -        |
| **Create Polls**                            |     ❌     |    -    |  -   |   -    |        -        |

Note: Read Message history for @everyone is required at minimum in the intake category/channel(s); with overrides it is possible to have the global permission be false but allow new users to read the intake channel.

### Voice Channel Permissions 

| Permission                   | @everyone | @member | @mod | @admin | Quarantine  Role |
| :--------------------------- | :-------: | :-----: | :--: | :----: | :--------------: |
| **Connect**                  |     ❌     |    -    |  -   |   -    |        -        |
| **Speak**                    |     ❌     |    -    |  -   |   -    |        -        |
| **Video**                    |     ❌     |    -    |  -   |   -    |        -        |
| **Use Soundboard**           |     ❌     |    -    |  -   |   -    |        -        |
| **Use External Sounds**      |     ❌     |    -    |  -   |   -    |        -        |
| **Use Voice Activity**       |     ❌     |    -    |  -   |   -    |        -         |
| **Priority Speaker**         |     ❌     |    ❌    |  -   |   -    |        ❌         |
| **Mute Members**             |     ❌     |    ❌    |  -   |   -    |        ❌         |
| **Deafen Members**           |     ❌     |    ❌    |  -   |   -    |        ❌         |
| **Set Voice Channel Status** |     ❌     |    -    |  -   |   -    |        ⚠️        |
| **Use Activities**           |     ❌     |    -    |  -   |   -    |        -        |
| **Use External Apps**        |     ❌     |    -    |  -   |   -    |        -        |

### Event Permissions

| Permission    | @everyone | @member | @mod | @admin | Quarantine Role |
| :------------ | :-------: | :-----: | :--: | :----: | :-------------: |
| Create Events |     ❌     |    ❌    |  -   |   -    |        ❌        |
| Manage Events |     ❌     |    ❌    |  -   |   -    |        ❌        |
### ⛔ Advanced Permissions: Administrator 

**DO NOT ASSIGN THE MASTER ADMINISTRATOR PERMISSION TO ANY ROLE.** 

**Why:** The `Administrator` permission in Discord bypasses all channel-specific overrides and grants the user the ability to self-edit permissions, delete any channel, kick any user, and invite malicious bots. 
1. To prevent catastrophic accidental configuration drift. 
2. To isolate and limit the "blast radius" if a high-ranking staff account is compromised via session hijacking or social engineering. By manually assigning discrete permissions to the `@admin` role rather than flipping the master switch, true separation of duties is maintained.

## Bot Permissions (WIP)

### Verification Bot:
The verification bot is the most privileged third-party integration in this architecture. Because it has the power to transition users from `@everyone` to `@member`, its "blast radius" must be strictly contained.

- **Channel Isolation:** Use category/channel overrides to deny the bot access to all channels except the designated **Intake/Verification** channel. This ensures that if the bot is compromised, the attacker cannot scrape history or spam users in protected channels.
    
- **Role Hierarchy:** The bot's integrated role must be positioned strictly **below** all Staff roles (`@mod`, `@admin`) but **above** the `@member` role. This prevents a compromised bot from escalating its own permissions or stripping roles from staff.
    
- **Permission Hardening:** 
* **Global:** Disable all permissions for the bot role at the server level. 
- **Local:** Only grant "Send Messages" and "Manage Roles" within the Intake channel overrides.
- **Administrator:** **Never** grant the `Administrator` permission to a verification bot, regardless of developer "requirements."
