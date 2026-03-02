# Writeup: LockTalk (Cyber Apocalypse 2024 - Web)

## 📋 Challenge Summary
- **Event:** Cyber Apocalypse 2024
- **Category:** Web / JWT Bypass / CVE-2022-39227
- **Goal:** ปลอมแปลง JWT Token เพื่อให้ได้รับสิทธิ์ administrator และเข้าถึง Flag

## 🔍 Analysis & Solution

เว็บไซต์มีระบบ Authorization โดยใช้ **JWT (JSON Web Token)**:

1. **HAProxy Bypass:** ระบบป้องกัน Endpoint `/api/v1/get_ticket` ไว้ด้วย HAProxy แต่สามารถข้ามได้โดยการใช้ Double Slash (เช่น `//api/v1/get_ticket`)
2. **Library Vulnerability:** จาก `requirements.txt` พบการใช้งาน `python_jwt==3.3.3` ซึ่งมีช่องโหว่รุนแรง (**CVE-2022-39227**)
3. **Exploit:** ช่องโหว่นี้อนุญาตให้ผู้โจมตีแก้ไขข้อมูลใน Payload ของ Token ได้โดยไม่ต้องรู้ Secret Key

### 🛠️ Steps to Solve:
1. **Get Valid Token:** เข้าถึง `//api/v1/get_ticket` เพื่อรับ Token สิทธิ์ guest
2. **Forge Token:** ใช้สคริปต์ Exploit สำหรับ CVE-2022-39227 เพื่อแก้ไขฟิลด์ `role` จาก `guest` เป็น `administrator`
3. **Access Flag:** ส่ง Token ที่ปลอมแปลงแล้วไปที่ Endpoint `/api/v1/flag`

## 🏁 Flag
**HTB{h4Pr0Xy_n3v3r_D1s@pp01n4s}**
