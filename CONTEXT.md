# 📋 SURU Dashboard — Project Context (อัปเดตจากเว็บ live จริง 17 มิ.ย. 2569)

> อัปโหลดไฟล์นี้เปิดแชตใหม่กับ Claude เพื่อให้จำโปรเจกต์ได้ทันที
> **เวอร์ชันนี้ Claude ไล่ดูเว็บ live จริงผ่านเบราว์เซอร์แล้ว** (ไม่ได้เชื่อ CONTEXT เก่าอย่างเดียว) — โค้ด live ปัจจุบัน ~8,191 บรรทัด script / ~1.23 MB (โตจากเดิม ~5,670 บรรทัด แปลว่ามีงานเพิ่มหลายอย่างที่ CONTEXT รุ่นเก่ายังไม่ได้บันทึก)

---

## 🏪 ธุรกิจ
- **ร้าน:** SURU.SHOP / Suru Japan — บริษัท ซูรุ เอ็นเตอร์ไพรส์ จำกัด (ขายสินค้าญี่ปุ่น)
- **ช่องทางขาย:** Facebook, LINE Official, Shopee, Page365
- **เจ้าของ:** Nuda (น้องดา / nudanica@gmail.com) — ใช้ iPhone/Mac, สื่อสารภาษาไทย, ระบุสินค้าด้วย CF codes (เช่น F1, M33, C5)

---

## 🔗 URLs & IDs สำคัญ (คัดลอกตรงตัว ห้ามพิมพ์ใหม่จากความจำ)

| สิ่งที่ใช้ | ค่า |
|---|---|
| Dashboard (live) | https://nudanica.github.io/suru-dashboard/ |
| Converter | https://nudanica.github.io/suru-dashboard/converter.html |
| GitHub repo (PUBLIC) | https://github.com/nudanica/suru-dashboard (ไฟล์ index.html, branch main) |
| Apps Script (Dashboard Data) | https://script.google.com/macros/s/AKfycbxZE92cQiRmratXJhgdYea3ih__gBbpZRLzFb-sucPOHDJERsvl3F8ea0Xv_uLOfczu/exec |
| Dashboard Data Sheets ID | 1qAR5qjqv-a1lnxYMlwbL-xHixpuATn-8dIW3iLrpJDw (ลงท้าย ...iLrpJDw) |
| Master CF Sheets ID | 1BB4KXYhDRhmN7if9IVOuaoPJhzR9021GUiAJs5KwP9k (sheet "📦 Master CF", header row 4) |

> migrate จาก Netlify มา GitHub Pages หลังเครดิต deploy ฟรีหมด

### 🚀 วิธี deploy index.html (วิธีที่ user ต้องการให้ใช้ตลอด — อัปเดต 17 มิ.ย. 2569)
**Claude แก้ไฟล์ LIVE ในเบราว์เซอร์ + สั่งดาวน์โหลดไฟล์ลงเครื่อง user → user เอาไปอัป GitHub + Commit เอง**
1. fetch raw `raw.githubusercontent.com/.../main/index.html` (CORS เปิด) เข้ามาในเบราว์เซอร์ = ไฟล์ LIVE จริง
2. apply find/replace pairs ที่ **verify แล้วว่าแต่ละ pair เจอ 1 ตำแหน่งพอดี** (`split(old).length-1===1`) ลงบนไฟล์ LIVE จริง — **ไม่ใช่อัปโหลด working file/snapshot ทั้งไฟล์** (working file มักตามหลัง live ไม่กี่ร้อยตัวอักษร ถ้าทับทั้งไฟล์จะ revert งานที่อยู่บน live)
3. verify markers + ความยาวที่เพิ่ม (delta) ให้ตรงก่อน แล้วค่อยสั่ง `new Blob([patched]) → a.download='index.html' → a.click()` ให้ไฟล์ลง Downloads
4. user เอาไฟล์ใน Downloads ไปอัปทับใน GitHub repo → กด **Commit changes** เอง
- ❌ **อย่า**ยัดไฟล์เข้าฟอร์ม upload ของ GitHub ให้ (DataTransfer) · ❌ **อย่า**ส่งไฟล์ patcher แยกให้ · ❌ **อย่า**แนบไฟล์ ~980KB ในแชต (โดน content-filter)
- ส่ง pairs/โค้ดใหม่เข้าเบราว์เซอร์: ถ้าก้อนใหญ่ → **gzip+base64** (ฝั่ง page decode ด้วย `DecompressionStream('gzip')`) ลดขนาดให้ reproduce ได้แม่นในคำสั่งเดียว
- page context **ไม่รับ top-level await** → ห่อ `(async()=>{...; window.__X=...})()` แล้วอ่านผลจาก `window.__X` ใน call ถัดไป
- หลัง commit: ตรวจ live `fetch(pages/index.html)` ว่ามี markers + ความยาวตรง ก่อนสรุป (cache: Cmd+Shift+R)
- **ปัญหาเจอบ่อย:** user ยัง commit ไฟล์ล่าสุดไม่ทัน → เห็นของเก่า → **เช็คเสมอว่า commit ขึ้น Pages หรือยังก่อนสรุป**

### ⚠️ Apps Script — กฎติดตั้ง (ระบุ 3 อย่างทุกครั้งที่ให้โค้ดไปแก้)
1. **ไฟล์เจ้าของ = "SURU Dashboard Data"** เปิดผ่าน Extensions → Apps Script (ไม่ใช่ Master CF)
2. **ยืนยันถูกไฟล์:** const SHEET_ID ต้องลงท้าย ...iLrpJDw (ถ้า ...KwP9k = Master CF ผิดไฟล์)
3. **ระบุชื่อชีตปลายทาง** ที่ข้อมูลจะถูกเขียน/สร้าง
- **ห้ามสั่ง Ctrl+A วางทับ Apps Script บางส่วน** (เสี่ยงพังทั้งไฟล์)

---

## 🏛️ สถาปัตยกรรม (หลักการสำคัญที่สุด)
**Google Sheets คือความจริงชุดเดียว — ไม่ใช้ localStorage, ไม่ hardcode**

1. **Master CF** (Google Sheet + Apps Script v7) = ฐานข้อมูลสินค้า/ต้นทุน — ออกรหัส CF, เก็บราคาขาย/ต้นทุน/กำไร/GP%; Dashboard ดึง CF_COST + CF_NAMES real-time
2. **Dashboard** (single HTML บน GitHub Pages) — เปิดหน้า → loadFromSheets() ดึงทุกอย่างเข้า in-memory → render อ่านจากตัวแปร → save เขียนตรงเข้า Sheets; Converter แปลง Page365 CSV → SURU-format ก่อน upload


### 💰 รายได้ดึงจาก "Total" ไม่ใช่ "Paid Amount" (พิสูจน์แล้ว 14 มิ.ย. 2026)
**ระบบใช้คอลัมน์ Total เป็นยอดขายเสมอ** — ยืนยัน 3 ชั้น:
1. **โค้ด** parseSalesCSV + converter: `revenue = parseFloat(Total) || parseFloat(Paid Amount) || 0` → ใช้ Total ก่อน, Paid Amount เป็นแค่ fallback ตอน Total=0/ว่าง
2. **ไฟล์ปี 2026 เทียบยอด dashboard จริง:** Σ Total = ยอดที่ระบบเก็บ **เป๊ะทุกบาททั้ง 5 เดือน** (ม.ค.2,878,761/ก.พ.2,245,484/มี.ค.2,182,588/เม.ย.2,165,577/พ.ค.2,001,094) — Paid Amount สูงกว่าทุกเดือน ไม่ตรงสักเดือน
3. **Total = Subtotal − Discount + Shipping** ตรง 66,229/66,237 บิล

**⚠️ Paid Amount เชื่อถือไม่ได้ในไฟล์นี้** — มี ~920 บิลที่ Paid Amount=0/ไม่ครบ ทั้งที่ติดแท็ก "PAID โอนเงินแล้ว" (ไม่ใช่ COD; ทั้งร้าน COD แค่ 148 บิล) ถ้าเปลี่ยนไปใช้ Paid Amount ยอด 2 ปีจะ **หาย ฿1,018,810** (Σ Paid 84,751,227 vs Σ Total 85,770,037). **ห้ามเปลี่ยนมาใช้ Paid Amount**

