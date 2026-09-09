# The Greenholt Phish (Write-up)

This markdown file is my write-up document for the TryHackMe's `The Greenholt Phish` room.

---

### Scenario

A sales executive at Greenholt PLC has reported a suspicious email received from a known customer. The employee found various inconsistencies in the mail and communication behaviour of the customer and escalated the situation to  the SOC for further investigation.

---

### Objectives

- analyse the provided email to identify and extract key artifacts
- investigate the message source to determine its origin and authenticity
- use analysis tools to assess the potential maliciousness of the email

---

### Tasks

Q1. What is the `Transfer Reference Number` listed in the email's **Subject** line?  
Answer. `09674321`  
Determination: {The subject clearly mentioned the Transference Reference Number as a part of the Subject of the email}

Q2. What is the display name of the sender?  
Answer: `Mr. James Jackson`  
Determination: {The sender name was displayed in the email header}

Q3. What is the sender's email address?  
Answer: `info@mutawamarine.com`  
Determination; {Analysing the email header we can determine the sender's address}

Q4. What email address will receive a reply to this email?  
Answer: `info.mutawamarine@mail.com`  
Determination: {Similar to the previous question this can be easily determined by analysing the email header}

**Note:** Notice how the email address for receiving a reply is different from the from email address. This indicates that the sender is trying to divert any information conveyed from the organisation is not sent to the actual recipient.

Q5. What is the originating IP address of the this email?  
Answer. `192.119.71.157`  
Determination: {Analysing the message source the originating Ip of the email was found}

Q6. Who is the owner of the originating IP?  
Answer. `HostPapa`  
Determination: {Using an IP lookup tool and providing the IP determined previously we can easily find out who is listed as the owner of the IP}

Q7. What is the full SPF record for this domain?  
Answer: `v=spf1 include:spf.protection.outlook.com -all`

Q8. What is the complete DMARC record for this domain?  
Answer: `v=DMARC1; p=quarantine; fo=1`

Q9. What is the file name of the attachment found in the email?  
Answer: `SWT_#09674321____PDF__.CAB`

Q10: What is the `SHA256` hash of the file?  
Answer: `2e91c533615a9bb8929ac4bb76707b2444597ce063d84a4b33525e25074fff3f`

Q11. What is the attachment file size in KB?   
Answer: `400.26 KB`

Q12. What is the actual file type of the attachment?  
Answer: `rar`
