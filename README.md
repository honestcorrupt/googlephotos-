# 📛 Google Photos App - Insecure Local Storage of Sensitive Firebase & User Data (Android)

## 🧑‍💻 Discovered by:
**Soyam Arya (aka honest_corrupt)**  
- Ethical Hacker | CVE Researcher | Mobile & Web Pentester  
- Blog: [https://soyamaryawork.blogspot.com](https://soyamaryawork.blogspot.com)  
- GitHub: [https://github.com/honestcorrupt](https://github.com/honestcorrupt)  
- LinkedIn: [https://www.linkedin.com/in/soyam-arya-a90356312/](https://www.linkedin.com/in/soyam-arya-a90356312/)

---

## 🕳️ Vulnerability Summary
The **Google Photos Android App** stores sensitive data **in plaintext** within its local database and `.blob`/`.json`/`shared_prefs` files. This includes:

- **Firebase Auth & Refresh Tokens**
- **User Media Identifiers & Download URLs**
- **Gaia IDs, Full Names, and Profile Picture URLs of Contacts**
- **App Metadata and Interaction History**

This poses a high privacy and security risk, especially on rooted/emulated environments used in forensic analysis, pen-testing, or when malware gains local data access.

---

## 📌 CVE Class
- **CWE-922: Insecure Storage of Sensitive Information**
- **CWE-312: Cleartext Storage of Sensitive Information**
- **OWASP Mobile Top 10: M2 - Insecure Data Storage**

---

## 🎯 Target App
``
Package Name: com.google.android.apps.photos
Platform: Android
Tested Version: Latest (as of June 2025)
Environment: Genymotion Emulator (rooted), Android SDK 29+




## 📂 Affected Files & Leaks
### 1. Shared Preferences
**File:** `/data/data/com.google.android.apps.photos/shared_prefs/FirebaseHeartBeat<...>.xml`
``xml
<map>
  <string name="last-used-date">2025-06-23</string>
  <long name="fire-global" value="1750606294965" />
</map>

**File:** `gms_icing_mdd_garbage_file`, `mdd_pds_config`, `phenotype`, etc. – containing internal configs and sync info.

### 2. Firebase Auth Tokens
**File:** `.json` blobs in app files directory:
``json
{
  "Fid": "cchxPuvCQYOAXImt-9TF9s",
  "AuthToken": "eyJhbGciOi...",
  "RefreshToken": "3_AS3qfwL...",
  "ExpiresInSecs": 604800
}
`
> ⚠️ These tokens allow potential unauthorized backend API access if exploited timely.

### 3. User Data
**From SQLite DB dump:**
`text
_id  | package_name                  | localization_quality
-----------------------------------------------------------
...  | com.whatsapp                  | 1
...  | com.snapchat.android          | 1
...  | com.google.android.apps.docs  | 1
`

**From profile/contact DB table:**
``text
display_name: Soyam Arya
profile_photo_url: https://lh3.googleusercontent.com/a/ACg8ocKibs...
gaia_id: 118013611367854136651
phone_number: +91 6205695120
`

### 4. Media Identifiers
**In local blob files (Notepad decoded):**
```
,AF1QipOl_j2mF3BZWW4vzhq6FF47YXenQQ4Hvoi_F3nC
,AF1QipMGVKgHvjUdpfBqCVWW8PzHFmaNO_8URR2215ur
https://video-downloads.googleusercontent.com/ADGPM2kCZHcLRaMX...
```

---

## 📽️ Video PoC
> **Note:** This repo provides *only data samples*. To view the full step-by-step exploitation, see the **Video PoC** here:  
➡️ **[https://drive.google.com/drive/folders/1N1_uYzqBOer-VCn-TYxysCui9UBEFxTa?usp=sharing]**

---

## 🔍 How It Was Found
1. **Setup:**
   - Used Genymotion rooted emulator
   - Installed latest Google Photos app via APK
2. **ADB Exploration:**
   ```bash
   adb shell
   su
   cd /data/data/com.google.android.apps.photos/
   ```
3. **Data Extraction:**
   - `shared_prefs/*.xml`
   - `files/*.json` and `*.blob`
   - SQLite local DB (`databases/photos.db`)
4. **Decoded Blobs & Tokens:**
   - Used Notepad++/Hex viewer to open blobs
   - Pulled and decoded Firebase tokens

---

## 🔐 Risk Level: **High**
### Justification:
- Firebase `AuthToken` + `RefreshToken` = **Access to Backend APIs**
- Stored in **unencrypted**, **easily retrievable** files
- Presence of **Gaia IDs** and **profile pictures** adds **PII** leakage
- No expiration or revocation enforced in-app for local tokens

---

## 🛠️ Recommendations
- Encrypt sensitive shared preference and blob data
- Move token storage to encrypted `EncryptedSharedPreferences` or Keystore
- Avoid storing tokens longer than session scope
- Secure `.blob` and media identifiers

---

## 📬 Reporting
Google has not been contacted before this CVE request. The issue is being disclosed through [CVE.org (MITRE)](https://cve.mitre.org/) independently for public interest and awareness.

---

## 🏷️ Tags
```
#android #firebase #photos #cve #insecurestorage #plaintext #bugbounty #ethicalhacking #firebasepoc #privacy
```

---

## 📁 Disclaimer
This repository is for **educational and responsible disclosure** purposes only. The data shown is collected from a testing environment and not associated with any real users.

> 📌 CVE Request Submitted via CVE.org – Pending CVE ID

---

## 🔗 Author Contact
- 📧 soyamarya96ethical@gmail.com
- 💻 [https://github.com/honestcorrupt](https://github.com/honestcorrupt)
- 🔐 CVE-2025-5154 holder
