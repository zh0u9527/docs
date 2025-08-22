# 1. CVE-ID
CVE-2025-55573
# 2. Product
new-api, https://github.com/Calcium-Ion/new-api

Affected Versions <=0.8.5.2
# 3. Environment Setup
```
# download project
git clone https://github.com/Calcium-Ion/new-api.git
cd new-api
# start
docker-compose up -d
```
<img width="2109" height="166" alt="image" src="https://github.com/user-attachments/assets/9cf3af08-c454-407c-af26-061225f3999b" />



# 4. Detailed Procedure
Navigate to the feature: Console → Playground. Here, you can interact with the AI (default settings are sufficient, no additional configuration is required). The page is shown as follows:
<img width="2539" height="1461" alt="image" src="https://github.com/user-attachments/assets/e870a37e-ea3b-43b4-b533-af77c9cf39ce" />
Here, we send a message containing XSS code to the AI. The server returns an error response, which does not require any handling. As shown below:
<img width="2542" height="837" alt="image" src="https://github.com/user-attachments/assets/9f22b412-f547-426b-9477-4b22494febbe" />
When we enable “Show Debug” on the page, the XSS code is triggered, as shown below:
<img width="2479" height="916" alt="image" src="https://github.com/user-attachments/assets/cc9e2019-76c5-40e5-8455-6cd285cf6db7" />
Up to this point, it is still only a Self-XSS type. To further escalate the impact, we can export the conversation by clicking the export button as shown below:
<img width="2536" height="1461" alt="image" src="https://github.com/user-attachments/assets/659e234d-8a11-40d6-b82e-2176c5f7b932" />
Here, we log in using another admin account, then navigate to Console → Playground. After that, we import the previously exported file.
<img width="2549" height="1517" alt="image" src="https://github.com/user-attachments/assets/e6097b84-89c4-45b5-a364-d4ace8a1e794" />
When the admin user is successfully compromised by the XSS attack.
<img width="2543" height="1371" alt="image" src="https://github.com/user-attachments/assets/6215dc0c-1d2e-49f6-a6e7-fbbdfbc0a09b" />
If any user in the system (including administrators) imports a conversation file containing XSS code, they will be affected by this vulnerability.
