# Writeup: Tutorial (Cyber Apocalypse 2024 - Pwn)

## 📋 Challenge Summary
- **Event:** Cyber Apocalypse 2024
- **Category:** Pwn / Integer Overflow
- **Goal:** ตอบคำถามเกี่ยวกับพื้นฐานการจัดการ Integer ในภาษา C และความเข้าใจเรื่อง Integer Overflow

## 🔍 Analysis & Solution

โจทย์ข้อนี้เป็นแบบทดสอบ Q&A เกี่ยวกับขีดจำกัดของตัวแปรประเภท Integer ในภาษา C:

1. **Integer Limits:**
   - `INT_MAX` (32-bit): `2147483647`
   - `INT_MIN` (32-bit): `-2147483648`
2. **Integer Overflow:** เมื่อเพิ่มค่าเกินขีดจำกัดสูงสุด (เช่น `INT_MAX + 1`) ค่าจะวนกลับมาที่ค่าต่ำสุด (`INT_MIN`) ตามระบบ **Two's Complement**

### 🛠️ Answers:
- **Q1:** ผลบวกของเลขบวก 2 ตัวเป็นลบได้ไหม? -> **y**
- **Q2:** ค่าสูงสุดของ 32-bit Integer? -> **2147483647**
- **Q3:** `INT_MAX + 1` ได้เท่าไหร่? -> **-2147483648**
- **Q4:** `INT_MAX + INT_MAX` ได้เท่าไหร่? -> **-2**
- **Q5:** ชื่อของบั๊กนี้คืออะไร? -> **integer overflow**
- **Q6:** ค่าต่ำสุดของ 32-bit Integer? -> **-2147483648**
- **Q7:** ต้องบวกอะไรเพิ่มเข้าไปใน `INT_MAX` เพื่อให้ได้ `-2147482312`? -> **1337**

## 🏁 Flag
**HTB{gg_3z_th4nk5_f0r_th3_tut0r14l}**
