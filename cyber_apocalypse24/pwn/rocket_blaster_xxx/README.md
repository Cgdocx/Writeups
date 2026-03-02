# Writeup: Rocket Blaster XXX (Cyber Apocalypse 2024 - Pwn)

## 📋 Challenge Summary
- **Event:** Cyber Apocalypse 2024
- **Category:** Pwn / Buffer Overflow / ROP
- **Goal:** โจมตี Buffer Overflow เพื่อเรียกใช้ฟังก์ชัน `fill_ammo` พร้อมพารามิเตอร์ที่ถูกต้อง

## 🔍 Analysis & Solution

ตรวจสอบไบนารีพบช่องโหว่ **Buffer Overflow** ที่ฟังก์ชัน `main` โดยไม่มีการป้องกันจาก Stack Canary:

1. **Win Function:** มีฟังก์ชัน `fill_ammo(param1, param2, param3)` ที่ทำหน้าที่อ่าน Flag
2. **Conditions:** ต้องส่งพารามิเตอร์ให้ถูกต้องดังนี้:
   - `param1 = 0xdeadbeef`
   - `param2 = 0xdeadbabe`
   - `param3 = 0xdead1337`

### 🛠️ Steps to Solve:
1. **Find Offset:** หาตำแหน่ง Return Address (Offset: `0x28`)
2. **Find Gadgets:** หาคำสั่ง `pop rdi; ret`, `pop rsi; ret` และ `pop rdx; ret` เพื่อจัดเรียงพารามิเตอร์ใน Registers
3. **Build ROP Chain:**
   - [Offset Padding]
   - `ret` (สำหรับ Memory Alignment เพื่อป้องกันโปรแกรม Crash)
   - `pop rdi; 0xdeadbeef`
   - `pop rsi; 0xdeadbabe`
   - `pop rdx; 0xdead1337`
   - `fill_ammo` address

## 🏁 Flag
**HTB{b00m_b00m_r0ck3t_2_th3_m00n}**
