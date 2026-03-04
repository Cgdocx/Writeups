# Write-up: Mail Phishing-10 & 11

## Scenario
จากการติดตามพฤติกรรมเบราว์เซอร์ พบว่าผู้ใช้งานได้ทำการดาวน์โหลดไฟล์ต้องสงสัยลงมาที่เครื่อง ต้องระบุเวลาที่ไฟล์ถูกสร้างขึ้นครั้งแรกและชื่อไฟล์ล่าสุดที่ดาวน์โหลด

## Investigation Steps

1. **File Creation Time (ข้อ 10)**:
   - ตรวจสอบไฟล์ Sysmon log (`Microsoft-Windows-Sysmon/Operational.evtx`)
   - ค้นหา Event ID 11 (File Create) ที่เกี่ยวข้องกับไฟล์ `.zip` ในโฟลเดอร์ Downloads
   - พบการสร้างไฟล์ `C:\Users\itsupport\Downloads\CompanyDrive.zip`
   - เวลาที่ไฟล์ถูกสร้างครั้งแรก (CreationUtcTime) คือ **06:37:19** UTC

2. **Latest Downloaded File (ข้อ 11)**:
   - ตรวจสอบประวัติการดาวน์โหลดผ่านไฟล์ `DownloadMetadata` ของ Chrome และ Sysmon log
   - พบการดาวน์โหลดไฟล์แนบจากอีเมล Phishing
   - ชื่อไฟล์ล่าสุดที่เจ้าหน้าที่ทำการ Download มาคือ **CompanyDrive.zip**

## Answers
- **Mail Phishing-10 (เวลาสร้างไฟล์ครั้งแรก)**: **06:37:19**
- **Mail Phishing-11 (ชื่อไฟล์ล่าสุดที่โหลด)**: **CompanyDrive.zip**
