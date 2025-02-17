# Advanced Security Incident Analysis

## Overview  
Email is one of the primary attack vectors used by hackers to extract sensitive information. This analysis explores how phishing emails manipulate email headers, embedded content, and attachments to trick users into revealing personal or corporate information.  

---

## Email Header Analysis  

### 1️⃣ Delivered To  
Indicates the recipient of the email.  

### 2️⃣ Received  
- Just like a postal system, emails go through multiple mail servers before reaching the recipient.  
- The topmost "Received" header shows the closest mail server to the recipient.  
- To analyze sender authenticity, we scroll to the **"Received: by"** field closest to the sender.  

---

## Authentication Checks (SPF, DKIM, DMARC)  

### SPF Failure Detection  
spf=fail (google.com: domain of billjobs@microapple.com does not designate 93.99.104.210 as permitted sender)

yaml
Copy
This means the domain @microapple.com does not authorize 93.99.104.210, making this highly suspicious.  

If SPF fails, it is recommended to:  
- Inspect the authentication-results header for further verification.  
- Cross-check if the Reply-To field is different from the From field, which is another red flag.  

---


## Analyzing Suspicious Fields  

### 1️⃣ Reply-To Mismatch  
- **From:** billjobs@microapple.com  
- **Reply-To:** negeja3921@pashter.com  
- Since the **Reply-To field differs from the From field**, this is an indication of a phishing attempt.  

### 2️⃣ Content-Type Analysis  
- The email specifies **multipart/mixed**, meaning multiple formats exist.  
- The first boundary contains **encoded text (Base64)**, which we need to decode.  

---


## Decoding Base64 Content  

### Step 1: Decode Email Body  
We extracted and decoded the first **Base64-encoded content** to reveal the phishing message.  
[![View Image](https://img.shields.io/badge/View_Image-Click_Here-blue?style=for-the-badge)](images/email_analysis_first_base64_decode.png)  

### Step 2: Decode Attachment  
The email contained an attachment **PuzzleToCoCanDa.pdf**, encoded in Base64.  
We decoded it, but the content was unreadable.  
[![View Image](https://img.shields.io/badge/View_Image-Click_Here-blue?style=for-the-badge)](images/email_analysis_attachment_decode_base64.png)  

---


## File Type Analysis: Is the Attachment Really a PDF?  
Upon further inspection, the extracted file signature did **not** match a PDF.  
We converted the content into **hex format** for further analysis.  
[![View Image](https://img.shields.io/badge/View_Image-Click_Here-blue?style=for-the-badge)](images/email_analysis_attachment_deoced_b64_hex.png)  

Using **Gary Kessler’s File Signature Database**, we determined that:  
- The file starts as a **ZIP archive** but was disguised as a **PDF**.  
[![View Image](https://img.shields.io/badge/View_Image-Click_Here-blue?style=for-the-badge)](images/email_analysis_gary_kessler_zip_pdf_file.png)  

---


## Extracting ZIP Content  
We manually saved the file as **.zip** and extracted the contents.  
Inside, we found:  

1. **DaughtersCrown.jpeg**  
2. **GoodJobMajor.pdf**  
3. **A hidden .xlsx file (Money.xlsx)**  

[![View Image](https://img.shields.io/badge/View_Image-Click_Here-blue?style=for-the-badge)](images/email_analysis_daughterscrown.png)  
[![View Image](https://img.shields.io/badge/View_Image-Click_Here-blue?style=for-the-badge)](images/email_analysis_goodjobmajor_pdf.png)  

---


## Hex Analysis of Hidden Files  
We inspected **DaughtersCrown.jpeg** in HxD and verified it was a **valid JPEG**.  
[![View Image](https://img.shields.io/badge/View_Image-Click_Here-blue?style=for-the-badge)](images/email_analysis_hxd_daughterscrown_garykessler.png)  

Similarly, **GoodJobMajor.pdf** was verified using the **PDF file signature**.  
[![View Image](https://img.shields.io/badge/View_Image-Click_Here-blue?style=for-the-badge)](images/email_analysis_hxd_goodjobmajor_garykessler.png)  

---


## Hidden Data in Money.xlsx  
Inside **Money.xlsx**, we discovered a third hidden sheet (**Sheet3**) that contained **Base64-encoded text**.  
[![View Image](https://img.shields.io/badge/View_Image-Click_Here-blue?style=for-the-badge)](images/email_analysis_money_xlsx_sheet3.png)  

After decoding, it revealed a **location**:  
**The Martian Colony, Beside Interplanetary Spaceport.**  
[![View Image](https://img.shields.io/badge/View_Image-Click_Here-blue?style=for-the-badge)](images/email_analysis_sheet3_cyberchef.png)  

---


## Final Analysis & Key Findings  
- The **SPF failed**, and **Reply-To was different from From**, indicating a forged sender.  
- The **attached "PDF" was actually a ZIP file**, containing **hidden malicious files**.  
- The **hidden Excel sheet** contained **Base64-encoded location information**.  
- **Conclusion:** This was an **advanced phishing attack** attempting to hide clues within **disguised attachments**.  

---


## 🛠 Tools Used  
- **Base64 Decoding** → CyberChef  
- **File Type Identification** → Gary Kessler’s File Signature Database  
- **Hex Analysis** → HxD Editor  
- **ZIP Extraction & File Verification** → Windows, CyberChef  
- **Header Analysis** → Google Admin Toolbox, MXToolbox  

---


## BTLO Challenge Submission  

| Question | Answer |  
|----------|--------|  
| What is the email service used by the malicious actor? | **Emkei.cz** |  
| What is the Reply-To email address? | **negeja3921@pashter.com** |  
| What is the filetype of the received attachment which helped continue the investigation? | **.zip** |  
| What is the name of the malicious actor? | **Pestero Negeja** |  
| What is the location of the attacker in this Universe? | **The Martian Colony, Beside Interplanetary Spaceport** |  
| What could be the probable C&C domain to control the attacker’s autonomous bots? | **pashter.com** |  

---


## Conclusion  
This **phishing security incident analysis** uncovered a **well-crafted attack** that:  
- **Bypassed standard email filters** using disguised attachments.  
- Used **SPF failures and mismatched headers** as key indicators of phishing.  
- **Hid clues in base64-encoded files, ZIP archives, and spreadsheets**.

By understanding how these attacks work, **SOC analysts and threat hunters** can better **detect, analyze, and mitigate phishing threats**.

---


### Final Notes  
- **Upload all images** to the `images/` folder inside your **GitHub repository**.  
- **Now, when users click on "View Image", they will be able to open the images directly in the repository.** 🚀
