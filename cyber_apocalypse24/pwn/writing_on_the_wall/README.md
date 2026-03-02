# Writeup: Writing on the Wall (Cyber Apocalypse 2024 - Pwn)

## 📋 Challenge Summary
- **Event:** Cyber Apocalypse 2024
- **Category:** Pwn / Buffer Overflow / Null Byte Injection
- **Goal:** ข้ามระบบตรวจสอบรหัสผ่านโดยการใช้ช่องโหว่ Buffer Overflow ขนาด 1 ไบต์

## 🔍 Analysis & Solution

จากการตรวจสอบไบนารี พบเงื่อนไขการตรวจสอบรหัสผ่านที่น่าสนใจ:

1. **Password Buffer:** ตัวแปร `local_18` เก็บสตริง `w3tpass ` (7 ไบต์ + เว้นวรรค)
2. **Input Buffer:** ตัวแปร `local_1e` มีขนาดเพียง 6 ไบต์ แต่อยู่ติดกับ Password Buffer
3. **Vulnerability:** คำสั่ง `read(0, local_1e, 7)` อนุญาตให้อ่านข้อมูลได้ 7 ไบต์ ซึ่งเกินขนาดของ Input Buffer ไป 1 ไบต์พอดี

### 🛠️ Steps to Solve:
1. **Null Byte Overwrite:** ไบต์ที่ 7 ที่ล้นออกมาจะเข้าไปทับตำแหน่งแรกของ Password Buffer
2. **Exploitation:** หากเราส่ง Null Bytes (`\x00`) จำนวน 7 ไบต์เข้าไป:
   - Input Buffer จะเริ่มต้นด้วย `\x00`
   - ตำแหน่งแรกของ Password Buffer จะถูกทับด้วย `\x00`
3. **Bypass:** ฟังก์ชัน `strcmp` จะมองว่าทั้งสองเป็นสตริงว่าง (Empty String) เพราะพบ Null Byte ตั้งแต่ตำแหน่งแรก ทำให้ผลการเปรียบเทียบเป็น 0 (Success) และเปิดประตูรับ Flag

## 🏁 Flag
**HTB{3v3ryth1ng_15_r34d4bl3}**
