# Writeup: KORP Terminal (Cyber Apocalypse 2024 - Web)

## 📋 Challenge Summary
- **Event:** Cyber Apocalypse 2024
- **Category:** Web / SQL Injection
- **Goal:** เจาะระบบ Login ของ Terminal ผ่านช่องโหว่ SQL Injection

## 🔍 Analysis & Solution

เว็บไซต์มีหน้า Login ที่รับ Username และ Password:

1. **Vulnerability:** ทดสอบใส่เครื่องหมาย `'` ในช่อง Username พบ Error Message ที่บ่งบอกว่าระบบใช้ **MariaDB** และมีช่องโหว่ **SQL Injection**
2. **Analysis:** ระบบมีการตรวจสอบรหัสผ่านแบบ Hashing (เช่น Bcrypt) ทำให้ไม่สามารถใช้ payload ทั่วไปอย่าง `' OR 1=1--` ได้ตรงๆ

### 🛠️ Steps to Solve:
1. **Identify Hashing:** ทดสอบทำ `UNION SELECT` เพื่อคืนค่า Password Hash ที่เราเตรียมไว้เอง
   - Payload: `admin' AND 1=0 UNION ALL SELECT '$2a$12$fuqOG.Xuqon6cO9bX2d5OuQGzKvi3BKDoE6a5n3t3GpqyNBdiZqJe`
2. **Exploitation:**
   - ใช้ Payload ด้านบนในช่อง Username
   - ใส่รหัสผ่านที่ตรงกับ Hash (ในที่นี้คือ `test`)
3. **Result:** ระบบจะมองว่าผลลัพธ์จาก Query (ซึ่งคือ Hash ของเรา) ตรงกับรหัสผ่านที่กรอกเข้าไป และให้ผ่านเข้าระบบ

## 🏁 Flag
**HTB{t3rm1n4l_cr4ck1ng_sh3n4nig4n5}**
