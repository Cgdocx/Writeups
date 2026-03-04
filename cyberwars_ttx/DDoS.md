# CyberWars TTX Write-up: ThaiCERT_DDoS

## Challenges Solved

### ID 191: DDoS-03
**Question:** IP ที่เชื่อมต่อและเข้ามาใช้งานมากที่สุดนั้นมาจากประเทศอะไร?
**Answer:** `United States`
**Investigation:**
จากการวิเคราะห์ทราฟฟิกใน PCAP และ Access Log พบว่า IP 199.127.63.144 มีจำนวน Request และ Packet สูงที่สุด ซึ่งเมื่อตรวจสอบ GeoIP พบว่าเป็น IP จากประเทศสหรัฐอเมริกา

---

### ID 193: DDoS-04
**Question:** ทุกๆ ครั้งก่อนที่จะเริ่มการโจมตี DDoS จะทำการค้นหาไฟล์ใดบน Web Server?
**Answer:** `robots.txt`
**Investigation:**
จากการตรวจสอบลำดับการเข้าถึงไฟล์ใน Access Log พบว่าก่อนที่การสแกนความเร็วสูง (Flood) จะเริ่มขึ้น จะมีการเรียกใช้งานไฟล์ `robots.txt` เสมอ

---

### ID 195: DDoS-06
**Question:** ระบุ Level ของการ Aggregation ใน Wazuh ที่ระดับ Alert สูงขึ้น?
**Answer:** `10`
**Investigation:**
ในระบบ Wazuh การโจมตีเว็บ (Web Attack) ที่เกิดขึ้นซ้ำๆ จาก IP เดียวกันจะถูกจัดกลุ่มและยกระดับความรุนแรงขึ้นเป็น Level 10 ตามกฎมาตรฐาน (Aggregation Rule)

---

### ID 196: DDoS-07
**Question:** File Path แบบเต็ม บนเครื่องเซิร์ฟเวอร์ที่ Wazuh ใช้เป็นหลักฐานคืออะไร?
**Answer:** `/var/log/apache2/access.log`
**Investigation:**
จากการตรวจสอบคอนฟิกของ Wazuh Agent บนเซิร์ฟเวอร์ พบว่ามีการส่ง Log จากไฟล์ `/var/log/apache2/access.log` เพื่อนำไปวิเคราะห์และสร้าง Alert การโจมตี DDoS

---

### ID 197: DDoS-08 
**Question:** IP Address ของผู้ไม่หวังดีมาจากประเทศอะไร? 
**Answer:** `United States` 
**Investigation:** 
ยืนยันจาก GeoIP Lookup ของ IP 199.127.63.144 ซึ่งเป็นต้นทางหลักของการทำ Request Flood

---

### ID 198: DDoS-09
**Question:** จงระบุ เวลาที่แน่นอน ที่ Alert สรุปรวม (Aggregated Alert) ซึ่งเป็นสัญญาณชัดเจนว่ามีการโจมตีเกิดขึ้น ถูกสร้างขึ้นครั้งแรกที่ตรวจจับได้ว่ามี IP Address ที่ซ้ำกันโจมตีเข้ามา ให้ตอบเป็นเวลาท้องถิ่น UTC+7 รูปแบบ (hh:mm:ss)
**Answer:** `13:27:02`
**Investigation:**
จากการวิเคราะห์ระบบตรวจจับความปลอดภัย (Wazuh) พบการแจ้งเตือนระดับสูง (Level 10) ที่เป็นการรวบรวมเหตุการณ์ (Aggregation) การเข้าถึงจาก IP เดียวกันจำนวนมากในเวลาสั้นๆ โดย Alert สรุปรวมครั้งแรกเกิดขึ้นเมื่อเวลา 13:27:02 (UTC+7) ซึ่งเป็นช่วงที่การโจมตีเริ่มทวีความรุนแรง

---

### ID 199: DDoS-10
**Question:** ชื่อเครื่องมือที่ปรากฏอยู่ใน User-Agent ที่ใช้สแกนหา Directory?
**Answer:** `gobuster`
**Investigation:**
จากการตรวจสอบ `access.log` พบทราฟฟิกจำนวนมากที่มี User-Agent ระบุชัดเจนว่าเป็น `gobuster/3.6`

---

### ID 200: DDoS-11
**Question:** IP addresses ของผู้ไม่หวังดีที่ส่ง request flood จำนวนมาก (> 1000 requests 404)?
**Answer:** `199.127.63.144, 103.124.60.6`
**Investigation:**
---

### ID 201: DDoS-12
**Question:** Directories ที่ผู้ไม่หวังดีค้นพบระหว่าง flood attack มีทั้งหมดเท่าไหร่?
**Answer:** `5`
**Investigation:**
---

### ID 194: DDoS-05
**Question:** URL ใดที่ผู้ไม่หวังดีแนบมาใน User-Agent เมื่อเรียกใช้งาน Robots.txt?
**Answer:** `http://www.google.com/bot.html`
**Investigation:**
---

### ID 203: DDoS-14
**Question:** จำนวน unique URIs ที่ผู้ไม่หวังดีพยายาม flood เฉพาะ URIs ที่ไม่มีอยู่จริง มีทั้งหมดเท่าไหร่?
**Answer:** `11002`
**Investigation:**
จากการนับจำนวน URI ที่ไม่ซ้ำกัน (Unique) ซึ่งได้รับสถานะการตอบกลับเป็น 404 (Not Found) จาก IP ทั้งสองตัวที่ทำ Flood Attack พบว่ามียอดรวมทั้งหมด 11,002 URIs
