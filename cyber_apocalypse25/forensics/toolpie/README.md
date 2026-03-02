# Writeup: ToolPie (Cyber Apocalypse 2025 - Forensics)

## 📋 Challenge Summary
- **Platform:** Hack The Box (Cyber Apocalypse 2025)
- **Category:** Forensics / Network Analysis / Malware Reverse
- **Goal:** วิเคราะห์ไฟล์ PCAP เพื่อติดตามการโจมตีและการรั่วไหลของข้อมูล

## 🔍 Analysis & Solution

จากการวิเคราะห์ไฟล์บันทึกเครือข่าย (Network Capture) พบกิจกรรมที่น่าสงสัยดังนี้:

### 1. Initial Access
- ผู้โจมตีส่งข้อมูลผ่าน HTTP POST ไปยัง Endpoint `/execute`
- พบการอัปโหลดสคริปต์ Python ที่มีการทำ Obfuscation ด้วยเครื่องมือ **Py-Fuscate**

### 2. Malware Analysis
- สคริปต์ที่ถูกอัปโหลดเป็นโทรจัน (Trojan) ที่มีความสามารถในการรันคำสั่ง Shell และรับ-ส่งไฟล์
- ตัวมัลแวร์ใช้การสื่อสารแบบเข้ารหัส **AES-CBC** โดยใช้ Key ที่สร้างแบบสุ่มและส่งไปยังเครื่อง C2 (Command and Control)

### 3. C2 Infrastructure
- C2 Server IP: `13.61.7.218` Port: `55155`
- ตรวจสอบ Packet หมายเลข 76 พบ Key ที่ส่งแบบ Cleartext คือ: `5UUfizsRsP7oOCAq`

### 4. Data Exfiltration
- ใช้ Key ที่พบในการถอดรหัสทราฟฟิกส่วนที่เหลือ
- พบผู้โจมตีดึงไฟล์ชื่อ `garricks_masterwork.pdf` ออกไป
- ไฟล์ที่รั่วไหลมีค่า MD5 Hash คือ: `8fde053c8e79cf7e03599d559f90b321`

## 🏁 Flag
**8fde053c8e79cf7e03599d559f90b321**
