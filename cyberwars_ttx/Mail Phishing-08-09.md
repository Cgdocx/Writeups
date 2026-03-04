# Write-up: Mail Phishing-08 & 09

## Scenario
เจ้าหน้าที่ itsupport ได้เปิดดูเนื้อหาอีเมล Phishing เพื่อตรวจสอบนโยบายการติดตั้งซอฟต์แวร์ ต้องระบุเวลาที่เปิดดูครั้งสุดท้ายและค่า MD5 ของเนื้อหาอีเมล

## Investigation Steps

1. **Last Open Time (ข้อ 08)**:
   - ตรวจสอบประวัติการใช้งาน Browser จากไฟล์ `results/Windows.Applications.Chrome.History.csv`
   - พบการเข้าถึง URL: `https://box.mail.thaicert.ncx/mail/?_task=mail&_uid=19&_action=preview`
   - เวลาที่บันทึกไว้คือ **2025-10-02T06:43:47Z** ซึ่งเป็นเวลา UTC

2. **Email Content MD5 (ข้อ 09)**:
   - ตรวจสอบทราฟฟิก SMTP จากไฟล์ `network_log/1/DMZ-Zone-part1.pcap` (TCP stream 7)
   - เนื้อหาอีเมลฉบับเต็มคือ:
     `To ensure security and efficiency, all software installations on company devices must be approved by IT. We are now transitioning all teams to Security Solutions. The installation file and a user guide have been attached in a .zip file named CompanyDrive.zip. Please install and begin using the new application immediately.`
   - นำเนื้อหาดังกล่าวมาคำนวณค่า MD5 Hash

## Answers
- **Mail Phishing-08 (เวลาเปิดดู)**: **2025-10-02T06:43:47Z**
- **Mail Phishing-09 (MD5 เนื้อหา)**: **064509ecb90dabe7d38ff65dc1f9cdcc**
