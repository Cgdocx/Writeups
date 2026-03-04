# Write-up: Stealing Web Data && Manipulating Database - 01 to 10

## Scenario
ผู้ไม่หวังดีโจมตี Web Server ด้วยเทคนิค SQL Injection เพื่อขโมยข้อมูลจากฐานข้อมูลและพยายามแก้ไขข้อมูล โดยมีระบบ ModSecurity (WAF) และ Wazuh คอยตรวจจับ

## Investigation Steps

1. **Target Identification (ข้อ 01 & 02)**:
   - ตรวจสอบ `web02/apache2thaicertnews/error.log.6`
   - พบ Payload SQL Injection ถูกส่งไปยังไฟล์ **login.php**
   - IP ของผู้โจมตีคือ **103.124.60.3**

2. **Attacker Context (ข้อ 03 & 05)**:
   - ตรวจสอบ GeoIP ของ IP `103.124.60.3` พบว่ามาจากประเทศ **Vietnam**
   - User-Agent ที่ใช้ในการโจมตีคือ `Mozilla/5.0 (X11; U; Linux i686; en-US; rv:1.8.1.11) Gecko/20071204 Ubuntu/7.10 (gutsy) Firefox/2.0.0.11`

3. **Attack Details (ข้อ 04, 06, 07, 08)**:
   - ชื่อตารางที่ถูกขโมยข้อมูลคือ **login_sys.users** (ข้อ 04)
   - Fingerprint ที่ ModSecurity ตรวจพบคือ **s&(E1** (ข้อ 06)
   - ตารางระบบที่ถูกใช้เพื่อดูโครงสร้างฐานข้อมูลคือ **information_schema.schemata** (ข้อ 07)
   - ค่า Hex ของ Database เป้าหมายที่พบใน Payload คือ **0x6c6f67696e5f737973** (ถอดรหัสได้เป็น `login_sys`)

4. **WAF Analysis (ข้อ 09 & 10)**:
   - คะแนนความอันตรายสูงสุด (Inbound Anomaly Score) คือ **63**
   - ModSecurity Rule ID ที่ตรวจพบการโจมตีคือ **942100**

## Answers
- **01 (ไฟล์เป้าหมาย)**: login.php
- **02 (IP ผู้โจมตี)**: 103.124.60.3
- **03 (ประเทศ)**: Vietnam
- **04 (ตารางเป้าหมาย)**: login_sys.users
- **05 (User-Agent)**: Mozilla/5.0 (X11; U; Linux i686; en-US; rv:1.8.1.11) Gecko/20071204 Ubuntu/7.10 (gutsy) Firefox/2.0.0.11
- **06 (Fingerprint)**: s&(E1
- **07 (System Table)**: information_schema.schemata
- **08 (Hex Database)**: 0x6c6f67696e5f737973
- **09 (Max Score)**: 63
- **10 (Rule ID)**: 942100
