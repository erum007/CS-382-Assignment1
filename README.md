# CS-382-Assignment1

# **Option 1: Web Application With Online Payment Processing System**

# **Task 1**

This report aims to outline the security architecture and threat model of an internet-facing tourism assistance platform developed as part of TEKNOFEST, a global technology competition project in 2022.  It integrates mapping APIs, AI services, translation systems, mini-games, and payment gateways. The system has the following features: 

- Location-based public services (hotels, restaurants, transport, prayer areas, emergency spots) 
- AI-powered voice assistant to navigate website
- Text and speech translation 
- Trip planning functionality 
- Mini-games with reward logic 
- Virtual wallet with payment gateways integration 
- Associate booking and feedback forms 

The exact technology stack for the application includes: 

- Google Maps API 
- ArcGIS API 
- Microsoft Azure Cognitive Services 
- IBM Watson APIs 
- PayPal 
- Google Forms 
- Node.js 
- Python 
- Vue.js 
- Unity WebGL
- Airtable API

The architecture is cloud-agnostic. The system can be deployed on any infrastructure provider such as public cloud. Third-party APIs (Google, Azure, IBM) are consumed as external services and do not impose infrastructure-level vendor lock-in. The application is assumed to be internet-facing (was in the past, but is now down due to expired API keys and the fact that the console accounts have not been renewed.) 

## **Frontend Layer**

- HTML/CSS/Bootstrap interface was used to design the interface of the web app along with some libraries such as FontAwesome for icons.
- Vue.js framework and components played a key role when designing the UI as well as ensuring the functionality of the nearby-locations feature 
- JavaScript/JQuery logic for a smooth and seamless user experience through event handline, AJAX, effects and animations. 
- Embedded Unity WebGL game in an iframe. 
- Client-side API consumption such as for language translation and location services support.
- Link to Google Forms to partner associate brands' booking and feedback forms
- Trip planning feature working with Airtable database on a webpage on a click.

## **Backend Layer**

- Node.js services to help with various development tasks using the vast number of packages available through Node Package Manager.
- Python-based Watson integration service that was used to develop the web application’s virtual assistant/agent.
- API orchestration layer that deals with failed API calls or makes them faster and scalable.
- Payment verification logic that works with the MetaMask server/payment gateway button (to PayPal).
- Game score validation logic within the developed Unity game.

## **Data Storage**

- User account database (future MySQL support).
- Trip planning feature database.
- Wallet balances gained through games.
- Game reward records based on obtained scores in games.
- Application logs for crashes, etc.
- Associate booking records for all bookings done through the website as well as the feedback given (external on Google server, not associated with application).

## **External Integrations**

- Google Maps & Places APIs for the nearby-locations feature to work.
- ArcGIS API to know exact, detailed directions between two locations.
- Microsoft Azure Translation API for language support (text, voice, picture).
- IBM Watson APIs for the web application’s agent.
- PayPal Payment Gateway integration that can work with user wallet currency.
- MetaMask connected with the user’s wallet. 
- Google Forms for bookings and reviews by customers.
- AirTable embed links into webpage dropdowns.

## **Administrative Access**

- Admin control panel (if need be).
- Log access. 
- Configuration management. 

## **Users and Roles**

| Role | Description |
|------|-------------|
| Guest User | Accesses public features |
| Registered User | Accesses tools and personalization |
| Pro User | Accesses virtual wallet and rewards |
| Administrator | Manages system configurations |
| Associate | Receives bookings/requests and feedback |
| External API Providers | Third-party service providers |

## **Data Types Handled**

- User credentials such as username and password
- Location data 
- Payment data and account credentials, private key/secret recovery phrase.
- API keys 
- Game scores 
- Wallet balances 
- Translation content 
- Voice recordings (temporary processing) 
- Logs and audit records
- Google Forms input (external on Forms URL)

## **Trust Boundaries**

These boundaries represent all the points where data crosses different levels of trust and require security enforcement controls.  

| Component | Trust Level | Why |
|-----------|------------|-----|
| User (All Kinds) | Untrusted | Could be attacker |
| Browser frontend | Untrusted | Can be modified |
| Backend API | Trusted | We control logic |
| Database | Semi-Trusted | External on Airtable/MySQL in future |
| PayPal | Semi-trusted | External system |
| Google APIs | Semi-trusted | External |
| Azure | Semi-trusted | External |
| Watson | Semi-trusted | External |
| Unity game (client-side) | Untrusted | Client can modify |

