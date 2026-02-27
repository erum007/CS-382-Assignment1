# CS-382-Assignment1
# Task 1

This report aims to outline the security architecture and threat model of an internet-facing tourism assistance platform developed as part of TEKNOFEST, a global technology competition project in 2022. It integrates mapping APIs, AI services, translation systems, mini-games, and payment gateways. The system has the following features:

Location-based public services (hotels, restaurants, transport, prayer areas, emergency spots)  
AI-powered voice assistant to navigate website  
Text and speech translation  
Trip planning functionality  
Mini-games with reward logic  
Virtual wallet with payment gateways integration  
Associate booking and feedback forms  

The exact technology stack for the application includes:

Google Maps API  
ArcGIS API  
Microsoft Azure Cognitive Services  
IBM Watson APIs  
PayPal  
Google Forms  
Node.js  
Python  
Vue.js  
Unity WebGL  

The architecture is cloud-agnostic. The system can be deployed on any infrastructure provider such as public cloud. Third-party APIs (Google, Azure, IBM) are consumed as external services and do not impose infrastructure-level vendor lock-in. The application is assumed to be internet-facing (was in the past, but is now down due to expired API keys and the fact that the console accounts have not been renewed.)

**Frontend Layer**

HTML/CSS/Bootstrap interface was used to design the interface of the web app along with some libraries such as FontAwesome for icons.  
Vue.js framework and components played a key role when designing the UI as well as ensuring the functionality of the nearby-locations feature.  
JavaScript/JQuery logic for a smooth and seamless user experience through event handling, AJAX, effects and animations.  
Embedded Unity WebGL game in an iframe.  
Client-side API consumption such as for language translation and location services support.

**Backend Layer**

Node.js services to help with various development tasks using the vast number of packages available through Node Package Manager.  
Python-based Watson integration service that was used to develop the web application’s virtual assistant/agent.  
API orchestration layer that deals with failed API calls or makes them faster and scalable.  
Payment verification logic that works with the MetaMask server/payment gateway button (to PayPal).  
Game score validation logic within the developed Unity game.

**Data Storage**

User account database (future MySQL support).  
Wallet balances gained through games.  
Game reward records based on obtained scores in games.  
Application logs for crashes, etc.  
Associate booking records for all bookings done through the website as well as the feedback given (external on Google server, not associated with application).

**External Integrations**

Google Maps & Places APIs for the nearby-locations feature to work.  
ArcGIS API to know exact, detailed directions between two locations.  
Microsoft Azure Translation API for language support (text, voice, picture).  
IBM Watson APIs for the web application’s agent.  
PayPal Payment Gateway integration that can work with user wallet currency.  
MetaMask connected with the user’s wallet.  
Google Forms for reviews by customers.

**Administrative Access**

Admin control panel (if need be).  
Log access.  
Configuration management.

**Users and Roles**

| Role | Description |
|------|------------|
| Guest User | Accesses public features |
| Registered User | Accesses tools and personalization |
| Pro User | Accesses virtual wallet and rewards |
| Administrator | Manages system configurations |
| Associate | Receives bookings/requests and feedback |
| External API Providers | Third-party service providers |

**Data Types Handled**

User credentials such as username and password  
Location data  
Payment data and account credentials, private key/secret recovery phrase  
API keys  
Game scores  
Wallet balances  
Translation content  
Voice recordings (temporary processing)  
Logs and audit records  

**Trust Boundaries**

These boundaries represent all the points where data crosses different levels of trust and require security enforcement controls.

| Component | Trust Level | Why |
|------------|------------|-----|
| User (All Kinds) | Untrusted | Could be attacker |
| Browser frontend | Untrusted | Can be modified |
| Backend API | Trusted | We control logic |
| Database | Semi-Trusted | External on Airtable |
| PayPal | Semi-Trusted | External system |
| Google APIs | Semi-Trusted | External |
| Azure | Semi-Trusted | External |
| Watson | Semi-Trusted | External |
| Unity game (client-side) | Untrusted | Client can modify |

The first trust boundary exists between the Internet and the Web Frontend. Due to the Internet exposure, any user or system can send requests to the frontend. This boundary is untrusted and must be protected using measures like HTTPS and web application firewall protections.

