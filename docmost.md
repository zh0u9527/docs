# CVE-ID
CVE-2025-55574

# Bug author
markbug

# Product
docmost

GitHub Link: https://github.com/docmost/docmost

# Description
In Docmost versions <= 0.21.0, an XSS vulnerability exists in published articles. This vulnerability can be exploited to steal the credentials of any user who views content containing the XSS payload. If a system administrator is targeted, the attacker can further achieve stealth privilege escalation.

Vulnerability Type: XSS

Vulnerability Severity: high

Affected Version <= 0.21.0

# Detailed Procedure
A member creates a new article as follows and inserts an XSS payload into it, as shown below:
