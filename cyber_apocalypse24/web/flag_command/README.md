# Writeup: Flag Command (Cyber Apocalypse 2024 - Web)

## 📋 Challenge Summary
- **Event:** Cyber Apocalypse 2024
- **Category:** Web / Information Disclosure
- **Goal:** ค้นหาคำสั่งลับในเกม Text Adventure เพื่อรับ Flag

## 🔍 Analysis & Solution

เว็บไซต์ให้บริการเกมทายใจแบบ Text Adventure:

1. **API Analysis:** ตรวจสอบไฟล์ JavaScript พบว่าเกมดึงรายการคำสั่งที่ใช้ได้มาจาก Endpoint `/api/options`
2. **Hidden Information:** เมื่อเรียกดูข้อมูลจาก `/api/options` พบฟิลด์ `secret` ที่เก็บคำสั่งพิเศษที่ไม่มีระบุในหน้าจอเกม:
   - Secret Command: `Blip-blop, in a pickle with a hiccup! Shmiggity-shmack`

### 🛠️ Steps to Solve:
1. เปิด Developer Tools ใน Browser
2. ตรวจสอบ Network Tab หรือเข้าถึง `/api/options` โดยตรง
3. คัดลอกคำสั่งลับจากฟิลด์ `secret`
4. นำไปพิมพ์ในช่อง Command ของเกม

## 🏁 Flag
**HTB{D3v3l0p3r_t00l5_4r3_b35t_wh4t_y0u_Th1nk??!}**
