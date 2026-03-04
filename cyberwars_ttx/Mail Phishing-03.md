# Write-up: Mail Phishing-03

## Scenario
โปรแกรมที่ใช้ในการเข้าถึง Web Mail (Chrome) ถูกเรียกใช้งานภายใต้บริบทและสิทธิ์ของผู้ใช้งาน (User Account) ใดในเครื่องดังกล่าว ให้ตอบเป็นรูปแบบ (Workgroup\User)

## Investigation Steps

1. **User Identification**:
   จากไฟล์ `results/Windows.Sys.Users.csv` และประวัติการใช้งาน Chrome พบว่าบัญชีผู้ใช้ที่ล็อกอินและใช้งานเว็บเมล์ในช่วงเวลาที่เกิดเหตุคือ **itsupport**
   - SID: `S-1-5-21-2589627397-2393488385-2100276161-1103` (บ่งบอกว่าเป็น Local Account)

2. **Computer Name Identification**:
   - ตรวจสอบ Network Logs (PCAP) โดยใช้โปรโตคอล NBNS (NetBIOS Name Service)
   - พบชื่อเครื่องที่ผูกกับ IP `10.1.3.1` (ซึ่งเป็น Local IP ของเครื่องที่วิเคราะห์ตามไฟล์ `Netstat.csv`) คือ **WIN-9CVO996T4L7**
   - นอกจากนี้ยังพบ NetBIOS Domain Name คือ **THAICERTNCX**

3. **Format Construction**:
   - โจทย์ต้องการรูปแบบ `Workgroup\User`
   - สำหรับ Local Account รูปแบบมาตรฐานคือ `COMPUTERNAME\User`
   - Computer Name คือ **WIN-9CVO996T4L7**
   - User Name คือ **itsupport**

## Answer
บัญชีผู้ใช้งานคือ **THAICERTNCX\itsupport**
