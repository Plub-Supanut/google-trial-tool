# Google AI Trial Tool

เนื้อหานี้นำมาจากกลุ่ม QQ: 797481450
วิธีนี้มีความเสี่ยงที่จะถูกแบนบัญชี เนื่องจากเป็นการเรียกใช้งานผ่านเครื่องมือ โปรดใช้อย่างระมัดระวัง


ใช้ Frida inject เข้าไปในโปรเซสของ Google One เพื่อเรียก BillingClient API สำหรับค้นหาและเปิดใช้งานสิทธิ์ทดลองใช้ฟรี $0 ที่ซ่อนอยู่

## สิ่งที่ต้องเตรียม

| องค์ประกอบ | คำอธิบาย |
|---|---|
| **MuMu Emulator** | เปิดเครื่องจำลองแล้ว, ล็อกอินบัญชี Google และผูกวิธีชำระเงินเรียบร้อยแล้ว |
| **adb** | อยู่ใน PATH หรือกำหนดผ่าน environment variable `ADB_PATH` |
| **frida** (ฝั่งเครื่องหลัก/Host) | `pip install frida-tools` เวอร์ชัน ≥ 16 |
| **frida-server** (บนอุปกรณ์) | push ไฟล์ไปที่ `/data/local/tmp/frida-server` พร้อมตั้งสิทธิ์รัน (`chmod 755`) |
| **module.apk** (บนอุปกรณ์) | ไฟล์ APK ที่มีไลบรารี BillingClient โดย push ไปที่ `/data/local/tmp/module.apk` |
| **Google One** | ติดตั้งแอปไว้ในอุปกรณ์แล้ว (`com.google.android.apps.subscriptions.red`) |

## การเตรียมอุปกรณ์

```bash
# เชื่อมต่อ MuMu
adb connect 127.0.0.1:16384

# Push ไฟล์ frida-server (ดาวน์โหลดให้ตรงกับสถาปัตยกรรม CPU จาก https://github.com/frida/frida/releases)
adb push frida-server /data/local/tmp/frida-server
adb shell "chmod 755 /data/local/tmp/frida-server"

# Push ไฟล์ module.apk (ไฟล์ APK ที่มี com.android.billingclient)
adb push module.apk /data/local/tmp/module.apk
```

## วิธีใช้งาน

```bash
# แสดงรายการสิทธิ์ทดลองใช้ทั้งหมดที่ใช้ได้ในบัญชีปัจจุบัน
./auto-trial.sh --list

# เปิดใช้งานทดลองใช้ฟรี Pro (5TB, $19.99/เดือน) 3 เดือน
./auto-trial.sh --product pro --dur 3mo

# เปิดใช้งานทดลองใช้ฟรี Pro variant (5TB, $19.99/เดือน) 12 เดือน ★แนะนำ
./auto-trial.sh --product pro12 --dur 12mo

# เปิดใช้งานทดลองใช้ฟรี Ultra 20x (30TB, $199.99/เดือน) 12 เดือน
./auto-trial.sh --product 20x --dur 12mo

# เปิดใช้งานทดลองใช้ฟรี Ultra 5x (20TB, $99.99/เดือน) 6 เดือน
./auto-trial.sh --product 5x --dur 6mo

# เลือกสิทธิ์ทดลองใช้ที่นานที่สุดให้อัตโนมัติ (ค่าเริ่มต้นคือแพ็กเกจสูงสุด)
./auto-trial.sh

# ระบุพอร์ต ADB ของ MuMu
./auto-trial.sh --port 16416 --product pro --dur 3mo
```

### พารามิเตอร์

| พารามิเตอร์ | คำอธิบาย | ค่าเริ่มต้น |
|---|---|---|
| `--list` | แสดงรายการสิทธิ์ทดลองใช้เท่านั้น (ไม่เปิดใช้งาน) | (โหมดเปิดใช้งาน) |
| `--product` | `pro` / `pro12` / `5x` / `20x` / `auto` / Product ID เต็ม `g1.*` | `auto` |
| `--dur` | `1mo` / `3mo` / `6mo` / `12mo` / `longest` | `longest` |
| `--port` | พอร์ต ADB ของ MuMu | ค้นหาอัตโนมัติ |
| `--adb` | Path ของ adb | ค้นหาอัตโนมัติ |

### Environment Variables

| ตัวแปร | คำอธิบาย |
|---|---|
| `ADB_PATH` | Path ของไฟล์โปรแกรม adb |
| `FRIDA_SCRIPT` | Path ของไฟล์ frida-auto-trial.js (ค่าเริ่มต้น: โฟลเดอร์เดียวกับสคริปต์) |
| `FRIDA_SERVER_PATH` | Path ของ frida-server บนอุปกรณ์ (ค่าเริ่มต้น: `/data/local/tmp/frida-server`) |
| `MODULE_APK_DEVICE` | Path ของ module.apk บนอุปกรณ์ (ค่าเริ่มต้น: `/data/local/tmp/module.apk`) |

## ตาราง Product ID