- The first trust boundary exists between the Internet and the Web Frontend. Due to the Internet exposure, any user or system can send requests to the frontend. This boundary is untrusted and must be protected using measures like HTTPS and web application firewall protections.
- The second boundary exists between the Frontend and Backend Services. Although all of the frontend does belong to the application, it executes within the user’s browser environment and can be modified or tampered with for example if the browser is hijacked. For this reason, the backend treats all frontend input as untrusted. Authentication verification and strict authorization checks are to be enforced at this boundary.
- A third boundary exists between the Backend and the Database (Airtable SaaS platform). Airtable operates as an external managed service and is therefore treated as a semi-trusted component. All database access is performed server-side using secured API credentials. Direct client access to the database should be prohibited to prevent data manipulation or credential leakage.
- Another trust boundary exists between the Backend and the Payment Gateway (PayPal). Payment confirmations and transaction data flow across this boundary. As we know payment-related fraud is a high-risk area, the backend is to perform server-side transaction verification before updating wallet balances.
- The system also has a trust boundary between the Backend and Third-Party APIs such as mapping, AI, and translation services. These services are external dependencies and are treated as semi-trusted. Secure API key storage, restrictions on outbound requests, and rate limiting are to be applied to reduce the possible misuse.
- A critical boundary is also there between the User and the Wallet System. Since wallet balances represent monetary value, attempts to manipulate transactions must be anticipated. All wallet updates are to be validated server-side and protected by integrity checks.
- Finally, a high-privilege trust boundary is there between Administrative Users and the Application Infrastructure. Administrators have elevated access to configuration settings and sensitive data. To mitigate insider threats, the system should have role-based access control (RBAC), multi-factor authentication, and comprehensive logging for all administrative actions.


# **Task 2**

## **Asset Inventory**

| Asset | Type | CIA Priority |
|-------|------|--------------|
| User credentials | Sensitive | C: High |
| Payment transactions | Financial | C: High, I: High |
| Wallet balances | Financial | I: High |
| Game reward logic | Business logic | I: High |
| API keys | Secret | C: High |
| Location data | Personal data | C: High |
| Translation input/output | User data | C: Medium |
| Logs | Audit | I: High |
| Admin credentials | Critical | C: High |
| Static Trip Planning Database (Airtable) based on User Budget |	Business / User Data |	I: High

## **Security Objectives**

### **Confidentiality**
- Protect user and admin data such as credentials, API keys, payment information so unauthorized parties can't access.
- Prevent exposure of location and translation data to ensure privacy and trust.

### **Integrity**
- Prevent wallet tampering such as that of balance.
- Prevent game score manipulation to avoid inflation of score by hacking or reduction of it through an attack. etc.
- Ensure payment authenticity and that only the exact amount involved in the transaction is affected. 
- Protect logs of activities from modification.
- Keep budget trip planning database accurate for smooth trip planning based on budget.

### **Availability**
- Ensure service uptime and minimal downtime.
- Prevent DDoS disruption. 
- Protect API consumption limits to ensure functionality at all times. 

### **Accountability**
- Maintain audit logs. 
- Enable non-repudiation for payments in particular. 
- Log admin actions.


# **Task 3**

## **Threat Model Table (Selected High-Impact Threats)**

| Threat | STRIDE | Component | Impact/Rationale | Risk |
|--------|--------|-----------|-----------------|------|
| Account takeover | Spoofing | Authentication | Can lead to financial fraud | High |
| API key exposure (Google, Azure, Watson, ArcGIS) | Info Disclosure | Frontend | System compromise/API abuse, cost, and reputation affected. | High |
| Wallet manipulation | Tampering | Backend | Financial loss | High |
| Game score manipulation | Tampering | Client | Financial loss | High |
| Fake MetaMask/PayPal callback | Spoofing | Payment System | Financial fraud | High |
| SQL injection in future database | Tampering | Backend | Data breach of data in database | High |
| DDoS attack | DoS | Web tier | Service outage and increased downtime | Medium |
| Admin privilege escalation | Elevation | Admin Panel | Full compromise of all sensitive data | High |
| Unverified free to pro user status | Elevation | Pro User Panel | Financial loss | High |
| Log deletion/modification | Repudiation | Logging | Needed for digital forensics, else loss of evidence happens | Medium |
| API abuse via bots | DoS | Translation API | Cost impact and reputation, exhaustion of limit and more downtime | Medium |
| Location data leakage | Info Disclosure | Maps API | Privacy violation of sensitive data | High |
| Insider misuse | Elevation | Admin | Data compromise | High |
| Zero-day exploit in backend framework or dependency | Elevation of Privilege / Tampering | Backend Services | Unknown vulnerability may allow remote code execution, data breach, or full system compromise | Medium |
| Third-party API compromise (e.g., Google, Azure, PayPal breach) | Information Disclosure / Tampering | Third-Party Integrations | Sensitive data exposure, fraudulent transactions, or corrupted responses affecting system integrity | Medium |
| Large-scale infrastructure outage (hosting/DNS failure) | Denial of Service | Web Tier / Hosting Environment | Complete service unavailability, financial loss, reputational damage | Medium |


# **Task 4**

## **Identity & Access Management**

- Role-Based Access Control (RBAC) 
- Separation of Admin and User planes 
- Multi-Factor Authentication for Free/Pro/Admin Users with password and OTP both, for example
- Obvious but password hashing at the backend (store hashes not plaintext)
- OAuth-based authentication 
- Short-lived access tokens, when a new one is generated, the old one is useless
- Secure session cookies/sessions should timeout in case of inactivity (JWT expiration)

## **Network Segmentation and Controls**

