# Writeup: Pet Companion (Cyber Apocalypse 2024 - Pwn)

## 📋 Challenge Summary
- **Event:** Cyber Apocalypse 2024
- **Category:** Pwn / Buffer Overflow / Ret2Libc
- **Goal:** โจมตีโปรแกรมที่มีช่องโหว่ Buffer Overflow และไม่มี PIE เพื่อทำ Ret2Libc

## 🔍 Analysis & Solution

ตรวจสอบความปลอดภัยของโปรแกรมพบว่า:
- **No Canary:** สามารถทำ Buffer Overflow ทับ Return Address ได้
- **No PIE:** Address ของฟังก์ชันในโปรแกรมหลักคงที่
- **NX Enabled:** ไม่สามารถรัน Shellcode ใน Stack ได้

### 🛠️ Steps to Solve:
1. **Find Offset:** หาตำแหน่งที่ Return Address ถูกเขียนทับ (Offset: `0x48`)
2. **Leak Libc:** 
   - เนื่องจากโปรแกรมใช้ `write` เราสามารถใช้ ROP Chain เพื่อสั่ง `write(1, GOT_ENTRY, 8)` เพื่ออ่าน Address ของฟังก์ชันใน Libc
   - ส่งโปรแกรมกลับไปรันที่ `main` อีกครั้งเพื่อรอรับ Payload รอบที่สอง
3. **Ret2Libc:**
   - คำนวณ Base Address ของ Libc จาก Address ที่ Leak มาได้
   - หาตำแหน่งของ `system` และสตริง `/bin/sh` ใน Libc
   - ส่ง Payload รอบที่สองเพื่อเรียก `system("/bin/sh")`

## 🏁 Flag
**HTB{p37_sh0p_c0mp4n10n_3xpl0i7}**
