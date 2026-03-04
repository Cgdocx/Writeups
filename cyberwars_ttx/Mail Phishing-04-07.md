# Write-up: Mail Phishing-04 to 07

## Investigation Steps

1. **SMTP Traffic Analysis**:
   จากการตรวจสอบไฟล์ Network Logs (PCAP) ในโซน Client-Zone พบทราฟฟิกโปรโตคอล SMTP ที่มีการส่งอีเมล Phishing จากภายนอกเข้ามายังองค์กร
   - ค้นหาคำสั่ง `MAIL FROM` และ `RCPT TO` ใน PCAP

2. **Identifying Sender and Receiver**:
   พบรายการการส่งอีเมลดังนี้:
   - **Sender (MAIL FROM)**: `somchai@mail.thaicert.ncx` (ข้อ 05)
   - **Receiver (RCPT TO)**: `itsupport@mail.thaicert.ncx` (ข้อ 04)
   - **Source IP**: `199.127.63.151` (ข้อ 06)

3. **Analyzing Sending Program**:
   ตรวจสอบฟิลด์ `X-Mailer` ในส่วนข้อมูล SMTP DATA พบข้อมูลดังนี้:
   - `X-Mailer: sendEmail-1.56`
   - ซึ่งบ่งบอกว่าผู้ไม่หวังดีใช้โปรแกรม **sendEmail** เวอร์ชัน 1.56 (ข้อ 07)

## Answers
- **Mail Phishing-04 (ผู้รับ)**: **itsupport@mail.thaicert.ncx**
- **Mail Phishing-05 (ผู้ส่ง)**: **somchai@mail.thaicert.ncx**
- **Mail Phishing-06 (IP ผู้ส่ง)**: **199.127.63.151**
- **Mail Phishing-07 (โปรแกรมส่ง)**: **sendEmail-1.56**