- Public Web Tier (DMZ i.e. public-facing part)  
- Private Application Tier (backend logic) 
- Isolated Database Tier (data storage)
- No direct database exposure 
- Firewall between tiers to control who talks to whom
- Reverse proxy that receives traffic before your real server
- WAF (Web Application Firewall) that blocks attacks
- API Gateway that manages all API requests
- Private subnet backend not directly accessible from the internet
- Database not internet-accessible, DB only talks to backend, never to public
- Outbound API calls are restricted so that that server is only allowed to call specific APIs

## **Data Protection**

- TLS for all communications (HTTPS encryption for travelling data)
- Encryption at rest for database (scrambled data when stored)
- Secure HTTP headers 
- Input validation and sanitization 
- Payment webhook signature verification 

## **Secrets Management**

- API keys stored in environment variables (no keys in code)
- Secrets vault for production 
- Key rotation policy should be opted for to change them periodically
- No client-side secret exposure 
- IAM role based access (only certain components access the secret)

## **Monitoring & Logging**

- Centralized logging server 
- Payment audit trail to log everything
- Admin action logging, again to log everything
- API rate monitoring to detect spam
- Intrusion detection alerts 

## **Payment Security Controls**

- Server-side PayPal verification always, no trusting browser upon payment successful message
- Webhook signature validation to ensure real callbacks
- Idempotency keys to prevent double payment processing
- Fraud detection thresholds to detect anomalies in behavior

## **Game Integrity Controls**

- Server-side score validation, never trust client score, verify always
- Replay detection to prevent use of a reward more than once
- Reward cap enforcement to limit the maximum number to attainable points


# **Task 5**

| Threat | STRIDE Category | Risk Level | Primary Treatment | Treatment Type | Residual Risk | Reason for Residual Risk |
|--------|----------------|------------|-----------------|----------------|---------------|-------------------------|
| Account takeover | Spoofing | High | MFA, RBAC and account lockout | Mitigate | Medium | Phishing and credential stuffing may still succeed |
| API key exposure (Google, Azure, Watson, ArcGIS) | Information Disclosure | High | Secrets vault and  no frontend storage along with key rotation | Mitigate | Low | Backend compromise could still expose all the keys |
| Wallet manipulation | Tampering | High | Server-side validation, transaction logs, and integrity checks | Mitigate | Low | Backend vulnerability could still be exploited |
| Game score manipulation | Tampering | High | Server-side score validation, replay detection | Mitigate | Low | Logic flaws may still exist |
| Fake PayPal / MetaMask callback | Spoofing | High | Webhook signature verification and server-side payment validation | Mitigate | Low | Payment provider compromise is external risk and mostly out of our hands |
| SQL Injection in future database | Tampering | High | Parameterized queries and input validation | Mitigate | Low | New injection vectors may emerge |
| DDoS attack | Denial of Service | Medium | Rate limiting, CDN and WAF | Mitigate | Medium | Large-scale attacks may still overwhelm infrastructure |
| Admin privilege escalation | Elevation of Privilege | High | Strict RBAC, MFA and admin audit logs | Mitigate | Medium | Zero-day or misconfiguration risk remains |
| Unverified free-to-pro upgrade | Elevation of Privilege | High | Server-side subscription verification | Mitigate | Low | Logic bugs may still exist and raise tier level |
| Log deletion/modification | Repudiation | Medium | Log immutability and restricted access | Mitigate | Low | Infrastructure-level compromise may bypass controls |
| API abuse via bots | DoS | Medium | Rate limiting and API usage quotas | Mitigate | Medium | Distributed botnets may bypass limits |
| Location data leakage | Information Disclosure | High | TLS and minimal data storage along with access controls | Mitigate | Medium | Third-party API breach is outside direct control |
| Insider misuse | Elevation | High | Least privilege, logging, activity monitoring | Mitigate | Medium | Privileged users with more access inherently carry risk |
| Zero-day exploit | Multiple | Low | Monitoring and patch management | Accept (partially) | Medium | Cannot predict unknown vulnerabilities |
| Third-party API compromise | Multiple | Medium | Vendor due diligence and limited permissions | Transfer (partially) | Medium | Vendor security is outside direct control |
| Large-scale infrastructure outage | DoS | Medium | Redundancy planning | Accept (partially) | Medium | Cloud-wide outages cannot be fully prevented (AWS outage example) |

Some residual risks remain due to: 

- Insider threats from those who misuse or carelessly handle access to organizational systems and data is highly unpredictable and a risk out of the bounds of all the measures that can be taken technically.
- Third-party API vulnerabilities are out of our control mostly, but developers can keep an eye out in case of updates in response to vulnerabilities.
- Zero-day software flaws can be exploited and these are unpredictable, we can only monitor and patch in case of issues.
- Large-scale attacks distributed denial-of-service attacks may exceed defensive capacity despite rate limiting.
- Activities like phishing or bruteforce for weak passwords may succeed.
- These are mitigated but cannot be eliminated entirely.

# **Task 6**

## **Assumptions & Limitations**

- Cloud-agnostic architecture assumed
- Production deployment assumed
- Internet-facing exposure assumed
- No vendor-specific security services required
- Regulatory compliance not deeply analyzed
- Insider threat considered but not eliminated
- External APIs assumed to be independently secured
