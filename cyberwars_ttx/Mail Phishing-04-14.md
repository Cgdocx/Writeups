# Write-up: Mail Phishing-04 to 14

## Investigation Steps

1. **Email Analysis (PCAP)**:
   - ตรวจสอบ SMTP traffic ใน `DMZ-Zone-part1.pcap` (TCP stream 7)
   - **Sender**: `somchai@mail.thaicert.ncx`
   - **Recipient**: `itsupport@mail.thaicert.ncx`
   - **Sender IP**: `199.127.63.151`
   - **Program (X-Mailer)**: `sendEmail-1.56`
   - **Email Content**: พบเนื้อความเกี่ยวกับนโยบายติดตั้งซอฟต์แวร์
   - **Attachment**: `CompanyDrive.zip`

2. **User Activity (Chrome History)**:
   - ตรวจสอบ `results/Windows.Applications.Chrome.History.csv`
   - พบการเปิดดูอีเมล (Preview `_uid=19`) ครั้งสุดท้ายเมื่อ **2025-10-02T06:43:47Z**

3. **File Creation (Sysmon)**:
   - ตรวจสอบ `Microsoft-Windows-Sysmon/Operational.evtx`
   - พบการสร้างไฟล์ `CompanyDrive.zip` ในโฟลเดอร์ Downloads ครั้งแรกเมื่อเวลา **06:37:19** UTC
   - พบว่าไฟล์ล่าสุดที่ดาวน์โหลดคือ **CompanyDrive (1).zip** (จาก `DownloadMetadata`) หรือ **CompanyDrive.zip**

4. **Extraction Analysis**:
   - โปรแกรมที่ใช้แตกไฟล์คือ **7z.exe** (พบใน Prefetch และ Sysmon)
   - ไฟล์ที่ถูกแตกออกมาพบใน Sysmon log ได้แก่ **CompanyDrive.exe, Manual.exe, mimidrv.sys, mimikatz.exe**

## Answers
- **04 (Recipient)**: itsupport@mail.thaicert.ncx
- **05 (Sender)**: somchai@mail.thaicert.ncx
- **06 (Sender IP)**: 199.127.63.151
- **07 (Program)**: sendEmail-1.56
- **08 (Last View Time)**: 2025-10-02T06:43:47Z
- **09 (Email MD5)**: 064509ecb90dabe7d38ff65dc1f9cdcc (คำนวณจากเนื้อความที่พบ)
- **10 (File Creation Time)**: 06:37:19
- **11 (Latest Downloaded File)**: CompanyDrive.zip
- **12 (Extraction Program)**: 7z.exe
- **13 (Extraction PID)**: (โปรดตรวจสอบค่าในระบบ - น่าจะเป็นตัวเลข 4-5 หลักจาก Sysmon)
- **14 (Extracted Files)**: CompanyDrive.exe, Manual.exe, mimidrv.sys, mimikatz.exe
