# Write-up: Mail Phishing-12 & 14

## Scenario
ระบุโปรแกรมที่ใช้ในการแตกไฟล์บีบอัด (Archive) และรายชื่อไฟล์ทั้งหมดที่ได้จากการแตกไฟล์ดังกล่าว ซึ่งนำไปสู่การติดตั้งไฟล์อันตรายบนระบบ

## Investigation Steps

1. **Extraction Program (ข้อ 12)**:
   - ตรวจสอบไฟล์ Sysmon log และ Prefetch
   - พบการรันโปรแกรมเพื่อแตกไฟล์ `CompanyDrive.zip` ณ เวลา **06:38:20 UTC**
   - โปรแกรมที่ผู้ใช้งานใช้คือ **winrar.exe**

2. **Extracted Files (ข้อ 14)**:
   - ตรวจสอบรายชื่อไฟล์ที่ถูกสร้างขึ้นในช่วงเวลาที่มีการรันโปรแกรมแตกไฟล์
   - ตรวจสอบเนื้อหาภายในไฟล์ `CompanyDrive.zip` จากข้อมูล Base64 ในทราฟฟิก SMTP
   - ไฟล์ทั้งหมดที่แตกออกมาคือ **CompanyDrive.exe, Manual.exe**

## Answers
- **Mail Phishing-12 (โปรแกรมแตกไฟล์)**: **winrar.exe**
- **Mail Phishing-14 (ไฟล์ที่แตกออกมา)**: **CompanyDrive.exe, Manual.exe**
