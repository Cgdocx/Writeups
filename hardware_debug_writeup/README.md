# Writeup: Debug (Hack The Box - Hardware)

## Challenge Description
- **Platform:** Hack The Box
- **Goal:** ถอดรหัสสัญญาณ Serial จากไฟล์ Logic Analyzer (.sal) ที่บันทึกช่วงการ Boot ของดาวเทียม
- **Category:** Hardware
- **Tools used:** Saleae Logic / Python / PulseView

## 🔍 Analysis

จากการตรวจสอบไฟล์ `hw_debug.sal` ซึ่งเป็นไฟล์บันทึกสัญญาณ Digital จาก Saleae Logic Analyzer:

1. **Channels:** พบสัญญาณในช่อง 0 (TX) และช่อง 1 (RX)
2. **Metadata:** สัญญาณถูกบันทึกที่ Sample Rate 25 MS/s
3. **Protocol:** เป็นสัญญาณแบบ Asynchronous Serial (UART)

## 🚀 Solution

การหา Flag ทำได้โดยการถอดรหัส (Decode) สัญญาณในช่อง TX ด้วย Baud Rate มาตรฐานคือ **115200** (หรือ 9600):

1. เปิดไฟล์ `.sal` ด้วยโปรแกรม Saleae Logic หรือ PulseView
2. เพิ่ม **Async Serial Analyzer** ในช่อง TX
3. ตั้งค่า Baud Rate เป็น **115200**, 8 Bits, 1 Stop Bit, No Parity
4. ตรวจสอบข้อความที่แสดงออกมาใน Console จะพบ Flag ซ่อนอยู่ในบรรทัดการ Boot

### 🚩 Flag:
**HTB{547311173_n37w02k_c0mp20m153d}**
