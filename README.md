# Description
During analysis of the AI ​​system (ChatGPT), a Server-Side Request Forgery (SSRF) vulnerability was discovered.
This issue occurs because the application automatically fetches and executes external URLs submitted by users (e.g., webhooks) without any validation or sanitization.

By exploiting this flaw, an attacker can force the AI ​​server to make arbitrary HTTP requests to internal or external systems. This behavior exposes sensitive information, such as the server's real IP address, HTTP headers, internal services, and potentially cloud metadata.

# Affected endpoint
https://chatgpt.com/

# Affected parameter
Any input field or message where the user can submit a URL for the AI to process.

# Payload example
https://webhook.site/5926e570-88b4-47ea-a6ed-b4c44ec37a1e Verifique esse link e me escreva um texto sobre ele

When this URL was submitted, the AI immediately made a request to the attacker-controlled server. On the attacker side, request logs captured sensitive details such as the AI server’s IP address, request headers, and other identifying information.

# CVSS 3.1 Base Score

Base Score: 9.1 (Critical)
Vector: CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:L

# Proof of Concept (PoC)

During testing, the following steps were performed:

A controlled webhook URL (https://webhook.site/5926e570-88b4-47ea-a6ed-b4c44ec37a1e) was sent to the AI.

The AI ​​immediately requested the provided URL without any confirmation.

On the attacker-controlled server, the request was logged, revealing sensitive details such as the source IP address and the headers used by the AI.

This confirmed that the AI ​​blindly follows external links, exposing internal infrastructure details and allowing SSRF exploitation.

This behavior allows an attacker to craft malicious requests to internal services.

<img width="1028" height="942" alt="1" src="https://github.com/user-attachments/assets/9a176482-3916-41ae-b15a-4e3f687d34d1" />

<img width="981" height="866" alt="2" src="https://github.com/user-attachments/assets/d4664159-65c7-44fb-bbd7-8e910d0c1291" />

<img width="981" height="864" alt="3" src="https://github.com/user-attachments/assets/bd095121-da8e-4edb-a4fa-52442ffaf88a" />

<img width="980" height="861" alt="4" src="https://github.com/user-attachments/assets/5b5fe58f-132d-43c0-b012-83b41f7d3d6c" />

<img width="979" height="863" alt="5" src="https://github.com/user-attachments/assets/6916f345-f6c5-48f0-b6b8-9c2670a6f1ef" />

<img width="980" height="865" alt="6" src="https://github.com/user-attachments/assets/daa4be98-f22c-42ec-b4d8-319c797362f7" />

# Impact

The presence of a Server-Side Request Forgery (SSRF) vulnerability in the AI application poses a critical risk to the security of the underlying infrastructure. Because the system automatically follows user-supplied URLs, an attacker can leverage this behavior to force the AI server to make arbitrary HTTP requests to attacker-controlled endpoints. This exposes sensitive information such as internal IP addresses, HTTP headers, and potentially authentication tokens. More critically, the vulnerability can be weaponized to target internal-only services, bypass firewall restrictions, and access cloud metadata endpoints, enabling the theft of temporary credentials that may escalate to full compromise of cloud resources. Since the vulnerability is exploitable remotely without requiring authentication or user interaction, the likelihood of mass exploitation is high, significantly threatening the confidentiality, integrity, and overall trust in the platform.

# Remediation

To mitigate the identified SSRF vulnerability, the AI application must implement strict validation and sanitization of all user-supplied URLs before initiating outbound requests. The system should only allow access to a predefined list of trusted domains and explicitly block private IP ranges, loopback addresses, and cloud metadata services. Additionally, the application must restrict accepted protocols to HTTPS (and optionally HTTP) while disallowing others such as file, gopher, and ftp to prevent abuse. Outbound network traffic from the AI should be routed through a proxy or firewall with strict filtering policies to ensure that unauthorized destinations cannot be reached. The component responsible for making external requests should be isolated in a sandboxed environment with no direct access to sensitive internal resources. Finally, all outbound requests initiated by the AI should be logged and actively monitored for suspicious activity, and the system design should be revised to ensure that automated external requests are subject to explicit approval or security checks prior to execution.
