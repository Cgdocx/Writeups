# Writeup: Tales for the Brave (Cyber Apocalypse 2025 - Forensics)

## 📋 Challenge Summary
- **Platform:** Hack The Box (Cyber Apocalypse 2025)
- **Category:** Forensics / JS Analysis / Telegram Bot API
- **Goal:** ตรวจสอบเว็บไซต์ที่อาจเกี่ยวข้องกับขบวนการ phishing และค้นหา Flag ที่ซ่อนอยู่

## 🔍 Analysis & Solution

จากการตรวจสอบเว็บไซต์ พบไฟล์ JavaScript (`index.js`) ที่มีการทำ Obfuscation ไว้:

### 1. JavaScript Analysis (Stage 1)
- ถอดรหัส (Deobfuscate) พบการใช้งาน **AES Decryption**
- Key และ IV ถูกกำหนดไว้ในสคริปต์ เพื่อนำไปถอดรหัส Payload ขั้นที่สอง (Stage 2)

### 2. Payload Analysis (Stage 2)
- สคริปต์ในขั้นที่สองทำหน้าที่ดักจับข้อมูลจาก Checkbox และแบบฟอร์ม
- พบการใช้ **RC4** เพื่อถอดรหัส Token ของ Telegram Bot
- ใช้ Key: `0p3r4t10n_4PT_Un10n` ในการถอดรหัส Token ได้เป็น `7767830636:AAF5Fej3DZ44ZZQbMrkn8gf7dQdYb3eNxbc`

### 3. Telegram API Interaction
- ใช้ Token ที่ได้เข้าถึง Telegram Bot API (`getUpdates`, `forwardMessage`)
- ตรวจสอบประวัติการแชท พบการส่งไฟล์ `Brave.zip` และรหัสผ่านคือ `dr4g0nsh34rtb3l0ngst0m4l4k4r`
- ดาวน์โหลดไฟล์มาตรวจสอบ พบว่าเป็น Malware สำหรับขโมยข้อมูลจาก Brave Browser

### 4. Flag Extraction
- วิเคราะห์ไฟล์ Malware พบการส่งข้อมูล HTTP POST ไปยังโดเมน `zolsc2s65u.htb`
- ตรวจสอบ JWT Token ในส่วนของ `auth` field พบค่า Base64 ที่เข้ารหัสซ้ำ 2 รอบ
- ถอดรหัสจะได้ Flag

## 🏁 Flag
**HTB{APT_c0nsp1r4c13s_b3h1nd_b3n1gn_l00k1ng_s1t3s}**
