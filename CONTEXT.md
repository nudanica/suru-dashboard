# SURU Dashboard — Project Context

## URLs & IDs
- **Dashboard:** https://nudanica.github.io/suru-dashboard/
- **GitHub repo:** https://github.com/nudanica/suru-dashboard
- **Apps Script (Dashboard Data):** https://script.google.com/macros/s/AKfycbxZE92cQiRmratXJhgdYea3ih__gBbpZRLzFb-sucPOHDJERsvl3F8ea0Xv_uLOfczu/exec
- **Dashboard Data Sheets ID:** 1qAR5qjqv-a1lnxYMlwbL-xHixpuATn-8dIW3iLrpJDw
- **Master CF Sheets ID:** 1BB4KXYhDRhmN7if9IVOuaoPJhzR9021GUiAJs5KwP9k

---

## ระบบที่ทำเสร็จแล้ว ✅

### Master CF (Apps Script v7)
- Form 1 (SAVE LOT): บันทึกล็อตใหม่ → อัปเดตต้นทุนใน Master CF
- Form 2 (ADD NEW): เพิ่มสินค้าใหม่ → ออกรหัส CF อัตโนมัติ
- Copy format จากแถวแรกของหมวด (ไม่ hardcode สี/ฟอนต์)
- Formula I: `=IF(F>0, F+IF(H>0,H,0), IF(AND(E>0,G>0), E*G+H, IF(H>0,H,"")))`
- ไฟล์: `SURU_AppsScript_v7.js`

### Dashboard (index.html v6)
- โหลดข้อมูลจาก Google Sheets ทุกครั้งที่เปิด
- COGS คำนวณจาก CF code ใน Item Detail ของ Page365 CSV
- Net Profit = รายได้ - COGS - ค่าแอด - รายจ่าย
- Warning แจ้งเมื่อต้นทุนไม่ครบ
- CF_COST ดึงจาก Master CF real-time ทุกครั้ง
- Backup ข้อมูลขึ้น Sheets ผ่าน GET request (bypass CORS)
- กราฟ dropdown: รายวัน / รายสัปดาห์ / รายเดือน / รายปี
- Auto select เดือนล่าสุดที่มีข้อมูลเมื่อเปิด

### Apps Script Dashboard Data (v4)
- doGet: ดึงข้อมูลทุกประเภท รวม cf_cost, cf_names, stock
- doPost + doGet backup: รับข้อมูลจาก Dashboard ผ่าน GET
- backupSales: เก็บ COGS รายวันในคอลัมน์ G
- ไฟล์: `SURU_Dashboard_Script_v4.js`

### โครงสร้าง Master CF
| คอลัมน์ | ข้อมูล |
|---------|--------|
| A | รหัส CF |
| B | ชื่อสินค้า |
| C | ตัวเลือก |
| D | ราคาขาย ฿ |
| E | ราคา ¥ |
| F | ต้นทุน ฿ (บาท) |
| G | เรท |
| H | ค่าส่ง |
| I | ต้นทุน/ชิ้น (formula) |
| J | กำไร |
| K | GP% |
| L | หมายเหตุ |

### โครงสร้าง Dashboard Data Sheets
- **ยอดขาย_รายวัน:** วันที่, รายได้, บิล, ชิ้น, AOV, ค่าแอด, COGS
- **ยอดขาย_สรุปเดือน:** สรุปรายเดือน
- **รายจ่าย:** id, วันที่, หมวดหมู่, รายละเอียด, จำนวนเงิน
- **ค่าแอด:** วันที่, Transaction ID, บัญชี, จำนวนเงิน, สกุลเงิน
- **สต๊อก:** ข้อมูลสต็อกจาก Page365

---

## สิ่งที่ต้องทำต่อ 🔴

1. **เพิ่มต้นทุน P00009 และ P00012** ใน Master CF ผ่าน Form
2. **อัปโหลดค่าแอด CSV** (Meta) ม.ค.-พ.ค. 2569 ใน Dashboard
3. **อัปโหลดสต็อก CSV** จาก Page365 ใน Dashboard
4. **แก้ hardcode ค่าแอดในหน้า channels** (ตาราง ค่าโฆษณาแยกบัญชี ยังเป็น static)
5. **ระบบลูกค้าใหม่/เก่า** — รอข้อมูลย้อนหลัง 3 ปี
6. **ยอดขายไม่ตรง Page365** — Dashboard นับ 9,323 บิล (ไม่รวมเคลม), Page365 นับ 9,346

---

## ปัญหาที่รู้อยู่

- **COGS ประมาณการ** เพราะ Master CF ยังมีสินค้าหลายตัวไม่มีต้นทุน
- **ค่าแอด ROAS** ในหน้า channels ยังเป็น hardcode เก่า
- **Backup ผ่าน GET** จำกัดขนาด chunk 20 rows ต่อ request

---

## วิธีใช้งานปกติ

### อัปเดตยอดขายรายเดือน
1. Export CSV จาก Page365 → รายงาน → ยอดขาย
2. อัปโหลดใน Dashboard → อัปโหลดยอดขาย

### อัปเดตต้นทุนสินค้า
1. กรอก Form ADD NEW หรือ SAVE LOT ใน Master CF
2. เปิด Dashboard → Refresh → COGS อัปเดตอัตโนมัติ

### ดูข้อมูลล่าสุด
1. เปิด Dashboard
2. รอ 1-2 วินาทีให้โหลดจาก Sheets
3. จะ auto เลือกเดือนล่าสุดให้อัตโนมัติ