**🐛 บิล Total=0 / Paid>0 → อัป "ดิบ" จะเพี้ยน ต้องอัปผ่าน converter เสมอ**
ไฟล์ดิบมีคอลัมน์ Paid Amount → บิล Total=0 จะ fallback ไปหยิบ Paid Amount → ยอดเกินจริง. ปี 2024-2025 เจอ 3 บิล (+฿3,576):
| Order | เดือน | เหตุผล (user ยืนยัน) | Paid |
|---|---|---|---|
| 141508 | ม.ค.2025 | ลูกค้าใช้เครดิตจ่าย ยอดเป็น 0 | 439 |
| 162224 | ส.ค.2025 | บิลเคลม ส่งใหม่ที่อยู่ผิด (ไม่ใช่ยอดขาย) | 1,017 |
| 167107 | ก.ย.2025 | ลูกค้าใช้เครดิตจ่าย ยอดเป็น 0 | 2,120 |
→ **อัปผ่าน converter.html เสมอ** (SURU-format ไม่มีคอลัมน์ Paid Amount → Total=0 fallback ไม่ได้ → เป็น 0 ถูกต้อง)

**✅ converter ตรวจแล้วถูกต้อง (14 มิ.ย. 2026):** รันโค้ด converter จริง (JS) เทียบ reference ทั้ง 6 ไฟล์ Page365 ปี 2024-2025 → **ยอด+จำนวนบิลตรงเป๊ะทุกไฟล์** (รวม 85,770,037 / 66,237 บิล) ต่างแค่ converter trim ช่องว่างนำหน้าใน Item Detail (สะอาดกว่า ไม่กระทบ CF code) → ใช้ converter อัปได้มั่นใจ


### ⚠️ สิ่งที่ทำให้ user หงุดหงิด (ต้องระวัง)
1. ใช้ hardcode (ห้ามเด็ดขาด)
2. จำผิดว่าทำอะไรเสร็จไปแล้ว
3. บอก "ถูกแล้ว/เสร็จ/ผ่าน 100%" โดยไม่ verify จริง
4. แก้โค้ดแล้ว user ยัง deploy ไม่ทัน → screenshot เก่า → **verify ผ่าน browser จริงเสมอ + เช็คว่า deploy ไฟล์ล่าสุดหรือยัง**
5. **ห้ามพูดว่า "งานวันนี้จะจบ"** — แก้ให้จบในวันเดียว
6. แก้ไม่ครบ/พลาดบั๊กแล้วบอกเสร็จ → user จับผิดได้ทุกครั้ง

### 🎨 เรื่อง UI — user ชอบแบบนี้
- **ทำ mockup ให้ดูก่อนลงโค้ดจริงเสมอ** (มักขอ "3-4 แบบ" หรือ "5-6 แบบ" ให้เลือก)
- ชอบ **Glassmorphism กระจกฝ้า** เป็นสไตล์ประจำ (rgba white + backdrop blur)
- คุมคำพูด/ตัดคำบรรยายเยอะๆ ออก ชอบสะอาดตา
- แก้ฝั่ง mobile ให้อยู่ใน @media(max-width:768px) เท่านั้น ห้ามแตะ desktop

### 🛑 กฎความรอบคอบ (บทเรียนจากความผิดพลาดจริง)
- **อย่าด่วนสรุปจากตัวเลขผิวเผิน** — เคยบอก "835 ตัวมีต้นทุนครบ 100%" เพราะเช็คแค่ cost>0 ทั้งที่ default ค่าส่ง 40 ทำให้ทุกตัวมี cost. **ที่ถูก = เช็ค (¥=0 AND ฿=0)**
- **เข้าใจ business logic ก่อนวิเคราะห์** — user รู้ระบบดีกว่า AI ถ้า user ทักว่า "ไม่น่าใช่" ให้กลับไปตรวจ data ดิบ
- **แยก "มีค่า" กับ "มีค่าที่ถูกต้อง"** — cost=40 = ค่าส่งล้วน ไม่ใช่ต้นทุนจริง
- **ฟีเจอร์ใหม่ต้องดึงจากตัวแปรที่มีอยู่จริง** (STOCK_DATA/CF_COST/MONTHLY_CUST) ไม่ตั้งชื่อใหม่มั่ว — เคยพลาดใช้ STOCK_ITEMS ที่ไม่มีจริง → ค่าออก "—"
- **globals อยู่ใน scope ปิด (IIFE)** อ่านผ่าน window.* ตรงๆ ไม่ได้ → ตอน verify ให้ดูจากค่าที่ render บน DOM หรือ screenshot แทน

---

## ✅ ระบบที่ทำเสร็จแล้ว + ขึ้น LIVE แล้ว (ยืนยันจากเบราว์เซอร์จริง 14 มิ.ย.)

### โครงสร้างเมนู (sidebar / mobile bottom-nav)
Dashboard · ช่องทางการขาย · ลูกค้า · Top สินค้า · รายรับรายจ่าย · อัปโหลดยอดขาย · อัปโหลดสต๊อก · อัปโหลดรายจ่าย · แปลงไฟล์ Page365
- pages (id): page-dashboard, page-channels, page-customers, page-products, page-expenses, recur-page, page-upload-sales, page-upload-stock, page-upload-ads

### หน้า Dashboard — KPI 2 แถว Glassmorphism (LIVE ✅)
- **แถว 1 P&L:** รายได้รวม / ต้นทุนสินค้า COGS / รายจ่ายรวม / Net Profit — label "P&L งบกำไรขาดทุน"
- **แถว 2 ตัวชี้วัด:** บิล-ชิ้น / AOV / ROAS / **Inventory Cost** / **GP Margin** — label "ตัวชี้วัดธุรกิจ"
- ค่าที่เห็นจริงเดือน ก.พ. 2569: รายได้ ฿2,245,484 (1,863 บิล · 6,963 ชิ้น) · COGS ฿846,889 (37.7%, ขึ้น "ครบทุกรหัส ✓") · รายจ่าย ฿604,962 (26.9%) · Net Profit ฿793,632 (35.3% NP) · AOV ฿1,205 · ROAS 9.23x · Inventory Cost ฿335,859 (สต๊อก 3,722 ชิ้น, ไฟล์ล่าสุด) · GP Margin 62.3%
- ป้ายเปรียบเทียบเดือน (ชมพู VS ม่วง "เดือนก่อน") + ตารางเปรียบเทียบ Glassmorphism ขึ้นครบ

### มือถือ (LIVE ✅ — user ยืนยัน "แสดงผลได้ดีแล้ว")
- @media(max-width:768px): sidebar ซ่อน, bottom nav 5 ปุ่ม (ภาพรวม/ลูกค้า/สินค้า/รายรับจ่าย/เพิ่มเติม) + bottom sheet, KPI 2 คอลัมน์, ตาราง overflow-x
- apple-touch-icon: iPhone โชว์ 🇯🇵 เพราะ cache ไม่ใช่ไฟล์ผิด — แก้โดยลบ icon เดิม → เปิดใน Safari → Add to Home Screen (RESOLVED)

### 🆕 ระบบ "กรอกยอดผันแปร" (LIVE ✅ — แทนที่ปุ่ม "แก้ยอดภาษี" เดิมที่ไม่มีแล้ว)
ปุ่ม "✏️ กรอกยอดผันแปร (ภาษี/ค่าส่ง)" เปิด modal รวมยอดที่ไม่เท่ากันทุกเดือน แยก 3 แท็บตามความถี่ (ระบบคิดเฉพาะช่วงนั้น ไม่กระทบช่วงอื่น) → ปุ่ม "บันทึกขึ้น Sheets":
- **แท็บรายวัน:** หมวด "📮 ค่าส่งไปรษณีย์" + กล่องลากไฟล์ **EMS .xlsx** → ดึงค่าส่งรายวันอัตโนมัติ (กรอกราย 1–30 ของเดือน) + ปุ่ม "เพิ่มหมวดรายวัน"
- **แท็บรายเดือน:** หมวด "📁 ภาษี" (ตาราง 2 คอลัมน์ **ภ.พ.30 (VAT)** + **ภ.พ.36 (FB)** กรอกรายเดือน) · หมวด "📢 Line broadcast" · ปุ่ม "เพิ่มหมวดรายเดือน"
- **แท็บรายปี:** ยังว่าง (ปุ่ม "เพิ่มหมวดรายปี") — น่าจะใช้ลงค่าบัญชี ฿80,000/ปี ในอนาคต

