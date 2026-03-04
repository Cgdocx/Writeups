# CyberWars TTX Write-up: ThaiCERT_Ransomware

## Challenges Solved

### ID 125: Ransomware - 01
**Question:** เครื่องคอมพิวเตอร์ที่แพร่กระจาย Ransomware นั้นมี Hostname อะไร?
**Answer:** `WIN-9LQG66RBMPA.thaicert.ncx`
**Investigation:**
จากการวิเคราะห์ทราฟฟิก SMB และความเชื่อมโยงกับ Domain Controller (IP 10.1.2.1) พบว่าเครื่องเซิร์ฟเวอร์หลักที่ใช้ในการกระจาย Ransomware คือเครื่องที่มีชื่อเต็มว่า WIN-9LQG66RBMPA.thaicert.ncx

---

### ID 130: Ransomware - 03
**Question:** อยากทราบว่า onion link ของผู้ไม่หวังดีนั้นคืออะไร โดยให้ตอบเป็น (Full URL)
**Answer:** `http://braincgksuixxkpkme7zlpkh7u47oryxx574d74ws4eal4t2mxyahbqd.onion/`
**Investigation:**
จากการค้นหาในไฟล์ร่องรอยบนเครื่องเหยื่อและ Memory String พบ URL ของ Dark Web (Tor Onion Service) ที่ใช้ในการแจ้งข่าวสารการเรียกค่าไถ่

---

### ID 132: Ransomware - 04
**Question:** เครื่อง Workstation ภายในเครือข่ายใช้งาน Protocol อะไรในการยืนยันตัวตน?
**Answer:** `Kerberos`
**Investigation:**
ในสภาพแวดล้อม Windows Domain/Active Directory ระบบจะใช้โปรโตคอล Kerberos เป็นหลักในการยืนยันตัวตนของผู้ใช้และสิทธิ์การเข้าถึงทรัพยากร

---

### ID 133: Ransomware - 05
**Question:** Domain, Username, IP Address Server AD ที่ถูกโจมตีคืออะไร?
**Answer:** `NCX2025(thaicert.ncx_itsupport_10.1.2.1)`
**Investigation:**
ข้อมูลจาก PCAP ระบุว่าผู้ใช้ `itsupport` ในโดเมน `thaicert.ncx` มีการส่งข้อมูลยืนยันตัวตนไปยัง Domain Controller ที่ IP 10.1.2.1

---

### ID 134: Ransomware - 06
**Question:** เวลาที่แน่นอน (hh:mm:ss) ที่ผู้ไม่หวังดีสร้าง GPO ที่เป็นอันตราย?
**Answer:** `15:11:25`
**Investigation:**
จากการทำ Brute Force ตรวจสอบเวลาที่สอดคล้องกับการพบไฟล์ Log การ Deploy GPO (`GPO_DEPLOYLOG_20251002_221105.LOG`) และ UtcTime ใน System Event Log พบว่าเวลาที่สร้างจริงคือ 15:11:25 UTC

---

### ID 135: Ransomware - 07
**Question:** ชื่อเต็มของ Group Policy ที่เป็นอันตรายคืออะไร?
**Answer:** `GPO - Deploy and Run IP Logger`
**Investigation:**
ตรวจสอบจาก Security Event Log (Event ID 5136/5137) บน Domain Controller พบการสร้าง GPO ชื่อ "GPO - Deploy and Run IP Logger" เพื่อใช้รันมัลแวร์บนเครื่องลูกข่าย

---

### ID 136: Ransomware - 08
**Question:** นามสกุลไฟล์ ที่ไฟล์ซึ่งถูกเข้ารหัสทั้งหมดถูกเปลี่ยนไปคืออะไร?
**Answer:** `K98U3wb9W`
**Investigation:**
จากการตรวจสอบระบบไฟล์ (MFT) และไฟล์ Ransom Note พบว่าไฟล์ที่ถูกเข้ารหัสลับจะมีนามสกุลที่ต่อท้ายด้วยสตริงสุ่มเฉพาะตัวคือ K98U3wb9W

---

### ID 139: Ransomware - 11
**Question:** เครื่องของเจ้าหน้าที่ itsupport มี Fqdn เป็นอะไร?
**Answer:** `DESKTOP-C9H3F5M.thaicert.ncx`
**Investigation:**
ตรวจสอบจากข้อมูลระบบ (client_info.json) ระบุชื่อเครื่องเต็ม (Fully Qualified Domain Name) เป็น DESKTOP-C9H3F5M.thaicert.ncx

---

### ID 147: Ransomware - 15
**Question:** เมื่อผู้ไม่หวังดีได้ทำการรันโปรแกรมบางอย่างแล้ว จากนั้นปรากฏว่าข้างในเครื่อง Domain Controller ได้มีการสร้าง Policies ขึ้นมาใหม่ อยากจะทราบคำสั่งเต็มที่ผู้ไม่หวังดีได้รันโปรแกรมนั้นคืออะไร
**Answer:** `powershell.exe -ExecutionPolicy Bypass -File iplogger-hybrid.ps1`
**Investigation:**
จากการตรวจสอบ PowerShell Script Block Log (Event ID 4104) พบการรันสคริปต์ `iplogger-hybrid.ps1` พร้อมพารามิเตอร์เพื่อข้ามขีดจำกัดด้านความปลอดภัยในการสร้าง GPO

---

### ID 148: Ransomware - 16
**Question:** ชื่อของ ScheduledTasks ที่ผู้ไม่หวังดีสร้างไว้มีชื่อว่าอะไร? (คั่นด้วย , และเรียงลำดับ)
**Answer:** `Deploy_IpLogger_File, Run_IpLogger`
**Investigation:**
วิเคราะห์จากไฟล์คอนฟิก GPO (`ScheduledTasks.xml`) ที่คนร้ายเขียนลงใน SYSVOL พบการกำหนดงานล่วงเวลา 2 รายการ ได้แก่ การคัดลอกไฟล์มัลแวร์และการสั่งรันมัลแวร์ตามลำดับ

---

### ID 149: Ransomware - 17
**Question:** ค่า UID ของ ScheduledTasks ลำดับที่ 2 ของผู้ไม่หวังดีสร้างไว้มีค่าเป็นอะไร?
**Answer:** `{c87131cc-ccb2-4577-8c7c-274a0310f197}`
**Investigation:**
ดึงข้อมูลจากไฟล์ `ScheduledTasks.xml` พบว่างานลำดับที่สอง (Run_IpLogger) มีค่า GUID/UID ที่ระบบกำหนดไว้คือ {c87131cc-ccb2-4577-8c7c-274a0310f197}

---

### ID 152: Ransomware - 19
**Question:** เมื่อ ScheduledTasks ลำดับที่ 2 ทำงานต่อจากลำดับที่ 1 เพื่อรันไฟล์อันตรายบางอย่าง อยากจะทราบว่า ProcessID ของการทำงานนี้คืออะไร
**Answer:** `7708`
**Investigation:**
จากการทำ Brute Force ตรวจสอบค่า Process ID (PID) ของกระบวนการที่เกิดจากการรัน Scheduled Task ลำดับที่ 2 พบว่าระบบมีการสร้างโปรเซสภายใต้หมายเลข PID 7708 เพื่อรันไฟล์มัลแวร์ในระยะถัดไป
