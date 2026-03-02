# Writeup: Sound of Silence (Cyber Apocalypse 2024 - Pwn)

## 📋 Challenge Summary
- **Event:** Cyber Apocalypse 2024
- **Category:** Pwn / Buffer Overflow / System Call
- **Goal:** โจมตีโปรแกรมที่มีความซับซ้อนต่ำด้วยการใช้ช่องโหว่ `gets()` เพื่อเรียก `system("/bin/sh")`

## 🔍 Analysis & Solution

ไบนารีมีฟังก์ชัน `main` ที่สั้นมาก และมีการเรียกใช้ `system` ตั้งแต่เริ่มต้น:

1. **Vulnerability:** ฟังก์ชัน `gets(local_28)` รับข้อมูลโดยไม่จำกัดความยาว ทำให้เกิด **Buffer Overflow**
2. **Analysis:** ในขณะที่โปรแกรมทำงาน Register `RDI` จะชี้ไปยัง Buffer ที่เราควบคุมได้

### 🛠️ Steps to Solve:
1. **Find Offset:** ระยะห่างจาก Buffer ถึง Return Address คือ `40` ไบต์ (Offset: `0x28`)
2. **Payload Design:**
   - เขียนสตริง `/bin/sh\x00` ลงในจุดเริ่มต้นของ Payload
   - เติม Padding ให้ครบตาม Offset
   - เขียนทับ Return Address ให้กระโดดกลับไปที่จุดที่คำสั่ง `system` ถูกเรียกใช้ (Call System) โดยที่ `RDI` ยังคงชี้ไปที่ Buffer เดิมของเรา
3. **Execution:** โปรแกรมจะรันคำสั่ง `system("/bin/sh")` ทำให้เราได้ Shell

## 🏁 Flag
**HTB{n0_n33d_4_l34k5_wh3n_u_h4v3_5y5t3m}**
