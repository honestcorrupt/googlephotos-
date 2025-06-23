# 📛 Google Photos App - Insecure Local Storage of Sensitive Firebase & User Data (Android)

## 🧑‍💻 Discovered by:
**Soyam Arya (aka honest_corrupt)**  
Ethical Hacker | CVE Researcher | Mobile & Web Pentester  
- Blog: https://soyamaryawork.blogspot.com  
- GitHub: https://github.com/honestcorrupt  
- LinkedIn: https://www.linkedin.com/in/soyam-arya-a90356312/

---

## 🕳️ Vulnerability Summary

The Google Photos Android App insecurely stores sensitive information in plaintext within local `.blob`, `.json`, and `shared_prefs` files as well as SQLite databases. This includes:

- Firebase Installation ID (FID), AuthToken, and RefreshToken
- Media Download URLs from Google's video storage
- User’s PII (names, gaia IDs, profile photo URLs, phone numbers)
- Internal Firebase heartbeat, analytics, and sync identifiers
- Contact database and app usage metadata

All of these are stored without encryption or access controls in rooted/emulated environments, posing a serious security and privacy concern.

---

## 📌 CVE Class
- **CWE-922:** Insecure Storage of Sensitive Information
- **CWE-312:** Cleartext Storage of Sensitive Information
- **OWASP Mobile Top 10:** M2 - Insecure Data Storage

---

## 🎯 Target App
- **Package Name:** com.google.android.apps.photos
- **Platform:** Android
- **Tested Version:** Latest (June 2025)
- **Environment:** Rooted Genymotion Emulator (Android 10, SDK 29+)

---

## 📂 Affected Files & Data Leaks

### 🔹 1. Shared Preferences
**File:** `/data/data/com.google.android.apps.photos/shared_prefs/FirebaseHeartBeat<...>.xml`
- Dates and component versions linked to Firebase usage

**File:** `phenotype`, `phenotype_storage_info`, `gms_icing_mdd_garbage_file`, `mdd_pds_config`
- Contain internal tracking/sync config and experiment flags

### 🔹 2. Firebase Auth Tokens (Highly Sensitive)
**Location:** `.json` files in `/data/data/com.google.android.apps.photos/files/`
```json
{
  "Fid": "cchxPuvCQYOAXImt-9TF9s",
  "AuthToken": "eyJhbGciOi...",
  "RefreshToken": "3_AS3qfwLFpXH...",
  "ExpiresInSecs": 604800
}
```
> ⚠️ These tokens, if extracted and used before expiration, could potentially access backend APIs or services.

### 🔹 3. Personally Identifiable Information (PII)
**Source:** `photos.db` & blob file dumps

**Data Extracted:**
- display_name`: *Soyam Arya*, *amandeep verma*, etc.
- gaia_id`: *118013611367854136651*, etc.
- profile_photo_url`: `https://lh3.googleusercontent.com/a/...`
- phone_number`: `+91 6205695120`

### 🔹 4. Contact & App Metadata Table
From SQLite:
``
_id | package_name                  | localization_quality
-----------------------------------------------------------
... | com.whatsapp                 | 1
... | com.snapchat.android         | 1
... | com.google.android.apps.docs | 1
... | com.google.android.apps.dynamite | 1
... | com.facebook.katana          | 1
... | com.twitter.android (X)      | 1
... | com.signal.android           | 1
``

### 🔹 5. Media Identifiers & Download Links
From `.blob` and `.json` files:
- Example Media IDs:
  - AF1QipOl_j2mF3BZWW4vzhq6FF47YXenQQ4Hvoi_F3nC`
  - AF1QipMA7Bn_aLQfjLPnWiS_hIkXS-QG`

- Example Video Download Link:
```
https://video-downloads.googleusercontent.com/ADGPM2kCZHcLRaMXsQ61P0uDji2CQmUqv4TH6JAfakbji7JsPVogoP5llNOxLs9nK_qW8ZxGfDaKgiWoVYXNuoWOA98JKoFPqf43wQ1YvTwhoThhIyn71AJzvZtQwaokQPo0pmZgM2FZ4Jrj8gNKPRL-slsFcIdxGqy2VPE9b-XzUuGgTjqEzYs
```

---

## 📽️ Video PoC
> **Note:** This repo provides only data samples. For full exploitation steps, watch the **video PoC**:
➡️ [Google Drive Video](https://drive.google.com/drive/folders/1N1_uYzqBOer-VCn-TYxysCui9UBEFxTa?usp=sharing)

---

## 🔍 How I Found It
**Setup:**
- Genymotion (rooted)
- Installed Google Photos app (latest APK)

**Exploration Steps:**
1. ADB access: `adb shell && su`
2. Accessed files:
   - `/data/data/com.google.android.apps.photos/shared_prefs/*.xml`
   - `/files/*.blob` and `.json`
   - `/databases/photos.db`
3. Decoded binary blob using Notepad++ & hex viewers
4. Verified presence of real PII & Firebase tokens

---

## 🔐 Risk Level: HIGH
- Firebase tokens allow unauthorized backend access
- Tokens & PII stored in unencrypted formats
- Exposure of Gaia IDs and user media links is a major privacy leak
- Suitable for session hijacking, impersonation, or data scraping

---

## 🛠️ Recommendations
- Encrypt all tokens using Android Keystore or EncryptedSharedPreferences
- Avoid long-term token storage
- Obfuscate or restrict access to `.blob` and `.json` directories
- Sanitize local storage of contact/metadata entries

---

## 📬 Reporting
This issue has **not been reported to Google**. CVE is requested directly via [CVE.org (MITRE)] for public security awareness.

---

## 🏷️ Tags
#android #firebase #photos #cve #insecurestorage #plaintext #bugbounty #ethicalhacking #firebasepoc #privacy

---

## 📁 Disclaimer
This repository is for educational and ethical research only. Data was extracted from a clean emulator. No real users were impacted.

---

## 🧾 CVE Status
📌 **CVE Request submitted via CVE.org – Pending ID**

---

## 🔗 Contact
**Soyam Arya (aka honest_corrupt)**  
📧 soyamarya96ethical@gmail.com  
🔐 CVE-2025-5154 holder  
💻 https://github.com/honestcorrupt
