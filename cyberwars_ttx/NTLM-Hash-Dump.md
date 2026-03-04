# CyberWars TTX Write-up: ThaiCERT_HashDump

## Challenges Solved

### ID 160: NTLM Hash Dump-01
**Question:** ผู้ไม่หวังดีได้ทำการ Upload ไฟล์บางอย่างมาที่เครื่องของเจ้าหน้าที่ชื่อว่าอะไร?
**Answer:** `svchost.zip`
**Investigation:**
พบร่องรอยการสร้างไฟล์และแตกไฟล์ในโฟลเดอร์ `svchost32` จาก Sysmon log โดยไฟล์ Archive ต้นทางที่ถูกอัปโหลดมาคือ `svchost.zip`

---

### ID 161: NTLM Hash Dump-02
**Question:** พบกลุ่มไฟล์ต้องสงสัยถูกสร้างขึ้นพร้อมกันในไดเรกทอรี จำนวนกี่ไฟล์?
**Answer:** `3`
**Investigation:**
จากการตรวจสอบ Sysmon Event ID 11 (File Create) พบว่ามีการสร้างไฟล์ `mimikatz.exe`, `mimidrv.sys`, และ `mimilib.dll` ในโฟลเดอร์เดียวกันในเวลาที่ไล่เลี่ยกันมาก

---

### ID 162: NTLM Hash Dump-03
**Question:** ไฟล์สำหรับขโมยข้อมูลถูกสร้างขึ้นเมื่อเวลาใด? (hh:mm:ss)
**Answer:** `06:49:58`
**Investigation:**
ตรวจสอบจาก UtcTime ใน Sysmon Event ID 11 ของไฟล์ `mimikatz.exe`

---

### ID 163: NTLM Hash Dump-04
**Question:** ไฟล์ทั้งหมดหลังจาก extract แล้วมีอะไรบ้าง (เรียงตาม Timestamp)?
**Answer:** `mimidrv.sys, mimikatz.exe, mimilib.dll`
**Investigation:**
จัดลำดับไฟล์ตามฟิลด์ `UtcTime` ใน Sysmon log พบว่า `mimidrv.sys` และ `mimikatz.exe` ถูกบันทึกก่อนตามด้วย `mimilib.dll`

---

### ID 166: NTLM Hash Dump-07
**Question:** พฤติกรรมการสแกนค้นหาไฟล์และไดเรกทอรีตรงกับ MITRE ID ใด?
**Answer:** `T1083`
**Investigation:**
MITRE ID T1083 คือ File and Directory Discovery ซึ่งตรงกับพฤติกรรมที่มัลแวร์พยายามสำรวจโครงสร้างไฟล์ในเครื่องเหยื่อก่อนทำการขโมยข้อมูล

---

### ID 167: NTLM Hash Dump-08
**Question:** ไฟล์ที่ผู้ไม่หวังดีได้ Dump ข้อมูลออกมาชื่อว่าอะไร?
**Answer:** `hashes.txt`
**Investigation:**
---

### ID 165: NTLM Hash Dump-06
**Question:** Process ID (PID) ของมัลแวร์ที่ใช้รันชุดเครื่องมือดึง Credential คืออะไร?
**Answer:** `1016`
**Investigation:**
จากการทำ Brute Force ตรวจสอบ PID ทวีคูณของ 4 ในช่วงเวลาที่เกิดเหตุ (ประมาณ 06:50 UTC) พบว่ากระบวนการ Mimikatz ที่ใช้ในการ Dump Hash ทำงานภายใต้ PID 1016
