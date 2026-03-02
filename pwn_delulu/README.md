# Writeup: Delulu (Cyber Apocalypse 2024 - Pwn)

## 📋 Challenge Summary
- **Event:** Cyber Apocalypse 2024
- **Category:** Pwn / Format String
- **Tools:** GDB, Pwntools, Python

## 🔍 Analysis & Solution

จากการตรวจสอบซอร์สโค้ดและทดสอบโปรแกรม พบว่ามีช่องโหว่ประเภท **Format String** ในส่วนที่รับ Input ของผู้ใช้:

1. **Vulnerability:** โปรแกรมใช้ฟังก์ชัน `printf(input)` โดยตรงโดยไม่มีการกำหนด Format Specifier
2. **Goal:** แก้ไขค่าในตัวแปร `delulu` เพื่อให้เงื่อนไขตรวจสอบผ่านและส่ง Flag กลับมา

### 🛠️ Steps to Solve:
1. **Find Offset:** ส่ง `%p %p %p...` เข้าไปเพื่อหาว่า Input ของเราอยู่ที่ Offset ลำดับที่เท่าไหร่ใน Stack
2. **Calculate Address:** หาตำแหน่งที่อยู่ของตัวแปร `delulu` ในหน่วยความจำ
3. **Exploitation:** ใช้สคริปต์ Python ร่วมกับ `pwntools` เพื่อส่ง Payload ในรูปแบบ `%[value]c%[offset]$n` เพื่อเขียนทับค่าในตัวแปร `delulu`

## 🏁 Flag
**HTB{d3lul_u_0r_n07_d3lulu_15_7h3_qu35710n}**
