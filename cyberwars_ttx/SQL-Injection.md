# CyberWars TTX Write-up: ThaiCERT_SQL-Injection

## Challenges Solved
### ID 168: SQL Injection && File Upload web shell-01 
**Question:** Webserver ที่ให้บริการเปิดให้เข้าถึงได้จาก IP:Port อะไร? 
**Answer:** `122.155.145.163:4444` 
**Investigation:** 
จากการตรวจสอบ Log พบว่ามีการเรียกใช้งานเว็บผ่านพอร์ตที่ไม่ปกติคือ 4444 ซึ่งเชื่อมโยงกับ IP ภายนอก 122.155.145.163 
---

### ID 169: SQL Injection && File Upload web shell-02
**Question:** ชื่อเครื่องมือและภาษาที่เขียนที่ใช้สุ่มหา Directory/File?
**Answer:** `NCX2025(gobuster_go)`
**Investigation:**
ตรวจสอบ User-Agent ใน Apache access log พบข้อความ `gobuster/3.6` ซึ่งเป็นเครื่องมือที่เขียนด้วยภาษา Go (Golang)

---

### ID 170: SQL Injection && File Upload web shell-03
**Question:** ชื่อเทคนิคการโจมตีบนเว็บคืออะไร?
**Answer:** `SQL Injection`
**Investigation:**
ตรวจพบพฤติกรรมใน ModSecurity log และ access log ที่มีการส่งคำสั่ง SQL ผ่านพารามิเตอร์ของ URL (เช่น `UNION SELECT`, `ORDER BY`)

---

### ID 171: SQL Injection && File Upload web shell-04
**Question:** IP ของผู้ไม่หวังดีมาจากประเทศอะไร?
**Answer:** `Vietnam`
**Investigation:**
จากการทำ GeoIP Lookup ของ IP `103.124.60.3` ที่พบใน Log การโจมตี พบว่าเป็น IP จากประเทศเวียดนาม

---

### ID 173: SQL Injection && File Upload web shell-05
**Question:** เว็บใช้ฐานข้อมูลอะไรในการเก็บข้อมูล?
**Answer:** `NCX2025(mysql)`
**Investigation:**
ตรวจสอบกระบวนการ (Processes) และเซอร์วิสที่รันอยู่บนเซิร์ฟเวอร์เว็บ พบเซอร์วิส `mysqld` ทำงานอยู่

---

### ID 175: SQL Injection && File Upload web shell-07
**Question:** PID ใดที่เกี่ยวข้องกับสคริปต์ที่ใช้เริ่มต้นฐานข้อมูล MySQL?
**Answer:** `946`
**Investigation:**
จากข้อมูล `NetstatEnriched.json` พบว่า `mysqld_safe` (สคริปต์หุ้มสำหรับเริ่ม MySQL) ทำงานอยู่ที่ PID 946

---

### ID 176: SQL Injection && File Upload web shell-08
**Question:** ผู้ไม่หวังดีพยายามเข้าถึงตารางใดในฐานข้อมูล?
**Answer:** `login_sys.users`
**Investigation:**
ตรวจสอบจาก ModSecurity Audit Log พบคำสั่ง SQL ที่พยายาม Query ข้อมูลจากตาราง `login_sys.users`

---

### ID 174: SQL Injection && File Upload web shell-06
**Question:** คำสั่งที่ใช้เริ่มต้นฐานข้อมูล MySQL สำหรับเว็บ ThaiCERTNews คืออะไร?
**Answer:** `/usr/sbin/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib/mysql/plugin --log-error=/var/log/mysql/error.log --pid-file=/var/run/mysqld/mysqld.pid --socket=/var/run/mysqld/mysqld.sock`
**Investigation:**
จากข้อมูล Process list ใน NetstatEnriched.json ระบุ CommandLine แบบเต็มของ mysqld process (PID 1120)

---

### ID 178: SQL Injection && File Upload web shell-10
**Question:** การโจมตี SQL Injection ที่ตรวจพบโดย ModSecurity เกิดขึ้นที่ URI ใด?
**Answer:** `/login.php`
**Investigation:**
ตรวจสอบจาก ModSecurity Audit Log พบเหตุการณ์ SQL Injection (Rule 942100) เกิดขึ้นซ้ำๆ ที่ URI `/login.php`