### 🆕🆕 หมวดผันแปรเก็บลง Sheets + หน้าต่าง "⚙️ จัดการหมวด" (LIVE ✅ 17 มิ.ย. 2569) — *แก้บั๊กหมวดที่เพิ่มแล้วยังไม่ลงยอด หายตอนรีเฟรช/สลับเดือน*
**บั๊กเดิม:** รายชื่อหมวดผันแปรถูก derive จาก "แถวยอดที่เซฟ" เท่านั้น → เพิ่มหมวดใหม่แต่ยังไม่กรอกยอด พอรีเฟรช/สลับเดือน หมวดหาย (ไม่มีลิสต์หมวดที่ persist)
**แก้ (เก็บ "รายชื่อหมวด" ลง Sheets แยกจากยอด):**
- **ชีตใหม่ "หมวดผันแปร"** คอลัมน์ `ความถี่ | รายการ` (freq = daily/monthly/yearly) — สร้างอัตโนมัติตอน POST ครั้งแรก
- **Apps Script เพิ่ม `?type=varcats`** (deploy เวอร์ชันใหม่แล้ว ✅): doGet คืน `{status:'ok',data:[{ความถี่,รายการ}]}` · doPost (`type:'varcats'`) `clear()` แล้วเขียนทับทั้งลิสต์ (upsert ทั้งก้อน) — วาง 2 branch แบบ **early-return บนสุดของ doGet/doPost** (self-contained ไม่พึ่ง SHEET_MAP/jsonResponse เดิม ไม่ยุ่ง logic อื่น)
- **index.html:** `persistVarCats()` POST ทั้งลิสต์ทุกครั้งที่ add/rename/delete · loadFromSheets เพิ่ม fetch `?type=varcats` (fetch ตัวที่ 13 ใน Promise.all) + restore เข้า DAILY_ITEMS/VARIABLE_ITEMS/YEARLY_ITEMS (monthly เซ็ต VARIABLE_FREQ='monthly') · log "✅ Restored varcats: N หมวด" · ถ้า endpoint ยังไม่มี → ข้ามแบบ graceful ไม่ error
- **หน้าต่าง "⚙️ จัดการหมวด"** (`#var-catmgr` กระจกฝ้า) เปิดจากปุ่มข้าง "+ เพิ่มหมวด": add/rename/delete รวมที่เดียว (input onchange=`vcRename` + 🗑️ `vcDelete` + แถวล่างเพิ่ม `vcAdd`) → **ยกเลิกปุ่ม ✏️🗑️ ติดบน chip เดิม** (chip สะอาดขึ้น) · core `applyRenameVarItem`/`applyDeleteVarItem` ย้ายยอดทุกเดือน+persist+re-render, กันชื่อซ้ำ/กันแตะ protected
- **หมวดระบบล็อก `VAR_PROTECTED_ITEMS = ['tax','ค่าส่งไปรษณีย์']`** — แก้ชื่อ/ลบไม่ได้ เพราะผูกกับ **คำนวณภาษีอัตโนมัติ (ภาษี)** + **ดึงไฟล์ EMS รายวัน (ค่าส่งไปรษณีย์)** → ไม่โผล่ในหน้าจัดการ (ไม่ใช่ตั้งใจล็อกเฉย ๆ — เป็นการกันลบแล้วระบบพัง) · ตัวอื่น (Line broadcast/ค่าไฟ/ค่าน้ำ) แก้/ลบได้ปกติ
- **verify ครบวงจรบน live 17 มิ.ย.:** เพิ่มหมวด (ไม่ลงยอด) → เซฟลงชีตทันที → **รีเฟรชเต็มหน้า → หมวดยังอยู่** ✅ → ลบ → หายทั้งจอ+จากชีต ✅ · ลิสต์ในเพจ = ชีตเป๊ะ (daily: ค่าส่งไปรษณีย์ · monthly: Line broadcast, ⚡️ค่าไฟ, 💧ค่าน้ำ · yearly: [])
- **path UI:** รายรับรายจ่าย → การ์ด "รายจ่ายประจำ" จัดการ → การ์ด "กรอกยอดผันแปร" → ปุ่ม "⚙️ จัดการหมวด" (สีฟ้า cyan ข้างปุ่มเพิ่มหมวด)

### 🆕 ภาษี ภ.พ.30 + ภ.พ.36 — กรอกแล้ว (LIVE ✅) — *เดิมเป็น todo "รอ user ส่งยอด ภ.พ.36" ตอนนี้ทำแล้ว*
ค่าที่อ่านจากตารางผันแปรจริง (รายเดือน):
| เดือน | ภ.พ.30 (VAT) | ภ.พ.36 (FB) |
|---|---|---|
| พ.ย. 2568 | 21,375.97 | 0 |
| ธ.ค. 2568 | 20,289.57 | 20,562.04 |
| ม.ค. 2569 | 20,754.56 | 25,936.14 |
| ก.พ. 2569 | 20,338.26 | 17,220.68 |
| มี.ค. 2569 | 25,591.69 | 21,247.78 |
| เม.ย. 2569 | 20,852.53 | 12,401.91 |
| พ.ค. 2569 | 0 (รอ) | 16,166.93 |
| มิ.ย. 2569 | 0 (รอ) | 0 (รอ) |
> เดือนเก่ากว่า พ.ย.68 (พ.ค.–ต.ค. 2568) มี ภ.พ.30 อยู่แล้วตามชุดเดิม 14 เดือน · ที่ยังว่าง = พ.ค./มิ.ย. 2569 (VAT) + มิ.ย. 2569 (FB)

### 🆕 หน้า "ช่องทางการขาย" (page-channels, LIVE ✅) — *แก้ todo เดิม "hardcode ค่าแอด channels"*
- "📊 ยอดขายแยกช่องทาง" + "📢 ค่าโฆษณาแยกบัญชี" (SURU ตัวหลัก / SURU ทดลอง2 / รวม 2 บัญชี: ค่าแอด ฿ + จำนวนครั้ง)
- **หมายเหตุในหน้า:** ยอดขาย/ROAS แยกรายบัญชีไม่ได้ เพราะยอดขายไม่ได้ระบุว่ามาจากแอดบัญชีไหน → ให้ดู ROAS รวมที่การ์ด ROAS

### 🆕 หน้าอัปโหลด 3 แบบ (LIVE ✅)
- **อัปโหลดยอดขาย** (page-upload-sales) — Page365 CSV
- **อัปโหลดสต๊อก** (page-upload-stock) — สินค้า.csv replace-all → Inventory Cost KPI อัปเดตตามไฟล์ล่าสุด
- **อัปโหลดรายจ่าย** (page-upload-ads) — มี 3 แท็บ: **รูปภาพ (exp-tab-image)** / **xlsx (exp-tab-xlsx)** / **csv (exp-tab-csv)** + พรีวิว (exp-xlsx-preview, upload-xlsx-preview)

### 🆕 หน้าลูกค้า (page-customers, LIVE ✅)
- การ์ดแบ่งลูกค้า: ใหม่ / กลับมาซื้อ 1 ครั้ง / ประจำ + AOV
- **"👑 Top ลูกค้า"** ยอดซื้อสูงสุด (Top 10/20/30/50/100 — ชื่อ/บิล/ยอด)
- มีตัวเลือกเปรียบเทียบในหน้า: รายวัน/รายสัปดาห์/รายเดือน/รายปี + "เลือกเปรียบเทียบ" (เดือนย้อนหลัง/ปีย้อนหลัง/เลือกเดือน)
- MONTHLY_CUST keyed phone — **เดิม**ฝังตายในไฟล์ มีแค่ 5 เดือน (2026-01..05) → **ตอนนี้โหลดเพิ่มจาก Sheets ได้แล้ว** (ดูระบบ custmonth ด้านล่าง)