The second boundary exists between the Frontend and Backend Services. Although all of the frontend does belong to the application, it executes within the user’s browser environment and can be modified or tampered with for example if the browser is hijacked. For this reason, the backend treats all frontend input as untrusted. Authentication verification and strict authorization checks are to be enforced at this boundary.

A third boundary exists between the Backend and the Database (Airtable SaaS platform). Airtable operates as an external managed service and is therefore treated as a semi-trusted component. All database access is performed server-side using secured API credentials. Direct client access to the database should be prohibited to prevent data manipulation or credential leakage.

Another trust boundary exists between the Backend and the Payment Gateway (PayPal). Payment confirmations and transaction data flow across this boundary. As we know payment-related fraud is a high-risk area, the backend is to perform server-side transaction verification before updating wallet balances.

The system also has a trust boundary between the Backend and Third-Party APIs such as mapping, AI, and translation services. These services are external dependencies and are treated as semi-trusted. Secure API key storage, restrictions on outbound requests, and rate limiting are to be applied to reduce the possible misuse.

A critical boundary is also there between the User and the Wallet System. Since wallet balances represent monetary value, attempts to manipulate transactions must be anticipated. All wallet updates are to be validated server-side and protected by integrity checks.

Finally, a high-privilege trust boundary is there between Administrative Users and the Application Infrastructure. Administrators have elevated access to configuration settings and sensitive data. To mitigate insider threats, the system should have role-based access control (RBAC), multi-factor authentication, and comprehensive logging for all administrative actions.

# Task 2

**Asset Inventory**

| Asset | Type | CIA Priority |
|--------|------|-------------|
| User credentials | Sensitive | C: High |
| Payment transactions | Financial | C: High, I: High |
| Wallet balances | Financial | I: High |
| Game reward logic | Business logic | I: High |
| API keys | Secret | C: High |
| Location data | Personal data | C: High |
| Translation input/output | User data | C: Medium |
| Logs | Audit | I: High |
| Admin credentials | Critical | C: High |

**3.2 Security Objectives**

**Confidentiality**

Protect user and admin data such as credentials, API keys, payment information so unauthorized parties cannot access.  
Prevent exposure of location and translation data to ensure privacy and trust.

**Integrity**

Prevent wallet tampering such as that of balance.  
Prevent game score manipulation to avoid inflation of score by hacking or reduction of it through an attack.  
Ensure payment authenticity and that only the exact amount involved in the transaction is affected.  
Protect logs of activities from modification.

**Availability**

Ensure service uptime and minimal downtime.  
Prevent DDoS disruption.  
Protect API consumption limits to ensure functionality at all times.

**Accountability**

Maintain audit logs.  
Enable non-repudiation for payments in particular.  
Log admin actions.

# Task 3

**Threat Model Table (Selected High-Impact Threats)**

| Threat | STRIDE | Component | Impact/Rationale | Risk |
|---------|---------|------------|------------------|------|
| Account takeover | Spoofing | Authentication | Can lead to financial fraud | High |
| API key exposure (Google, Azure, Watson, ArcGIS) | Info Disclosure | Frontend | System compromise/API abuse, cost and reputation affected | High |
| Wallet manipulation | Tampering | Backend | Financial loss | High |
| Game score manipulation | Tampering | Client | Financial loss | High |
| Fake MetaMask/PayPal callback | Spoofing | Payment System | Financial fraud | High |
| SQL injection | Tampering | Backend | Data breach of database | High |
| DDoS attack | DoS | Web tier | Service outage and increased downtime | Medium |
| Admin privilege escalation | Elevation | Admin Panel | Full compromise of all sensitive data | High |
| Unverified free to pro user status | Elevation | Pro User Panel | Financial loss | High |
| Log deletion/modification | Repudiation | Logging | Loss of forensic evidence | Medium |
| API abuse via bots | DoS | Translation API | Cost impact and downtime | Medium |
| Location data leakage | Info Disclosure | Maps API | Privacy violation | High |
| Insider misuse | Elevation | Admin | Data compromise | High |
| Zero-day exploit in backend framework or dependency | Elevation/Tampering | Backend Services | Remote code execution or full compromise | Medium |
| Third-party API compromise | Info Disclosure/Tampering | Third-Party Integrations | Fraudulent transactions or corrupted responses | Medium |
| Large-scale infrastructure outage | DoS | Web Tier/Hosting | Complete service unavailability | Medium |

