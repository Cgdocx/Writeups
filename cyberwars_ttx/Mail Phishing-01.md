# Write-up: Mail Phishing-01

## Scenario
เจ้าหน้าที่ในองค์กรได้รับ email Phishing ที่หลอกให้ติดตั้ง Software ป้องกัน Cybersecurity หลังจากติดตั้งพบความผิดปกติในระบบ ทีม Cybersecurity Analysis ต้องหาสาเหตุ โดยระบุเวลาที่เจ้าหน้าที่เข้าใช้งานหน้าเว็บเมล์ครั้งแรกหลังจากได้รับอีเมล

## Investigation Steps

1. **Analysis of Artifacts**: 
   จากการตรวจสอบไฟล์ `win10client/Collection-Win10_thaicert_ncx-2025-10-05T16_11_30Z.zip` ซึ่งเป็นข้อมูลที่เก็บรวบรวมจากเครื่องคอมพิวเตอร์ของเจ้าหน้าที่ (User: `itsupport`) พบไฟล์ประวัติการใช้งาน Browser (Chrome History)

2. **Log Extraction**:
   แตกไฟล์ `results/Windows.Applications.Chrome.History.csv` จากชุดข้อมูล Collection เพื่อตรวจสอบประวัติการเข้าชมเว็บไซต์

3. **Identifying the Incident**:
   ค้นหาประวัติการเข้าใช้งานที่เกี่ยวข้องกับ "mail" พบว่ามีการเข้าถึง URL ของเว็บเมล์องค์กรคือ `https://box.mail.thaicert.ncx/mail`

4. **Evidence**:
   พบรายการบันทึกใน Chrome History ดังนี้:
   ```csv
   itsupport,1,2025-10-02T06:34:07Z,https://box.mail.thaicert.ncx/mail,box.mail.thaicert.ncx Webmail :: Inbox,...
   ```

5. **Time Conversion**:
   เวลาที่ปรากฏในบันทึกคือ `2025-10-02T06:34:07Z` ซึ่งตัวอักษร `Z` ต่อท้ายหมายถึงเวลาในรูปแบบ **UTC (Coordinated Universal Time)**

## Answer
เวลาที่เจ้าหน้าที่เข้าใช้งานหน้าเว็บ mail ครั้งแรกคือ **06:34:07**