### 🆕🆕 ระบบลูกค้ารายเดือน "custmonth" (ติดตั้ง+deploy แล้ว ✅ 14 มิ.ย. รอบเย็น) — *แก้บั๊กลูกค้าใหม่/เก่า*
**บั๊กเดิม:** เลือก ม.ค. 2569 หน้าลูกค้าขึ้น "ลูกค้าใหม่ 100%" เพราะ logic ดูว่า "เก่า" จาก MONTHLY_CUST ที่**ฝังตายในไฟล์ มีแค่ 5 เดือน 2026** → ไม่มีประวัติปีก่อนให้เทียบ
**ดีไซน์ (ทาง A):** เก็บลูกค้ารายเดือนครบทุกเดือนลง Sheets → โหลดกลับเข้า MONTHLY_CUST ตอนเปิดเว็บ → **logic แสดงผล/แยกใหม่-เก่าเดิมไม่ต้องแก้** แตะ 3 จุด:
- **(a) index.html** (deploy แล้ว ✅): แก้ `const MONTHLY_CUST = window.__SURU_CUST = {...}` (เปิด global ให้เข้าถึง) + เพิ่ม `<script>` ก่อน `</body>`:
  - `buildYears()` — สร้าง dropdown ปี **2567 → ปีปัจจุบัน+3 (พ.ศ.)** ทุกช่อง (year-sel/c-year-sel/p-year-sel/e-year-sel) แทนของเดิมที่ค้าง 2568-2572 (verify บน live: ขึ้น 2567-2572 ครบ ✅)
  - `fillCustFromSheets()` — GET `?type=custmonth` โหลดลูกค้าเข้า MONTHLY_CUST แล้ว renderCustomers()
  - **ห่อ `loadSalesFile`** — ตอนอัปไฟล์ยอดขาย จะ aggregate ลูกค้าต่อ (เดือน,เบอร์) [นับ orders/revenue/items จาก Item Detail xN] → POST `custmonth` ขึ้น Sheets อัตโนมัติ (verify: loadSalesWrapped=true บน live ✅)
- **(b) Apps Script** (deploy เวอร์ชันใหม่แล้ว ✅ — Manage deployments → Edit → New version, URL เดิมไม่เปลี่ยน): เพิ่ม `SHEET_MAP['custmonth']='ลูกค้า_รายเดือน'` + `doGet` branch คืน array {month,phone,name,revenue,orders,items} + `doBackup` dispatch `custmonth`→`backupCustMonth(sheet,data,months)` (upsert ตามเดือน: ลบเฉพาะเดือนที่อัปใหม่แล้วเขียนทับ — เลียนแบบ backupNoCharge) — **verify: ยิง `?type=custmonth` คืน `[]` HTTP 200 ✅**
- **(c) converter.html**: ตรวจซอร์ส live แล้ว — **มี Customer Name อยู่แล้วครบ** (OUTPUT_COLS `{key:'customer_name',label:'Customer Name'}` + แถวดึง `customer_name: COL.name>=0?...`) → **ไม่ต้องแก้/อัปใหม่**
- **ชีตปลายทาง "ลูกค้า_รายเดือน"** คอลัมน์: เดือน | เบอร์ | ชื่อ | ยอดซื้อ | จำนวนบิล | จำนวนชิ้น (สร้างอัตโนมัติตอน POST ครั้งแรก ยังไม่มีตอนนี้)
- entry MONTHLY_CUST: `{phone, name(มี "(บิลXXXX)"), revenue, orders, items, aov, is_repeat}`; logic ใหม่/เก่า: key=phone||name, seenBefore=เบอร์ที่ปรากฏในเดือน < เดือนแรกของช่วงเลือก

### ระบบรายจ่าย (รายรับรายจ่าย + recur-page)
- รายจ่ายประจำ effective-dated (Sheets แท็บ รายจ่ายประจำ, type=recurring): แก้อนาคต=เพิ่มแถว, หยุด=ใส่ to, ลบ=ลบแถว; gen วันที่ 25; ปุ่ม "🔁 รายจ่ายประจำ (จัดการ + กรอกยอด)"
- DEFAULT ฐาน ~฿301,500/เดือน: เงินเดือน อ้อม/มายด์ 16000, เดียร์/ฟ้า 14000, รัตน์ 18500, นิค 15500; ผลตอบแทน บอล 25000, นุ๊ก/ป๊า/หนูดา 46000; ค่าเช่า 22000, ค่าตำรวจ 9000, รถ Ford 10000, ไฟ 1500, เน็ต 1500, น้ำ 500
- แก้/ลบ inline (✏️🗑️); ค่าแอดป้าย "อัตโนมัติ" แก้ไม่ได้; ค่าบัญชี ฿80,000/ปี user ลงเองหมวด 📓
- หน้ารายรับรายจ่าย: gradient hero + bars + PDF export

### ค่าแอด Meta 2 บัญชี
- SURU ตัวหลัก ฿1,167,638.88 (467992889148514) · SURU ทดลอง2 ฿144,081.39 (1021912629261041)

### Apps Script Dashboard Data
- doGet ดึงทุกประเภท (cf_cost, cf_names, stock, tax); doPost parse postData.contents → doBackup(ss,type,data,months); backupSales เก็บ COGS; backupTax + initTaxData
- **โครงสร้างจริง (อ่านผ่าน monaco 14 มิ.ย.):** header comment "v13" · ฟังก์ชัน: doGet, doPost, doBackup, backupSales/Expenses/Ads/Stock/NoCharge/Tax/Recurring/MonthlyVariable/DailyVariable, ensureHeaders, appendRows, jsonResponse, getExistingKeys ฯลฯ · `SHEET_MAP[type]`→ชื่อชีต (สร้างใหม่ถ้าไม่มี) · POST {type,data,months} · `backupNoCharge`/`backupMonthlyVariable` = แม่แบบ upsert ตามเดือน
- **🆕 เพิ่ม custmonth แล้ว (deploy ✅):** SHEET_MAP custmonth, doGet ?type=custmonth คืน array, doBackup→backupCustMonth (upsert ตามเดือน) — ไฟล์ ~28KB→30KB, ผ่าน syntax check, ไม่แตะ logic เดิม
- **🆕 เพิ่ม varcats แล้ว (deploy ✅ 17 มิ.ย. 2569):** 2 early-return branch บนสุดของ doGet/doPost — GET `?type=varcats` คืนลิสต์หมวดจากชีต "หมวดผันแปร", POST `type:'varcats'` เขียนทับทั้งลิสต์ (`clear()`+`setValues`) — self-contained ไม่พึ่ง SHEET_MAP/jsonResponse เดิม (วางเป็น statement แรกของฟังก์ชัน ไม่ใช่ต่อท้าย catch)
- **วิธีอ่าน/แก้ Apps Script ผ่านเบราว์เซอร์:** อ่านซอร์ส = `monaco.editor.getModels()[0].getValue()` (return ตรงๆ โดน content-filter บล็อก → sanitize: แทน token ยาว/ตัวเลข/`=`) · แก้ = `setValue(newSrc)` + คลิกในโค้ด + Cmd+S (ขึ้น "Saved to Drive") · **deploy = Deploy → Manage deployments → ดินสอ Edit → Version dropdown (คลิกกลางช่อง ไม่ใช่ caret, อย่า scroll เดี๋ยวปิด) → "New version" → Deploy** (อัปเวอร์ชัน deployment เดิม URL ไม่เปลี่ยน — **ห้าม New deployment** URL จะเปลี่ยน)

---

## 🗂️ โครงสร้างข้อมูล

