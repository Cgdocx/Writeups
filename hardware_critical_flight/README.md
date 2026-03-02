# Writeup: Critical Flight (Hack The Box - Hardware)

## Challenge Description
- **Platform:** Hack The Box
- **Goal:** ค้นหาสิ่งที่ถูกดัดแปลงในไฟล์ Gerber ของโดรน
- **Category:** Hardware
- **Tools used:** Online Gerber Viewer / Gerbv

## 🔍 Analysis

จากการตรวจสอบไฟล์ Gerber ทั้งหมด พบข้อมูลที่น่าสงสัยในชั้นทองแดง (Copper Layer):

### 1. `In1_Cu.gbr` และ `In2_Cu.gbr`
ในชั้นทองแดงด้านใน มีการวาดเส้นวงจรที่ไม่ได้เชื่อมต่อกับอุปกรณ์ใดๆ แต่เรียงตัวกันเป็นตัวอักษรส่วนแรกของ Flag

### 2. `B_Cu.gbr`
ในชั้นทองแดงด้านล่าง (Bottom Layer) พบการวาดตัวอักษรส่วนที่เหลือของ Flag

## 🚀 Solution

การหา Flag ต้องใช้โปรแกรมดู Gerber (เช่น **Gerbv**) และทำการปิดชั้น (Layer) อื่นๆ ให้หมด เหลือเพียงชั้นทองแดงด้านในและด้านล่าง จะพบตัวอักษรที่วาดไว้:

1. เปิดไฟล์ Gerber ทั้งหมด
2. ปิดชั้น Silkscreen, Mask, และ Front Copper
3. สังเกตลายเส้นที่ซ่อนอยู่ในชั้น **Bottom Copper** และ **Inner Copper Layers**

### 🚩 Flag:
**HTB{533_7h3_1nn32_w02k1n95_0f_313c720n1c5#$@}**
