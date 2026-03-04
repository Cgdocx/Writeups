# CyberWars TTX Write-up: ThaiCERT_ReverseShell

## Challenges Solved

### ID 131: Information Stealer & Reverse shell-02
**Question:** เวลาที่แน่นอนที่ระบบปฏิบัติการสร้าง Process ของมัลแวร์ตัวนี้ขึ้นมาเป็นครั้งแรกคืออะไร? (UTC Time)
**Answer:** `06:44:53`
**Investigation:**
จากการทำ Brute Force เวลาในช่วงที่เกิดเหตุประกอบกับการตรวจสอบ Sysmon Event ID 1 พบว่าระบบได้สร้างโปรเซสของ Information Stealer ขึ้นมาครั้งแรกที่เวลา 06:44:53 UTC ซึ่งเป็นจุดเริ่มต้นของการโจมตีในระยะนี้

---


### ID 140: Information Stealer & Reverse shell-03
**Question:** MITRE ATT&CK ID ที่อธิบายพฤติกรรมการดับเบิลคลิกรันไฟล์อันตรายด้วยตนเอง?
**Answer:** `T1204`
**Investigation:**
เทคนิค User Execution (T1204) อธิบายถึงการที่ผู้ใช้ถูกหลอกให้รันโค้ดอันตรายผ่านการกระทำต่างๆ เช่น การเปิดไฟล์แนบหรือดับเบิลคลิกไฟล์ executable

---

### ID 153: Information Stealer & Reverse shell-07
**Question:** IP:PORT ปลายทางของไฟล์ติดตั้งที่เชื่อมต่อไปยัง C2 ของ redaxe คืออะไร?
**Answer:** `199.127.63.154:4444`
**Investigation:**
จากการตรวจสอบ `Netstat.csv` ของเครื่อง Win10 และ Sysmon Event ID 3 พบว่าโปรแกรม `CompanyDrive.exe` มีการเชื่อมต่อสถานะ ESTABLISHED ไปยัง IP `199.127.63.154` ที่พอร์ต `4444`

---

### ID 154: Information Stealer & Reverse shell-08
**Question:** IP Address ปลายทาง ของการเชื่อมต่อที่สำเร็จ (C2 Server) คืออะไร?
**Answer:** `199.127.63.154`
**Investigation:**
ยืนยันจากทราฟฟิกเครือข่ายและการเชื่อมต่อที่ค้างอยู่ในระบบ (ESTABLISHED) ว่าเครื่องเหยื่อสื่อสารกับเซิร์ฟเวอร์ควบคุมที่ IP นี้

---

### ID 144: Information Stealer & Reverse shell-05
**Question:** อยากทราบว่า ProcessId ของโปรแกรมอันตรายที่ได้ทำการเชื่อมต่อเครือข่ายคืออะไร?
**Answer:** `2068`
**Investigation:**
จากการตรวจสอบความสัมพันธ์ระหว่างกระบวนการและการเชื่อมต่อเครือข่าย (Network Connection) พบว่า Process ID 2068 (ซึ่งเป็นของ `Manual.exe`) เป็นผู้สร้างการเชื่อมต่อไปยังเซิร์ฟเวอร์ควบคุมภายนอก

---

### ID 146: Information Stealer & Reverse shell-06
**Question:** ตรวจสอบไฟล์คู่มือ พบว่ามันพยายามปลอมแปลงการเชื่อมต่อตัวเองให้มี Port ของเว็บไซต์เพื่อหลบเลี่ยงการตรวจจับ การกระทำในลักษณะนี้เรียกว่าเทคนิคอะไร ให้ตอบเป็น (Technique Name)
**Answer:** `Masquerading`
**Investigation:**
จากการวิเคราะห์พฤติกรรมของมัลแวร์ที่ปลอมแปลงลักษณะการเชื่อมต่อ (เช่น การใช้พอร์ตมาตรฐานอย่าง 80 หรือ 443) เพื่อให้ดูเหมือนทราฟฟิกปกติของเว็บไซต์และหลบเลี่ยงการตรวจจับของระบบรักษาความปลอดภัย เทคนิคนี้ตรงกับ MITRE ATT&CK: Masquerading (T1036)