# Task 4

**Identity & Access Management**

Role-Based Access Control (RBAC)  
Separation of Admin and User planes  
Multi-Factor Authentication for Free/Pro/Admin Users with password and OTP  
Password hashing at the backend (store hashes not plaintext)  
OAuth-based authentication  
Short-lived access tokens  
Secure session cookies with timeout (JWT expiration)

**Network Segmentation and Controls**

Public Web Tier (DMZ)  
Private Application Tier  
Isolated Database Tier  
No direct database exposure  
Firewall between tiers  
Reverse proxy  
WAF (Web Application Firewall)  
API Gateway  
Private subnet backend not directly accessible  
Database not internet-accessible  
Restricted outbound API calls

**Data Protection**

TLS for all communications  
Encryption at rest  
Secure HTTP headers  
Input validation and sanitization  
Payment webhook signature verification  
Defense-in-depth to ensure layered protection

**Secrets Management**

API keys stored in environment variables  
Secrets vault for production  
Key rotation policy  
No client-side secret exposure  
IAM role-based access

**Monitoring & Logging**

Centralized logging server  
Payment audit trail  
Admin action logging  
API rate monitoring  
Intrusion detection alerts

**Payment Security Controls**

Server-side PayPal verification  
Webhook signature validation  
Idempotency keys  
Fraud detection thresholds

**Game Integrity Controls**

Server-side score validation  
No trust in client score  
Replay detection  
Reward cap enforcement

# Task 5

| Threat | STRIDE Category | Risk Level | Primary Treatment | Treatment Type | Residual Risk | Reason for Residual Risk |
|---------|----------------|------------|-------------------|----------------|--------------|--------------------------|
| Account takeover | Spoofing | High | MFA, RBAC, account lockout | Mitigate | Medium | Phishing and credential stuffing may still succeed |
| API key exposure | Information Disclosure | High | Secrets vault, no frontend storage, key rotation | Mitigate | Low | Backend compromise could expose keys |
| Wallet manipulation | Tampering | High | Server-side validation and integrity checks | Mitigate | Low | Backend vulnerability risk |
| Game score manipulation | Tampering | High | Server-side validation and replay detection | Mitigate | Low | Logic flaws may exist |
| Fake PayPal/MetaMask callback | Spoofing | High | Webhook verification and server validation | Mitigate | Low | External provider compromise risk |
| SQL Injection | Tampering | High | Parameterized queries and validation | Mitigate | Low | New injection vectors |
| DDoS attack | DoS | Medium | Rate limiting, CDN, WAF | Mitigate | Medium | Large-scale attacks may overwhelm |
| Admin privilege escalation | Elevation | High | Strict RBAC and MFA | Mitigate | Medium | Zero-day or misconfiguration |
| Unverified free-to-pro upgrade | Elevation | High | Server-side subscription verification | Mitigate | Low | Logic bugs may exist |
| Log deletion/modification | Repudiation | Medium | Log immutability | Mitigate | Low | Infrastructure compromise |
| API abuse via bots | DoS | Medium | Rate limiting and quotas | Mitigate | Medium | Distributed botnets |
| Location data leakage | Information Disclosure | High | TLS and access controls | Mitigate | Medium | Third-party breach |
| Insider misuse | Elevation | High | Least privilege and monitoring | Mitigate | Medium | Privileged user risk |
| Zero-day exploit | Multiple | Low | Monitoring and patching | Accept (Partially) | Medium | Unknown vulnerabilities |
| Third-party API compromise | Multiple | Medium | Vendor due diligence | Transfer (Partially) | Medium | Vendor-controlled security |
| Large-scale infrastructure outage | DoS | Medium | Redundancy planning | Accept (Partially) | Medium | Cloud-wide outages |

Some residual risks remain due to insider threats, third-party API vulnerabilities, zero-day software flaws, large-scale DDoS attacks, and phishing or brute-force attempts. These are mitigated but cannot be eliminated entirely.

# Task 6

**Assumptions & Limitations**

Cloud-agnostic architecture assumed  
Production deployment assumed  
Internet-facing exposure assumed  
No vendor-specific security services required  
Regulatory compliance not deeply analyzed  
Insider threat considered but not eliminated  
External APIs assumed to be independently secured
