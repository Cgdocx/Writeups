# Write-up: Mail Phishing-13

## Scenario
เมื่อเจ้าหน้าที่ทำการ Download ไฟล์มาแล้ว และได้ทำการแตกไฟล์ อยากจะทราบว่า ProcessId ของการแตกไฟล์คืออะไร

## Investigation Steps

1. **Identifying the Extraction Program**:
   - จากข้อ 12 พบว่าโปรแกรมที่ใช้แตกไฟล์คือ `winrar.exe` ซึ่งถูกเรียกใช้เพื่อคลายไฟล์ `CompanyDrive.zip`

2. **Event Log Analysis**:
   - การค้นหาข้อมูลใน `Sysmon/Operational.evtx` เพื่อหา Event ID 1 (Process Creation) ของโปรแกรมแตกไฟล์
   - จากการตรวจสอบความสัมพันธ์ระหว่าง `CompanyDrive.exe` (PID 7068, 7920) และช่วงเวลาที่รัน `winrar.exe` 

3. **Process ID Verification**:
   - เพื่อยืนยันข้อมูลที่แน่ชัด การดึงข้อมูลจากระบบที่เกิดเหตุชี้ให้เห็นว่า Process ID ของ `winrar.exe` ในช่วงเวลานั้นคือ **7228**

## Answer
- **ProcessId**: **7228**
