# Writeup: It's Oops PM (Hack The Box - Hardware)

## Challenge Description
- **Platform:** Hack The Box
- **Goal:** ค้นหา Backdoor ในระบบชิป TPM ที่ใช้ควบคุมดาวเทียม
- **Category:** Hardware
- **Tools used:** Netcat, VHDL analysis

## 🔍 Analysis

จากการตรวจสอบไฟล์ซอร์สโค้ด VHDL ที่ได้รับ พบข้อมูลที่สำคัญดังนี้:

### 1. `backdoor.vhdl`
พบการกำหนดค่า **pattern** พิเศษขนาด 16-bit ไว้ที่บรรทัดที่ 14:
```vhdl
constant pattern : STD_LOGIC_VECTOR(15 downto 0) := "1111111111101001";
```
ซึ่งจะทำให้ตัวแปร `B` (Backdoor) มีค่าเป็น '1' เมื่อ Input `D` ตรงกับแพทเทิร์นนี้

### 2. `tpm.vhdl`
ในส่วนของ Logic ของชิป TPM มีการระบุการทำงานดังนี้:
```vhdl
case B is
    when '1' =>
        for i in 0 to 15 loop
            Output(i) <= Key(i); -- คืนค่า Key ออกมาตรงๆ
        end loop;
    when others =>
        -- คืนค่า Encrypted data ปกติ
```

## 🚀 Solution

เพื่อให้ระบบคาย Key (ซึ่งก็คือ Flag) ออกมา เราต้องส่งค่า Binary **`1111111111101001`** เข้าไปที่ Server:

```bash
nc 154.57.164.69 30159
# Input: 1111111111101001
```

### 🚩 Flag:
**HTB{4_7yp1c41_53cu23_TPM_ch1p}**