### ⭐ ความจริงเรื่องข้อมูลขาย (สำคัญสำหรับฟีเจอร์เทียบ)
- **เดิมมีข้อมูลยอดขายจริงแค่ 5 เดือน: ม.ค.–พ.ค. 2569 (2026-01..05)**
- **🆕 14 มิ.ย. 2026: กำลังเพิ่มย้อนหลัง 24 เดือน ปี 2567-2568 (2024-2025) ผ่าน converter** → รวมเป็น ~29 เดือน. ยอดที่ถูกต้อง (จาก Total): **2024 = ฿45,153,857 / 32,879 บิล · 2025 = ฿40,616,180 / 33,358 บิล · รวม 2 ปี ฿85,770,037 / 66,237 บิล** (ไฟล์แปลง + สรุป xlsx อยู่ใน outputs)
- **🆕 14 มิ.ย. (เย็น): สร้างไฟล์แปลง SURU-format ที่มี Customer Name ครบ 7 ไฟล์ (2567-2569) อยู่ใน outputs** — `SURU-แปลง_2567_ม.ค.-มิ.ย / ก.ค.-ธ.ค · 2568_ม.ค.-มี.ค / เม.ย.-มิ.ย / ก.ค.-ก.ย / ต.ค.-ธ.ค · 2569_ม.ค.-พ.ค` · ยอดบิลรวม **75,583** (2567:32,879/2568:33,358/2569:9,346) ตรง verify · **รอ user อัปเข้า "อัปโหลดยอดขาย" ทีละไฟล์** → ลูกค้าเข้าชีต custmonth อัตโนมัติ (ยอดขายซ้ำ skip by Order no. ไม่นับซ้ำ) · แปลงจากดิบ Page365: คอลัมน์ Order no.=3, Paid At=22 (รูป `2025-01-01 06:04:00 +0700`), Total=20, Item Detail=24, Customer Phone=9 (ตัด `'` นำหน้า), Customer Name=8, Source=1; output TAB-sep + UTF-8 BOM + quote ฟิลด์ที่มี \t/\n/"
- ปฏิทินเลือกได้ถึง 2030 แต่เดือนที่ไม่มีข้อมูลคืน null
- ยอด 2569: ม.ค. ฿2,878,761 · ก.พ. ฿2,245,484 · มี.ค. ฿2,182,588 · เม.ย. ฿2,165,577 · พ.ค. ฿2,001,094
- (ข้อมูลภาษีย้อนได้ถึง 2568 เพราะกรอกมือ แต่ "ยอดขาย" จริงมี 5 เดือน)

### Master CF: A รหัส CF / B ชื่อ / C ตัวเลือก / D ราคาขาย฿ / E ราคา¥ / F ต้นทุน฿ / G เรท / H ค่าส่ง / I ต้นทุน/ชิ้น(formula) / J กำไร / K GP% / L หมายเหตุ
- formula I: =IF(F>0, F+IF(H>0,H,0), IF(AND(E>0,G>0), E*G+H, IF(H>0,H,"")))

### Dashboard Data Sheets (แท็บ): ยอดขาย_รายวัน · ยอดขาย_สรุปเดือน · รายจ่าย · ค่าแอด · สต๊อก · ภาษี_รายเดือน · รายจ่ายประจำ · ลูกค้า_รายเดือน · **หมวดผันแปร (ความถี่|รายการ — รายชื่อหมวดผันแปร)** · (แท็บยอดผันแปรราย วัน/เดือน เก็บ "ยอด" แยกอีกชุด)
- SHEET_MAP: daily/sales→ยอดขาย_รายวัน · monthly→สรุปเดือน · expenses→รายจ่าย · ads→ค่าแอด · stock→สต๊อก · nocharge→บิลไม่เก็บเงิน · tax→ภาษี_รายเดือน · recurring→รายจ่ายประจำ; cf_names/cf_cost อ่านจาก Master CF (slice(3), regex /^[A-Za-z]{1,3}[0-9]/)

### CATS หมวดรายจ่าย: 💰ค่าแอด FACEBOOK · 📦ค่าส่ง EMS · 🚗ค่าส่ง Lalamove · 🛒ค่าอุปกรณ์ · 💡ผลตอบแทน · 💩รับผิดชอบ · 👤เงินเดือน · 🏠ค่าเช่า · 🗂️ค่าภาษี · 📓ค่าบัญชี · 🥷🏽ค่าตำรวจ · 🤖ค่าระบบ · 🔧ค่าอื่นๆ

### CSS root colors: --pk:#FF6BAE/--pkd:#D94E8F · --cy/--cyd:#1A7BA8 · --am:#FF8C00 · --og:#FF7A45/--ogd:#D14D1F · --pu:#9B7FE8/--pud:#5C3DBF · --gn:#22C58B/--gnd:#0F8A5F · --yl/--yld:#B8901A · --tl:#00BCD4/--tld:#00838F(teal) · --rd:#FF5A5A; B()='฿'+Math.round(n).toLocaleString('th-TH')

---

## 🔴 สิ่งที่ต้องทำต่อ / กำลังรอ (เรียงตามสำคัญ — อัปเดตจากความจริง)

1. **⭐ ต้นทุนสินค้า (COGS) — ยืนยันความครบถ้วน:** เดือน ก.พ. 2569 บนเว็บขึ้น "ครบทุกรหัส ✓" แล้ว (user น่าจะกรอกต้นทุนเพิ่ม) — **แต่ Claude เห็นแค่ ก.พ. เดือนเดียว ยังไม่ได้ไล่ทุกเดือน/ภาพรวม Master CF** → รอบหน้าควรเช็คทุกเดือน 2569 ด้วยเกณฑ์ **(¥=0 AND ฿=0)** ไม่ใช่ cost>0. **DECISION เดิม: เริ่มถูกตั้งแต่ 2026 ไม่ย้อนแก้; user กรอกเองคอลัมน์ F, Claude ห้ามแตะ Master CF**
2. **กรอกยอดผันแปรเดือนปัจจุบันให้ครบ:** ภ.พ.30 พ.ค./มิ.ย. 2569 + ภ.พ.36 มิ.ย. 2569 ยังเป็น 0 · ค่าส่ง EMS รายวันต้องอัปโหลดไฟล์ EMS .xlsx ของเดือนใหม่เรื่อยๆ (มีไฟล์ "EMSสถิติ ม.ค.–พ.ค. 69")
3. **parser อ่านแค่ "CF code" ไม่อ่าน "SKU":** Page365 มี 2 คอลัมน์แยก; loadStockFile อ่านแค่ cfIdx. SKU P00009 ≠ CF P9 (คนละระบบ); user จะลบ P00xxx แล้วใส่ CF code ใหม่; ~51 ตัวไม่มี CF code → **ยังไม่ยืนยันว่าแก้แล้วหรือยัง ควรเช็ค**
4. **COGS อดีต = ตัวเลขตายตัว** ไม่ย้อนอัปเดต (user รับได้ เริ่มนับใหม่ 2026)
5. **⭐ ลูกค้าใหม่/เก่า เทียบข้ามปี — ระบบพร้อมแล้ว รอเติมข้อมูล:** ติดตั้ง custmonth (index+Apps Script deploy ✅) + ไฟล์แปลงมี Customer Name 7 ไฟล์พร้อมใน outputs. **ขั้นถัดไป (รอ user อัป):** อัป 7 ไฟล์เข้า "อัปโหลดยอดขาย" ทีละไฟล์ → **แล้ว Claude verify: (1) ชีต "ลูกค้า_รายเดือน" มีข้อมูลขึ้นจริง (2) `?type=custmonth` คืน rows (3) หน้าลูกค้า ม.ค. 2569 แยกใหม่/เก่าถูกต้อง + ชื่อ/เบอร์/Top ขึ้น**
6. **ยอดต่าง Dashboard vs Page365** — ปี 2024-2025 เจาะแล้ว = 3 บิล Total=0/Paid>0 (เครดิต/เคลม) เกิดจากอัปดิบ fallback ไป Paid Amount → แก้โดยอัปผ่าน converter (ดูหัวข้อ "รายได้ดึงจาก Total")
7. **Phase-2 daily notes** persist เป็นคอลัมน์ใน Sheet (โค้ดมีร่องรอย note อยู่ — ควรเช็คสถานะ)
8. **แท็บผันแปร "รายปี"** ยังว่าง — อนาคตอาจย้ายค่าบัญชี ฿80,000/ปี มาลงตรงนี้

---