---

### ID 179: SQL Injection && File Upload web shell-11
**Question:** การโจมตี SQL Injection ที่ตรวจพบโดย ModSecurity มาจาก IP อะไร?
**Answer:** `103.124.60.3`
**Investigation:**
จาก Log ของ ModSecurity พบว่า IP `103.124.60.3` เป็นต้นทางของการส่ง Payload SQL Injection

---

### ID 180: SQL Injection && File Upload web shell-12
**Question:** ModSecurity ตรวจพบการโจมตีด้วยกฎ ID ใด และความรุนแรงคืออะไร?
**Answer:** `942100:CRITICAL`
**Investigation:**
ใน `error.log.6` ระบุชัดเจนว่าถูกดักจับด้วย [id "942100"] และระดับความรุนแรง [severity "CRITICAL"]

---

### ID 181: SQL Injection && File Upload web shell-13
**Question:** PID และค่า Hash (SHA256) ของ mysqld_safe คืออะไร?
**Answer:** `946:64e48365207d0c19008ba7d53d75c0de3fcd5a1590e4c40fc69c677663fedc20`
**Investigation:**
สกัดข้อมูลจาก `Linux.Sys.Pslist.json` ซึ่งบันทึก Process ของ `mysqld_safe` (PID 946) พร้อมด้วยค่า SHA256

---

### ID 182: SQL Injection && File Upload web shell-14
**Question:** IP ใดที่ใช้เครื่องมือในการสแกนหา path สำหรับ File Upload web shell?
**Answer:** `199.127.63.144, 103.124.60.6`
**Investigation:**
จาก `access.log` พบว่า 2 IP นี้ใช้เครื่องมือ `gobuster` ในการสแกนหา Directory และ Path ที่เกี่ยวข้องกับการอัปโหลดไฟล์

---

### ID 184: SQL Injection && File Upload web shell-16
**Question:** ชื่อไฟล์ webshell ที่ผู้ไม่หวังดีอัพโหลดและใช้งานสำเร็จคืออะไร?
**Answer:** `profileblueeagle.php`
**Investigation:**
จากการตรวจสอบ `access.log` พบการเข้าถึงไฟล์ `/uploads/profileblueeagle.php` พร้อมพารามิเตอร์ `cmd=` ซึ่งเป็นการสั่งรัน Command ผ่าน Web Shell

---

### ID 185: SQL Injection && File Upload web shell-17
**Question:** ผู้ไม่หวังดีได้อ่านไฟล์อะไรบ้างจาก Path /etc/?
**Answer:** `hosts, passwd, lsb_release`
**Investigation:**
ประวัติการสั่งงาน Web Shell ใน `access.log` บันทึกคำสั่ง `cat /etc/hosts`, `cat /etc/passwd`, และ `cat /etc/lsb_release`

---

### ID 187: SQL Injection && File Upload web shell-19
**Question:** ผู้ไม่หวังดีทำการอัปโหลดไฟล์ลงบนเว็บเซิฟเวอร์ที่ path ใด?
**Answer:** `/var/www/html/uploads/profileblueeagle.php`
**Investigation:**
เมื่อรวม Root Path ของ Apache (`/var/www/html`) เข้ากับ URI ที่ถูกเรียกใช้งาน (`/uploads/profileblueeagle.php`) จะได้เป็น Path เต็มของไฟล์

---

### ID 186: SQL Injection && File Upload web shell-18
**Question:** ผู้ไม่หวังดีเริ่มการโจมตีด้วยวิธีการ SQL Injection ครั้งแรกเมื่อเวลาใด?
**Answer:** `13:55:30`
**Investigation:**
จาก ModSecurity `error.log.6` และ `access.log` พบว่า Payload ของ SQL Injection ครั้งแรกที่ถูกส่งมาจาก IP 103.124.60.3 ถูกดักจับได้ที่เวลา 13:55:30 (เวลาตาม Log ท้องถิ่น)