| ชื่อย่อ | Product ID | ความจุ | ค่าบริการรายเดือน | ทดลองใช้ |
|---|---|---|---|---|
| **pro12** ★ | `g1.2tb.ai.subs_variant` | 5TB | $19.99 | 6/12 เดือน |
| **pro** | `g1.play.2tb.ai` | 5TB | $19.99 | 3 เดือน |
| **5x** | `g1.play.20tb` | 20TB | $99.99 | 1/3/6/12 เดือน |
| **20x** | `g1.play.ai.pro` | 30TB | $199.99 | 1/3/6/12 เดือน |
| — | `g1.2tb.ai` | 5TB | $19.99 | ไม่มี |
| — | `g1.ai.pro` | 30TB | $199.99 | ไม่มี |
| — | `g1.30tb` | 30TB | $149.99 | ไม่มี |

ปุ่ม "อัปเกรด" บนหน้าแอปจะเรียกใช้ Product ID ที่ไม่มีสิทธิ์ทดลองใช้ (เช่น `g1.2tb.ai`)

> **หมายเหตุ**: ณ วันที่ 2026-11-19 สิทธิ์ทดลองใช้ของ Ultra 5x/20x ถูกปิดไปแล้ว (offer ถูกลบออก) ส่วน Pro มี Product ID อยู่ 2 ตัว:
> - `g1.2tb.ai.subs_variant` (ชื่อย่อ `pro12`): ทดลองใช้ฟรีสูงสุด **12 เดือน** (แนะนำให้ใช้ตัวนี้เป็นหลัก)
> - `g1.play.2tb.ai` (ชื่อย่อ `pro`): ทดลองใช้ฟรี 3 เดือน
>
> หมายเหตุ: Frida ≥ 17 มี Breaking Changes (ต้องโหลด Java bridge เองโดยตรง) แนะนำให้ใช้ Frida **16.7.19**

## วิธีผ่านหน้าเว็บล้วน — ใช้ไม่ได้ผล

จากการทดสอบ พบว่าวิธีฝั่งเว็บทั้งหมดต่อไปนี้ไม่สามารถผ่านการตรวจสอบฝั่งเซิร์ฟเวอร์ (server-side validation) ของ Google Play ได้:

1. **Static URL**: `rpctoken`, `_gfid` และ payments blob ผูกติดกับ session เมื่อหมดอายุจะใช้ต่อไม่ได้ทันที
2. **แก้พารามิเตอร์ `ear` ผ่าน Console**: ค่า `ear` ที่หน้าเว็บสร้างขึ้น (25 หลัก) จะเก็บ Product ID ไว้ที่ `ear[13]` และเก็บ integrity signature ไว้ที่ `ear[24]` เนื่องจาก signature ผูกกับแพ็กเกจเดิม หากเปลี่ยนแพ็กเกจแล้วไม่ว่าจะคง signature เดิมไว้หรือลบทิ้ง เซิร์ฟเวอร์จะคืนค่า HTTP 400 ทั้งหมด
3. **Chrome Extension แบบ RPC Hook**: แนวทางจากคอมมูนิตี้ที่ดักจับระดับ `Ngbm` RPC payload แต่รีโปซิทอรีที่เกี่ยวข้องถูกลบ (404) ไปแล้ว

> **สรุป**: สิทธิ์ทดลองใช้ที่ซ่อนอยู่ (`g1.play.2tb.ai`) ไม่ได้แสดงบนหน้าเว็บใดๆ ฝั่งเว็บจึงไม่สามารถสร้าง checkout session ที่ถูกต้องได้ วิธีเดียวที่ได้ผลแน่นอนคือใช้ Frida inject เพื่อเรียก BillingClient API จากฝั่งอุปกรณ์โดยตรง

## ข้อควรระวัง

- สคริปต์ทำหน้าที่เพียงเรียกหน้าต่างชำระเงินของ Google Play ขึ้นมาเท่านั้น ผู้ใช้ต้องกดยืนยันและกรอกรหัสผ่านด้วยตนเอง
- เมื่อครบกำหนดทดลองใช้ ระบบจะต่ออายุอัตโนมัติ อย่าลืมกดยกเลิกล่วงหน้า
- บัญชีต้องผูกวิธีชำระเงินไว้แล้ว และต้องไม่มีแพ็กเกจที่กำลังใช้งานอยู่ มิฉะนั้นจะไม่สามารถรับสิทธิ์ทดลองใช้ได้
- หาก `launchBillingFlow` คืนค่าเป็น `code=0` อาจถือว่าใช้สิทธิ์ทดลองใช้ไปแล้ว แม้ว่าหน้าต่างชำระเงินจะไม่แสดงขึ้นมาก็ตาม
- แนะนำให้ทำทุกขั้นตอนบน Emulator และผูกบัตรที่มีวงเงินหรือยอดเงินจำกัดไว้เพื่อความปลอดภัย

## คำอธิบายไฟล์

- `auto-trial.sh` — สคริปต์ Shell ตัวหลัก สำหรับค้นหาอุปกรณ์อัตโนมัติ จัดการ `frida-server`, inject สคริปต์ และติดตามผลลัพธ์
- `frida-auto-trial.js` — สคริปต์ Frida สำหรับ inject เข้าโปรเซส โดยโหลด `BillingClient` ผ่าน `DexClassLoader` เพื่อค้นหาและเปิดใช้งานสิทธิ์ทดลองใช้



กลุ่มพูดคุยแลกเปลี่ยนข้อมูล: t.me/muihbb