## ⚠️ ปัญหา/ข้อจำกัดที่รู้อยู่
- COGS เป็นประมาณการในเดือน/ตัวที่ยังไม่กรอกทุนจริง → กำไรอาจสูงเกินจริง (ก.พ. ขึ้นครบแล้ว เดือนอื่นต้องเช็ค)
- parser อาจยังไม่อ่าน SKU (ต้องยืนยัน)
- Backup GET จำกัด ~20 rows/request (บางจุดใช้ POST)
- **Claude-in-Chrome:** timeout บ่อย (เคยค้าง document_idle ตอนเปลี่ยนหน้า) — กู้ด้วย tabs_context_mcp(createIfEmpty:true) / wait แล้วลองใหม่ / ใช้ javascript_tool แทน screenshot; tabId เปลี่ยนทุกครั้ง
- **Browser content-filter บล็อก** การ return ค่าที่เป็นวันที่/SHEET_ID/base64 ("BLOCKED: Cookie/query string data") → ตอน verify ให้ mask ตัวเลข (เช่นแทนด้วย #) หรือดูผ่าน screenshot
- **globals (SURU_DATA/CF_COST/STOCK_DATA ฯลฯ) อยู่ใน scope ปิด** อ่าน window.* ไม่ได้ → verify จาก DOM/screenshot

---

## 🔧 วิธีใช้งานปกติ
- **ยอดขาย:** Export CSV Page365 → หน้าอัปโหลดยอดขาย
- **ต้นทุน:** เพิ่มใหม่ Google Form ADD NEW / กรอกตัวเดิมคอลัมน์ F เอง → ใส่ CF code ใน Page365 → Refresh
- **สต๊อก:** Export สินค้า.csv (replace-all) → อัปโหลดสต๊อก → Inventory Cost KPI อัปเดตตามไฟล์ล่าสุด
- **รายจ่าย:** หน้าอัปโหลดรายจ่าย (รูป/xlsx/csv) หรือเพิ่มมือ
- **ยอดผันแปร (ภาษี/ค่าส่ง/Line):** ปุ่ม "กรอกยอดผันแปร" → เลือกแท็บ/เดือน → กรอกหรือลากไฟล์ EMS → บันทึกขึ้น Sheets
- **ดูข้อมูล:** ทุกหน้า default = วันนี้; เทียบช่วง: จิ้มป้ายม่วง → เลือกเดือน → ตารางขึ้น

---

## 🛠️ Technical Notes
- โค้ด live ปัจจุบัน ~8,191 บรรทัด script — **เลขบรรทัดใน CONTEXT รุ่นก่อน (~5,670) ใช้อ้างอิงตำแหน่งไม่ได้แล้ว ต้องค้นด้วยชื่อฟังก์ชัน/คลาสแทน**
- ไฟล์ทำงาน: /home/claude/index.html (ดึงจาก repo) · /mnt/user-data/outputs/index.html (ส่ง deploy)
- syntax check: extract <script> → node --check (container network ปิด แต่ node/python3 ในเครื่องใช้ได้)
- verify CSS ไม่ต้อง deploy: inject <style> ผ่าน javascript_tool ดูผลทันที
- **🆕 ส่งไฟล์ index.html ให้ deploy (วิธีที่เวิร์คจริง):** แนบไฟล์ ~940KB ในแชต**ไม่ได้** (ดึง live กลับเข้าแชต/return โดน filter) · browser download ไป Downloads แต่ user มักหาไม่เจอ → **วิธีที่ใช้ได้: เปิด GitHub `/upload/main` → build ไฟล์ในเบราว์เซอร์ (fetch raw + แก้) → `new File([fixed],'index.html')` ใส่ `input.manual-file-chooser` ผ่าน DataTransfer + dispatch change → ไฟล์ขึ้น staged → user กด "Commit changes" เอง** (Claude ไม่กด commit/deploy เอง = แก้ public repo/backend ต้องให้ user ยืนยัน)
- **🆕 บทเรียน build index (off-by-N):** อย่าคำนวณตำแหน่งแทรก `<script>` (lastIndexOf `</body>`) **ก่อน** `.replace()` ที่เพิ่มอักษร — index จะเลื่อน (เคยเลื่อน 21 ตัวจาก `window.__SURU_CUST = `) → แทรกกลาง `</div>` → browser ไม่รันสคริปต์. **ต้องคำนวณตำแหน่งหลัง replace** · verify โครงสร้างท้ายไฟล์: ระหว่าง `</script>` กับ `</body>` ต้องมีแค่ whitespace, ไม่มีเศษ tag (`</`+`div>`) · ซ่อมไฟล์พังได้: `broken.split(INJECT).join('')` เอาบล็อกผิดออกแล้วแทรกใหม่
- fetch `raw.githubusercontent.com/.../main/index.html` จาก page context ได้ (CORS เปิด) — ใช้ดึงไฟล์ live ปัจจุบันมา build/verify (web_fetch จาก sandbox โดนบล็อก, bash network ปิด)

---

_อัปเดต 14 มิ.ย. 2026 — Claude ไล่ดูเว็บ live จริง: งานชุด 12–13 มิ.ย. (มือถือ + KPI 2 แถว + Inventory Cost/GP Margin + ตารางเทียบ) **deploy ขึ้น live ครบแล้ว** · พบงานเพิ่มที่ CONTEXT เก่ายังไม่บันทึก: ระบบ "กรอกยอดผันแปร" (รายวัน ค่าส่ง EMS / รายเดือน ภาษี ภ.พ.30+ภ.พ.36 + Line broadcast / รายปี) → ภ.พ.36 กรอกเกือบครบแล้ว · หน้าช่องทางการขาย · หน้าอัปโหลดรายจ่าย (รูป/xlsx/csv) · Top ลูกค้า. ถัดไป: ยืนยัน COGS ครบทุกเดือน 2569 + กรอกผันแปรเดือนปัจจุบัน + เช็ค parser SKU + เช็ค Apps Script version จริง_

_เพิ่มเติม 14 มิ.ย. 2026 (รอบบ่าย): พิสูจน์ว่ารายได้ใช้คอลัมน์ Total ไม่ใช่ Paid Amount (เทียบไฟล์ 2026 ตรง dashboard ทุกบาท) · Paid Amount มีช่องว่าง ~920 บิล (อย่าใช้) · ตรวจ converter ถูกต้องทั้ง 6 ไฟล์ · เตรียมไฟล์แปลง 2024-2025 + สรุปรายเดือน xlsx เพื่ออัปย้อนหลัง — user จะแปลงเองผ่าน converter เพื่อ test ระบบ_

_เพิ่มเติม 14 มิ.ย. 2026 (รอบเย็น — verify ผ่าน https://nudanica.github.io/suru-dashboard/ จริงทั้งหมด): **แก้บั๊กลูกค้าใหม่/เก่า** ด้วยระบบ custmonth — (1) index.html deploy แล้ว (ปี 2567-2572 ทุก dropdown ✅, loadSalesFile ถูกห่อ ✅, window.__SURU_CUST ✅) ผ่านการอัปไฟล์ขึ้น GitHub โดยตรง (build ในเบราว์เซอร์ + DataTransfer + user commit) (2) Apps Script เพิ่ม custmonth + deploy เวอร์ชันใหม่ (URL เดิม) — `?type=custmonth` คืน `[]` HTTP 200 ✅ (3) ยืนยัน converter live มี Customer Name อยู่แล้ว ไม่ต้องแก้ (4) สร้างไฟล์แปลงมีชื่อลูกค้า 7 ไฟล์ 2567-2569 (75,583 บิล) ส่งให้ user แล้ว. **ค้างรอ user อัป 7 ไฟล์ → แล้ว verify ชีต "ลูกค้า_รายเดือน" + หน้าลูกค้า ม.ค. 2569 แยกใหม่/เก่า + ชื่อ/เบอร์/Top.** บทเรียน: build index ต้องคำนวณตำแหน่งแทรกหลัง .replace (off-by-21 ทำให้สคริปต์ไม่รัน) · ส่งไฟล์ใหญ่ให้ deploy = อัปขึ้น GitHub ผ่านเบราว์เซอร์ ไม่ใช่แนบแชต_

_เพิ่มเติม 14 มิ.ย. 2026 (รอบเย็น #2 — ตรวจ live ละเอียดทุกแง่มุม):_

**(A) ระบบลูกค้า custmonth เสร็จสมบูรณ์ ✅** — user อัปไฟล์ครบ 7 ไฟล์ → ชีต "ลูกค้า_รายเดือน" มีครบ **29 เดือน (58,274 แถว)**. ผลหน้าลูกค้า (ประวัติครบ): ม.ค. 2569 = ใหม่ 29% / กลับมาซื้อ 71% · พ.ค. 2569 = ใหม่ 22% / เก่า 78% (ไม่ใช่ "ใหม่ 100%" แล้ว).
- **บั๊กที่เจอ+แก้ระหว่างทาง:** คอลัมน์ "เดือน" ถูก Google Sheets แปลงเป็น "วันที่" ตอนเขียน (เช่น "Fri Aug 01 2025...") → dashboard เช็ค regex `\d{4}-\d{2}` ไม่ผ่าน เลยทิ้งข้อมูลทั้งหมด. **แก้: เพิ่มฟังก์ชัน `toMonthStr_(v)` ใน Apps Script** (ถ้าเป็น Date → "YYYY-MM", ไม่งั้น slice/parse) ใช้ทั้ง `doGet` (อ่าน) และ `backupCustMonth` (เขียน: normalize kept rows + `setNumberFormat('@')` คอลัมน์เดือนเป็นข้อความ) — **deploy เวอร์ชันใหม่แล้ว** `?type=custmonth` คืน YYYY-MM ครบ 29 เดือน ✅
- **บทเรียน:** อัปหลายไฟล์ติดกันเร็วเกินไป → POST custmonth เขียนทับกัน (race condition) ทำให้บางเดือนหลุด (เคยหาย 6 เดือน) → **ต้องอัปทีละไฟล์ รอให้เสร็จก่อนค่อยอัปไฟล์ถัดไป**

**(B) 🔴 บั๊กค้าง (ยังไม่แก้ — ทำในแชตใหม่): การ์ด "ลูกค้าทั้งหมด" แสดง 0 สำหรับทุกเดือนนอก ม.ค.-พ.ค. 2569**
- **ไฟล์/จุด:** `index.html` → ฟังก์ชัน `renderCustomers()` (อยู่ใน main script ไม่ใช่สคริปต์เสริม custmonth)
- **สาเหตุ:** การ์ด "ลูกค้าทั้งหมด" (`id=ret-unique`) นับ `realTotal` จาก `MONTHLY_CUST_COUNT` (ค่าฝังตาย มีแค่ 5 คีย์ `2026-01..05`) แทนที่จะนับจาก `MONTHLY_CUST` (29 เดือนที่โหลดจาก Sheets) → เดือนอื่นได้ 0
- **โค้ดปัจจุบัน:** `const realTotal = activeMonths.reduce((s,mo)=> s + (typeof MONTHLY_CUST_COUNT!=='undefined' ? (MONTHLY_CUST_COUNT[mo]||0) : (MONTHLY_CUST[mo]||[]).length), 0);`
- **แก้เป็น:** `const realTotal = activeMonths.reduce((s,mo)=> s + ((MONTHLY_CUST[mo]||[]).length), 0);` (ใช้ MONTHLY_CUST เป็นแหล่งเดียว — ตอนนี้มันมีครบ 29 เดือนจาก Sheets แล้ว)
- **หลักฐาน:** ธ.ค. 2568 → ตาราง Top โชว์ลูกค้าจริง (คุณจเรศ เสมาทอง ฿56,616 ⭐ประจำ) แต่การ์ดรวม = 0; `MONTHLY_CUST['2025-12']` = 1,762 คน; `MONTHLY_CUST_COUNT` keys = `[2026-01,02,03,04,05]` เท่านั้น
- **ผลทดสอบทุกโหมด:** ม.ค.2569=1,880 ✓ / ก.พ.=1,476 ✓ / พ.ค.=1,342 ✓ (ถูกเพราะอยู่ใน 5 เดือน hardcoded) — **ธ.ค.2568=0 ✗ / ทั้งปี 2568=0 ✗** (ทั้งที่มีข้อมูล). Top table + %ใหม่/เก่า ใช้ `MONTHLY_CUST` จึงถูกอยู่แล้ว — กระทบแค่การ์ดยอดรวมใบเดียว
- **วิธี deploy แก้:** แก้ index.html → อัปขึ้น GitHub ผ่านเบราว์เซอร์ (build ในเบราว์เซอร์ + ใส่ File ใน upload form ผ่าน DataTransfer + user กด Commit)

**(C) 🟠 ปัญหารอง: หน้าลูกค้า default = "วันนี้"** — เปิดหน้าลูกค้ามาตั้งโหมด "วันนี้" (14 มิ.ย. 2569 ไม่มีข้อมูล) → เห็น 0 ทุกการ์ด (นี่คือสิ่งที่ user เห็นตอนรายงาน "ลูกค้าทั้งหมดไม่ขึ้น" ส่วนหนึ่ง). ควร default เป็นเดือนล่าสุดที่มีข้อมูล (เช่น พ.ค. 2569) เหมือนหน้า Dashboard

**id/ตัวควบคุมหน้าลูกค้า (อ้างอิงตอนแก้):** ปี dropdown `c-year-sel` value=ค.ศ.(2024-2029) text=พ.ศ.(2567-2572) · เดือน `c-month-sel`(1-12) · ช่วงเวลา `c-range-sel`(''/3m/6m/1y) · date inputs `cdt-from`/`cdt-to` · การ์ดรวม `ret-unique` (+`ret-unique-sub`) · การ์ดใหม่/เก่า `ret-new-*`/`ret-once-*` · ปุ่ม "ดู" class `abtn` · `renderCustomers()` ใช้ getMonths(from,to) map ช่วงวันที่→เดือน แล้ว aggregate จาก MONTHLY_CUST (key=phone||name)

---

_เพิ่มเติม 15 มิ.ย. 2569 (วันเต็มวัน — ไล่แก้บั๊ก + redesign หน้ารายจ่าย + ปรับ KPI + ลบ hardcode 2024):_

**(A) แก้บั๊กค้างจาก 14 มิ.ย. ✅ deploy แล้ว**
- บั๊ก `ret-unique` (การ์ดลูกค้าทั้งหมด = 0 นอก ม.ค.-พ.ค. 2569) — แก้แล้ว ใช้ `MONTHLY_CUST` เป็นแหล่งเดียว (29 เดือนจาก Sheets) ตามที่จดไว้ใน (B)
- CSS มือถือ หน้ารายจ่ายชื่อหมวดตกขอบ — แก้แล้ว (`@media max-width:768px` ของ `.eamt/.epct/.ename`)
- **บั๊กลูกค้า มิ.ย. 2567 = 211 คน (ผิด)** — ไฟล์ดิบ Page365 มี 2,824 บิล / 2,365 unique phones แต่ Sheets เก็บแค่ 211. **สาเหตุ:** ไฟล์แปลงเดิมมีข้อมูลไม่ครบ. **แก้:** สร้างไฟล์ `SURU-แปลง_2567_มิ.ย.tsv` ใหม่จากไฟล์ดิบ `/mnt/project/ไฟล_สถ_ต__JanJun_2024.csv` (UTF-16 TSV) → user อัปเอง → **มิ.ย. 2567 = 2,365 คน ✅**. **ตรวจครบ 30 เดือน 2567-2569 ทุกเดือนถูกหมด** (มิ.ย. 2569 = 578 คนเพราะมีแค่ 10 วันแรก)

**(B) Redesign หน้ารายจ่าย ✅ deploy แล้ว** — กล่อง "📋 รายการทั้งหมด" รื้อใหม่หมด:
- **Summary glassmorphism 4 cards** ขึ้นบนสุดในกล่อง: ยอดขาย/กำไรขั้นต้น/ค่าใช้จ่าย/กำไรสุทธิ (border สีสด, background `rgba(255,255,255,.6)+blur(8px)`)
- **กำไรสุทธิ class dynamic:** `${netProfit>=0?'rev':'rdn'}` → กำไร=เขียว / ขาดทุน=แดง (badge ก็เปลี่ยนตาม) + ไม่มี emoji
- **Layout 2 คอลัมน์ต่อวัน** `.eg-two-col` (gap:0, 1px border คั่นกลาง): ซ้าย="อัตโนมัติ" (`.col-hd-pk` ชมพู), ขวา="เฉลี่ย/วัน" (`.col-hd-cy` ฟ้า), header gradient `90deg, สี, transparent`
- **Day summary + badge กำไร/ขาดทุน** ทุกวัน (ของเดิม)
- **Mobile override** `@media(max-width:768px){.eg-two-col{grid-template-columns:1fr}}` → 1 คอลัมน์ซ้อนกัน ป้องกันหัวข้อตก
- **ปุ่ม Export PDF** ใช้ `.abtn` (solid `#D94E8F`) ของเดิม + ไอคอน 📄 — ห้ามใส่ pill/outline
- desc cell `text-align:right` (ค่าเช่า/ค่าตำรวจตัวเลขตรงแนวกับเงินเดือน)
- เอา dot สีหน้าหมวดออก

**(C) KPI Layout 5+5 + Net Profit Hero ✅ deploy แล้ว**
- **แถวบน P&L (5 cards):** รายได้รวม → ต้นทุน COGS → ค่าใช้จ่าย → **กำไรขั้นต้น (เพิ่มใหม่)** → **Net Profit (hero ใหญ่กว่า 1.5x + เงาเขียว)**
- **แถวล่าง ตัวชี้วัด (5 cards):** บิล/ชิ้น → AOV/บิล → **เฉลี่ย/วัน (เพิ่มใหม่)** → ROAS → Inventory Cost
- **CSS:** `.kgrid-wrapper.row-hero{grid-template-columns:1fr 1fr 1fr 1fr 1.5fr}` + `.kpi-hero .kval{font-size:1.15em}`
- **HTML:** แยกเป็น 2 `.kgrid-wrapper` (บน=row-hero, ล่าง=equal 5col)
- **Render:** เพิ่ม `hero:true` ใน kpi data, render เป็น `${k.hero?' kpi-hero':''}`
- **GP Margin card ถูกตัด** (รวมเข้า "กำไรขั้นต้น" แถวบนแล้ว)
- **Mobile override:** `.kgrid-wrapper, .kgrid-wrapper.row-hero{grid-template-columns:repeat(2,1fr)}` + `.kpi-hero .kval{font-size:1em}` → 2 col เลื่อนแนวตั้ง Net Profit ไม่ใหญ่ผิดสัดส่วน

**(D) ✅ ระบบยั่งยืน — ลบ hardcode ปี 2024 ทุกจุด (รวมในไฟล์รออัป):**
- 🐛 **Bug timezone ใน `getMonthKey`:** เดิมใช้ `dateObj.toISOString().slice(0,7)` → local "Feb 1 2024 00:00 +07" → UTC "2023-12-31T17:00Z" → ป้าย trend ก.พ. 2567 ขึ้น "vs ธ.ค. 2566" (ไม่มีข้อมูล = ไม่ขึ้น). **แก้:** `${dateObj.getFullYear()}-${String(dateObj.getMonth()+1).padStart(2,'0')}` (local time)
- 🐛 **Bug kpiTrend `if(!cmpHas \|\| !prev) return ''`:** `prev=0` ก็ return → ไม่แสดง trend แม้มีข้อมูลเทียบ. **แก้:** เช็คแค่ `cmpHas` + handle edge cases (prev=0 curr=0 skip / prev=0 curr>0 = +100%)
- **getDataForMonth** ใช้ `SHEETS_DAILY` เป็นแหล่งความจริงหลัก (hardcoded `SURU_DATA/APR_DATA/Q1_DATA` เป็นแค่ fallback กรณี Sheets โหลดช้า)
- **ลบ `min="2024-01"`** ออกจาก `cmp-month-a/b` (เหลือ max="2030-12")
- **ตารางรายปี** `for(y=cy;y>=2024;y--)` → dynamic `minYear` จาก SHEETS_DAILY (fallback 2024)
- **`buildYears()`** START dynamic จาก SHEETS_DAILY + เรียกซ้ำหลัง SHEETS_DAILY โหลดเสร็จ + expose `window.buildYears`

**🔴 ไฟล์ค้างรอ user อัปขึ้น GitHub:** `/mnt/user-data/outputs/index.html` (~968 KB) — รวม fix ทั้งหมดข้อ (C)+(D) ผ่าน syntax check + unit tests ทั้ง 3 จุดสำคัญ (getMonthKey timezone, kpiTrend logic, buildYears dynamic minYear)

**🟢 พรุ่งนี้จะเริ่ม: ทดสอบระบบเกี่ยวกับ Master CF Sheet**
- Master CF Sheets ID: `1BB4KXYhDRhmN7if9IVOuaoPJhzR9021GUiAJs5KwP9k` (sheet "📦 Master CF", header row 4)
- รอ user เริ่มทดสอบในแชตถัดไป

**บทเรียน:**
- กฎ "ห้าม hardcode ทุกอย่างเก็บบน Google Sheets" ต้องเช็คทุกครั้งก่อนเขียน fallback — ผมพลาด เขียน `getDataForMonth` ที่ใช้ hardcoded ก่อน Sheets ในครั้งแรก user ต้องเตือน
- ตรวจ regression ก่อนส่งไฟล์ทุกครั้ง: syntax check + unit tests + integrity check (ครั้งนี้พบ timezone bug ผ่าน unit test ก่อน deploy)
- Claude-in-Chrome ค้างได้ตอน reload → ต้อง catch error และทำงานต่อ ไม่ใช่หยุด
- ส่ง mockup 3 แบบให้เลือกเมื่อ user ไม่แน่ใจ design — เร็วกว่ากระทุ้งแก้ทีละจุด

---

_เพิ่มเติม 17 มิ.ย. 2569 (หมวดผันแปรเก็บลง Sheets + หน้าต่างจัดการหมวด — verify ครบบน live):_

**(A) ฟีเจอร์เสร็จ + ขึ้น live + verify ครบวงจร ✅**
- แก้บั๊ก "เพิ่มหมวดผันแปรแล้วยังไม่ลงยอด → หายตอนรีเฟรช/สลับเดือน" ด้วยการ persist รายชื่อหมวดลงชีตใหม่ **"หมวดผันแปร"** (ดูหัวข้อเต็มในส่วน "ระบบกรอกยอดผันแปร")
- index.html: 7 จุดแก้ (persistVarCats / addVariableItem / renderVarTabs 2 จุด / #var-catmgr / loadFromSheets 2 จุด) — node --check ผ่าน + unit test 30/30 — **commit ขึ้น GitHub Pages แล้ว** (live = 980,039 ตัวอักษร, markers ครบ 5)
- Apps Script: เพิ่ม 2 branch `varcats` บนสุดของ doGet/doPost — **deploy เวอร์ชันใหม่แล้ว** (`?type=varcats` คืน 200 ok)
- ทดสอบจริงบนเว็บ: เพิ่มหมวด (ไม่ลงยอด)→เซฟลงชีต→รีเฟรชเต็ม→ยังอยู่ ✅ →ลบ→หายทั้งจอ+ชีต ✅ · ลบข้อมูลทดสอบหมดแล้ว · ลิสต์ในเพจ=ชีตเป๊ะ

**(B) วิธี deploy เปลี่ยน (user สั่ง "ใช้วิธีนี้ตลอด" → จดใน memory แล้ว):** Claude แก้ไฟล์ LIVE ในเบราว์เซอร์ (fetch raw + apply find/replace pairs ที่ verify เจอ 1 ตำแหน่ง) → **สั่งดาวน์โหลด index.html ลงเครื่อง user** → user อัป GitHub + Commit เอง — ไม่ยัดฟอร์ม upload, ไม่ส่ง patcher แยก (ดูหัวข้อ "วิธี deploy index.html")

**บทเรียน:**
- ส่งโค้ดใหม่ก้อนใหญ่เข้าเบราว์เซอร์ให้แม่น = gzip+base64 แล้ว decode ด้วย DecompressionStream('gzip') ในเพจ (29KB → blob 5.6KB) · page ไม่รับ top-level await ต้องห่อ async IIFE + เก็บผลที่ window
- Claude-in-Chrome timeout ได้ (เคยค้าง 4 นาที 2 รอบกลางคัน) → กลับมาใช้ได้เองภายหลัง, ระหว่างนั้นเตรียม fallback ไว้แต่ยังไม่ทิ้งวิธีหลัก
- หมวดระบบที่ล็อก (tax, ค่าส่งไปรษณีย์) = ผูกกับ auto-calc/EMS — ถ้า user ถามว่า "ตั้งใจไม่ให้ลบหรอ" ให้ explain เหตุผล (กันพัง) ไม่ใช่ป้องกันลอย ๆ
- user ย้ำหลักคิด: **ออกแบบให้ยั่งยืน รองรับการแก้ไขในอนาคตเสมอ** (ล็อกตามชื่อหมวด ไม่ล็อกตามตำแหน่ง "ตัวแรก" ที่เปราะ)
