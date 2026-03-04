# Write-up: Mail Phishing-02

## Scenario
จุดเริ่มต้นของการสืบสวนพบว่า การเข้าถึง Web Mail ของผู้ใช้งานเป็นต้นตอของเหตุการณ์ จงระบุชื่อโปรแกรม (Process Name) ที่ผู้ใช้งานใช้ในการเปิด Web Mail ซึ่งเป็นประตูแรกที่นำไปสู่การดาวน์โหลดไฟล์ต้องสงสัย

## Investigation Steps

1. **Browser History Analysis**:
   จากการวิเคราะห์ประวัติการใช้งาน Browser ในโจทย์ข้อที่แล้ว (Mail Phishing-01) พบว่าข้อมูลประวัติการเข้าใช้งาน Web Mail ปรากฏอยู่ในไฟล์:
   `results/Windows.Applications.Chrome.History.csv`

2. **Identifying the Browser**:
   - ประวัติการเข้าใช้งาน URL `https://box.mail.thaicert.ncx/mail` ในเวลา `06:34:07 UTC` ถูกบันทึกโดย Google Chrome
   - ตรวจสอบไฟล์ `results/Windows.Applications.Edge.History.csv` (Microsoft Edge) ไม่พบรายการการเข้าใช้งาน Web Mail ในช่วงเวลาที่เกิดเหตุ

3. **Verification with Artifacts**:
   - ไฟล์ประวัติการใช้งานถูกดึงมาจากพาธ: `C:\Users\itsupport\AppData\Local\Google\Chrome\User Data\Default\History`
   - พาธนี้เป็นไดเรกทอรีเก็บข้อมูลเฉพาะของโปรแกรม **Google Chrome**

4. **Process Name**:
   ชื่อโปรแกรมมาตรฐาน (Process Name) ของ Google Chrome บนระบบปฏิบัติการ Windows คือ `chrome.exe`

## Answer
ชื่อโปรแกรม (Process Name) คือ **chrome.exe**
