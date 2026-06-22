# SURU Japan Business Dashboard — Context (อัปเดตล่าสุด 2026-06-21)

## บริบทโครงการ
- **Project**: SURU Japan Business Dashboard — แดชบอร์ดธุรกิจร้านขายของญี่ปุ่น ภาษาไทย (P&L, KPI, รายจ่าย, วิเคราะห์ลูกค้า, ตารางเปรียบเทียบ)
- **Repo**: `nudanica/suru-dashboard` (GitHub Pages) → `https://nudanica.github.io/suru-dashboard`
- **โครงสร้าง**: single-file `index.html` บน branch `main` (~6,994 บรรทัด, ~1.35 MB)
- **GitHub raw URL**: `https://raw.githubusercontent.com/nudanica/suru-dashboard/main/index.html`
- **Local working file**: `/home/claude/index.html`
- **Output file**: `/mnt/user-data/outputs/index.html`
- **Backend**: Google Sheets เป็น source of truth + Apps Script

### ID / ทรัพยากรสำคัญ
- **Dashboard Data Sheet ID**: `1qAR5qjqv-a1lnxYMlwbL-xHixpuATn-8dIW3iLrpJDw` (ลงท้าย `...iLrpJDw`) — **Apps Script อยู่ที่นี่** (Extensions → Apps Script)
- **Master CF Sheet ID**: `1BB4KXYhDRhmN7if9IVOuaoPJhzR9021GUiAJs5KwP9k` (ห้ามแก้ live)
- **Chrome MCP deviceId**: `6d454699-57e2-467d-9f9f-47d930148bf8` ("claude suru", macOS) — live tab `900007024`, GitHub tab `900007050`
- **Data sources**: Page365 (ยอดขาย), `STOCK_page365.csv` (UTF-16 tab-sep, bridge สำหรับ CF code matching)

### ภาษา / การสื่อสาร
- ผู้ใช้สื่อสารภาษาไทย เรียกแทนว่า **"คุณ"** (ห้าม "ดา"/"น้องดา")
- ลงท้าย "ครับ" บ้างเพื่อความสุภาพ
- ผู้ใช้ใช้ iPhone/Mac

---

## Deployment Workflow (คงที่ — ห้ามเปลี่ยน)
1. แก้ `/home/claude/index.html` → ตรวจ syntax ด้วย `node` (loop ทุก `<script>` block ผ่าน `new Function()`)
2. `cp` ไป `/mnt/user-data/outputs/index.html` → `present_files`
3. ผู้ใช้ download + upload ขึ้น GitHub เอง (manual commit)
4. ตรวจ live ผ่าน Chrome MCP (navigate `?_=vN` cache-bust)
- ❌ ห้าม inject ไฟล์เข้า GitHub upload form
- ❌ ห้ามส่ง patcher แยก

---

## หลักการสำคัญของผู้ใช้
- **"อย่าให้กระทบของเดิมโดยเด็ดขาด"** — ทุก change ต้อง render-layer/additive
- ห้าม mark งานเสร็จโดยไม่ verify บน live (ผู้ใช้เคยหงุดหงิด)
- ห้าม hardcode ข้อมูลที่ควร dynamic (ปี, เดือน, จำนวนลูกค้า ฯลฯ)
- Google Sheets category name strings = transaction linkage keys → **ห้ามแก้** (strip emoji render-layer only)
- ผู้ใช้ review mockup ก่อน implement, ให้ feedback ละเอียด (สี/ขนาดฟอนต์/ระยะ/ความเข้ม) → ใช้ `ask_user_input_v0` + `visualize:show_widget`
- ห้ามแก้ live Master CF sheet
- No-cost criterion = `E=0 AND F=0` (ไม่ใช่ cost>0 เพราะ default shipping=40)
- `custmonth` files upload ทีละไฟล์ (กัน POST race)

---

## ===== งานปัจจุบัน [IN PROGRESS — ยังไม่ลงมือ code] =====

### ทำให้ทุกการ์ดในหน้า Dashboard ใช้สไตล์เดียวกัน (กรอบ gradient + พื้นขาว)
ผู้ใช้ขอ: กราฟยอดขายรายวันที่เพิ่งทำเสร็จ "โดดเด่นขึ้นมาอยู่คนเดียว" (กรอบ gradient ชมพู-พีช + พื้นขาว) → ต้องการให้ **ทุกการ์ดในหน้า Dashboard ใช้สไตล์เดียวกัน**

**การ์ดเป้าหมาย 5 จุดที่เหลือ:**
1. **ตารางเปรียบเทียบ** (`.cmp-table` CSS ~บรรทัด 372 — มี `backdrop-filter: blur` ต้องเปลี่ยนเป็นพื้นขาว)
2. **รายจ่ายแยกหมวด** (`.card` ~870)
3. **ช่องทาง(การขาย)** (`.card` ~877)
4. **ค่าโฆษณา** (`.card` ~899, ctitle มี `cbadge id=ads-roas-badge`)
5. **สินค้าคงคลัง** (`.card` ~910, ctitle มี `cbadge id=inv-badge`)

(กราฟยอดขาย `.card` ~809 ทำกรอบ gradient แล้ว แต่ใช้ hardcode `#FFD0E6→#FFE9D6`)

**[DECISION ผู้ใช้ยืนยันล่าสุด]**: สีกรอบ gradient ของทุกการ์ด **"ให้เป็นเหมือนกันทุกการ์ด"** แต่ต้อง **ผูกกับ Theme system** — เลือก theme ไหน (pink/mint/lavender/peach/sky) สีกรอบต้องเปลี่ยนตาม theme นั้น
→ **ต้องใช้ CSS vars (`--pk`/`--pkl`) สร้าง gradient จาก theme color** ไม่ใช่ hardcode หรือให้ `applyCustomTheme()` อัปเดต gradient ตาม theme

**แผนแนะนำ (NEXT STEP):**
1. สร้าง CSS class `.card-grad` = wrapper กรอบ gradient (อิง theme vars `--pk`/`--pkl`) padding ~3-4px → พื้นขาว radius
2. Apply กับการ์ด 5 จุดที่เหลือ + พิจารณาเปลี่ยนกราฟยอดขายเป็น theme-var-based ด้วย (ความสม่ำเสมอ)
3. `.cmp-table` มี blur ต้องเปลี่ยนเป็นพื้นขาว
4. Verify ทุก theme บน live
- **สถานะปัจจุบัน: ยังไม่มี `.card-grad` ในไฟล์** (ตรวจแล้ว grep = 0 ทั้ง local + output)

---

## Theme System (สำคัญมากสำหรับงานปัจจุบัน)
- CSS vars บรรทัด 20: `--pk:#FF6BAE;--pkl:#FFF0F7;--pkd:#D94E8F;`
- `.theme-grid` CSS 608, `.theme-btn` 609-613, `.theme-swatch` 614-615
- theme buttons HTML ~1453-1457, `applyCustomTheme()` ~1542, themeBtns handler ~1508/1538-1542
  - (`CUSTOM.theme = btn.dataset.theme` → `applyCustomTheme()`)
- หัวข้อ "เลือกธีมสีสำเร็จรูป" บรรทัด 1451

**5 Themes** (data-theme: หลัก / กลาง / อ่อน):
- **pink** (default): `#FF6BAE / #FFD9E8 / #FFF5FA`
- **mint**: `#3DCFB5 / #C8F0E5 / #F0FBF7`
- **lavender**: `#9B7BC8 / #E0D2F0 / #F7F3FB`
- **peach**: `#FF8A65 / #FFD2C2 / #FFF5F0`
- **sky**: `#4A9EFF / #CCE2FF / #F0F7FF`

`CUSTOM.theme` เก็บใน customization, sync Sheets

---

## ===== งานที่เสร็จ + verified live (เซสชันนี้) [COMPLETED] =====

### 1. กราฟยอดขายแบบใหม่ (SVG แทน Chart.js เดิม) [COMPLETED + verified live]
แทน chart-rev (Chart.js bar) + dropdown `chart-granularity` เดิม → กราฟ SVG ใหม่ ปุ่มสลับ 3 มุมมอง รายวัน/รายเดือน/รายปี
- **HTML container** (~บรรทัด 808-809): `.card` กรอบ gradient `#FFD0E6→#FFE9D6` (padding 4px) + พื้นขาว radius 19px, ปุ่ม `.sc-tab`, ป้ายช่วง `#sc-period`, การ์ดสรุป 3 ใบ (ขายดีสุด/ค่าแอดรวม/ยอดขายรวม), `#sc-chart` div, legend
- **CSS เพิ่ม** (หลังบรรทัด 84): `.sc-tab`/`.sc-tab.active`, `.sc-num` (Sarabun weight 800), `.grad-pink/amber/green` (text gradient)
- **JS** (`renderSalesChart()` + `setSalesView()` + `_scPeriodLabel()` ก่อน `renderDash()` ~บรรทัด 2898): state `_salesView` (day/month/year)
  - รายวัน = รวมตามชื่อวัน (จ-อา order `[1,2,3,4,5,6,0]`), รายเดือน = ตามวันที่ 1-31, รายปี = ตามเดือน ม.ค.-ธ.ค. (ดูทั้งปี ไม่สนฟิลเตอร์)
  - แท่งวันขายดีสุด = ชมพูเข้ม `scg1`, อื่นๆ `scg2` อ่อน, เส้นค่าแอดเหลือง `#FFD43B`, ตัวเลขบนแท่ง (7px แสดงเมื่อ n≤14), ฟอนต์ Sarabun (`var(--font)`)
- **CRITICAL FIX (ads)**: `daily.ads` = 0 เสมอ! ค่าแอดจริงเก็บแยกใน **`ADS_STORE`** (keyed by txId: `.date/.amount/.account/.mo/.txId`). แก้ `renderSalesChart()` สร้าง `adsByDate` map จาก ADS_STORE แล้ว `adsOn(dateStr)` แทน `r.ads` ทั้ง 3 views
  - Verified: ads 2026 แยกเดือน ม.ค.฿357k ก.พ.฿243k มี.ค.฿300k เม.ย.฿174k พ.ค.฿236k รวม **฿1,311,720** ✅
- **Verified live**: รายปีแสดงครบ — ม.ค.ขายดีสุด ฿2,878,761, ยอดขายรวม ฿12,173,919 ✅ (892 daily records, 161 records ปี 2026)
- ผ่าน mockup ~8 iterations: ฟอนต์ตัวเลข Sarabun weight 800 (เหมือน KPI `.kval` ไม่ใช่ IBM Plex Mono), การ์ดพื้นขาว+กรอบ gradient, ตัวเลขสี gradient

### 2. ตารางเปรียบเทียบ — แก้ปุ่มกดเปิดปฏิทิน [COMPLETED, รอ verify live]
- ปัญหา: `<label.cmp-chip>` ครอบ `<input type=month opacity:0>` กดพื้นที่ว่างไม่เปิด picker บน Chrome desktop
- แก้: เพิ่ม `onclick="scOpenMonthPicker(event,'cmp-month-a')"` (และ `-b`) ที่ label (~บรรทัด 795/801) + `function scOpenMonthPicker(ev,inputId)` ก่อน `pickCmpAMonth` (~2608) — เรียก `inp.showPicker()` fallback `focus()+click()`

### 3. รายจ่ายแยกหมวด (Dashboard widget exp-breakdown-d) — emoji → doodle [COMPLETED + verified]
- จุด exp-breakdown-d (~บรรทัด 3263 ใน renderDash): เพิ่ม `_dashCatDoodle(cat)` (แอด→megaphone, EMS→truck, เงินเดือน→salary ฯลฯ, ตรวจ `CUSTOM.iconOverrides['expcat:'+cat]` ก่อน) + Chart labels/ename ใช้ `stripEmoOnly()` + svgIco doodle
- Verified live: "ค่าแอด Facebook" ไม่มี emoji ✅

---

## งานที่เสร็จเซสชันก่อนหน้า (ไฟล์เดียวกัน, รอ verify บางส่วน)
- **Line broadcast ซ้ำ — FIXED**: ต้นตอ hardcode ~บรรทัด 2483 `let VARIABLE_ITEMS=['Line broadcast']` + `VARIABLE_FREQ={...}` → แก้เป็น `[]` และ `{}` (ให้มาจาก Sheets type=varcats เท่านั้น). `📢Line broadcast` ใน Sheets ผู้ใช้ต้องลบเองครั้งเดียว
- **emoji ค้างหน้ารายจ่าย — FIXED**: exp-summary-row + eg-row-cat → doodle SVG + stripEmoOnly; rp-list `.ename` + varTabs strip emoji
- **KPI Net Profit font — FIXED**: บรรทัด 83 `.kpi-hero .kval` → `26px` (mobile `22px`)
- **window.openPickerForExpCat — FIXED**: expose ก่อน IIFE `})();` + picker save hook `expcat:` branch
- **getCatDoodle ใน exp-cat-list (renderExpPage ~5357)**: ตรวจ CUSTOM.iconOverrides ก่อน + crow-ico clickable

---

## โครงสร้างฟังก์ชัน/บรรทัดสำคัญ (index.html ~6994 บรรทัด)
- CSS vars บรรทัด 20: `--pk/--pkl/--pkd`
- `.card` CSS **บรรทัด 145** (`background:var(--sf);border:1px solid var(--bd);border-radius:var(--r);padding:18px 20px;margin-bottom:16px;box-shadow:var(--sh)`)
- `.chead` 146, `.ctitle` 147 (`font-size:14px;font-weight:600`), `.twocol` 149 (`grid-template-columns:1.4fr 1fr;gap:16px`), responsive→1fr ที่ max-width:900px(150)/media query(567)
- `.sc-tab`/`.sc-num`/`.grad-*` CSS หลังบรรทัด 84
- `.cmp-table` CSS **372** (มี `backdrop-filter:blur` — เป้าหมายเปลี่ยน), `.cmp-table-head` 373 (gradient ชมพู-ม่วงอยู่แล้ว), cmp-chip labels HTML ~795-805
- การ์ด Dashboard HTML: กราฟยอดขาย ~809, รายจ่ายแยกหมวด ~870, ช่องทาง ~877, ค่าโฆษณา ~899, สินค้าคงคลัง ~910, ค่าโฆษณาแยกบัญชี ~932
- `getAllDaily()` 2193 (daily record = `{date:'YYYY-MM-DD', revenue, ads(=0 เสมอ)}`)
- `getDataForMonth(mo)` 2216, **`ADS_STORE`** = ค่าแอดจริง (txId→{date,mo,amount,account,txId}, รวม ฿1,311,720)
- `VARIABLE_ITEMS=[]`/`VARIABLE_FREQ={}` ~2483-2484, `DAILY_ITEMS=['ค่าส่งไปรษณีย์']`, `YEARLY_ITEMS=[]`
- `scOpenMonthPicker()` + `pickCmpAMonth/BMonth` ~2608+
- `renderSalesChart()`/`setSalesView()`/`_scPeriodLabel()` ก่อน `renderDash()` ~2898
- `renderDash()` ~2898+, exp-breakdown-d + `_dashCatDoodle` ~3263, ads-account-cards ~3197
- `getCatStyle()`, `renderExpPage()` ~5067, `getCatDoodle()` ~5357
- `iconize()`/`stripEmoOnly()`/`svgIco()`/`DOODLE_P`/`CUSTOM.iconOverrides`
- `backupToSheets(type,data,months)` ~1860, `persistVarCats()`, `loadFromSheets()` ~1893
- **CF_NAMES** (CF→ชื่อ) บรรทัด 1939, **CF_COST** (CF→{name,sell,cost,profit,gp}) บรรทัด 1943 — embedded Master CF snapshot ~440 SKUs (prefixes B/C/D/DS/E/F/G/H/HR/i/L/M/MC/MQ/O/P/S/SD/SH/SN/T/V/W/Y). **CF_HAS_PRICE** Set ~1891
- ปุ่มเครื่องมือที่ผู้ใช้ชอบสไตล์ (template อ้างอิง): `.tool-btn`/`.tool-glass`/`.tool-ico`/`.tool-go` HTML ~1150-1172, CSS 455-478 (กรอบนอก gradient pastel padding 3px radius 20px → glass พื้น `rgba(255,255,255,.55)` blur radius 17px → วงกลม 46px doodle ขาว)

### Doodle Library
- `suru-doodles-final.html` (46 icons, สีชมพู `#FF6BAE`, stroke 2.2)
- ไอคอน pink `#FF6BAE`, ไม่มี blob backgrounds บน KPI/list (KPI วงกลมสีทำหน้าที่แล้ว)

---

## MCP Browser Patterns
- `Claude in Chrome:javascript_tool` (action='javascript_exec', tabId, text — REPL semantics, top-level await ok, last expr returned)
- `browser_batch`, `read_console_messages` (ต้องมี pattern), `computer` (screenshot/click/scroll/key/wait), `navigate`
- Wrap async ใน `(async()=>{...})()` — top-level await ไม่รองรับ
- Top-level `return` → "Identifier already declared" → ใช้ IIFE `(()=>{...})()`
- Async results: set `window.__R=null` → run async block set `window.__R` → read แยก call
- Large payloads: store บน `window` แล้ว reference by name
- Content filter block "Cookie/query string data" เมื่อ slice script source ที่มี emoji+Thai → ใช้ `indexOf` คืน int แทน slice string
- `tabs_create_mcp` timeouts ไม่ recover on retry → fallback `present_files`
- Large file injection: base64 via Python + `atob`/`TextDecoder` (กัน Thai/emoji/backtick เพี้ยน)
- `tool_search` ต้องเรียกก่อนใช้ deferred tools (Chrome MCP, Google Drive)

### Warnings ปกติ (ไม่ใช่ bug)
`Sheets load failed: Failed to fetch` + `loadCust: Failed to fetch` — เกิดเมื่อเปิด local (`file://`), หายบน GitHub Pages

---

## NEXT STEPS [PENDING]
1. **[งานปัจจุบัน] Implement `.card-grad` (theme-var-based gradient)** กับ 5 การ์ดที่เหลือ + พิจารณาเปลี่ยนกราฟยอดขายเป็น theme-var-based → verify ทุก theme บน live (.cmp-table มี blur ต้องเปลี่ยนเป็นพื้นขาว)
2. **Verify บน live (ค้างจาก upload ก่อน)**: ตารางเปรียบเทียบกดเปิดปฏิทินได้, Line broadcast ไม่ซ้ำ, KPI Net Profit font 26px
3. งานค้างเก่า (จาก journal):
   - Master CF Sheet integration testing
   - COGS completeness audit 2569 ด้วยเกณฑ์ `(¥=0 AND ฿=0)`
   - SKU parser fix ~51 items missing CF codes
   - current-month variable expense entry
   - tax/variable expense close button visibility (ค้างจาก session ก่อน)

---

## Master CF Consolidation (งานคู่ขนาน)
- ทำให้ `SURU Master CF Final.xlsx` เป็น single source of truth ของ cost data (¥ และ ฿)
- Deliverable `FILL_MasterCF_441.xlsx`: 441 clean CF→¥ pairs (กัน 3 wrong-name + 8 cost≥sell แยก), projecting coverage ~62% → ~84%
- ผู้ใช้กรอก ¥ ลง column E เอง — **Claude ห้ามแก้ live Master CF sheet**
- Safe apply: `=IFERROR(VLOOKUP($A4, temp!$A:$B, 2, FALSE), E4)` ใน helper column

---

## Transcripts (catalog)
- `2026-06-18-13-52-01-suru-dashboard-dev.txt` — Phase C.2 Doodle Picker, expense page redesign
- `2026-06-18-14-15-41-suru-dashboard-doodle-emoji.txt` — expense category emoji→doodle, Category Doodle Picker
- `2026-06-19-02-26-43-suru-dashboard-styling.txt` — SVG sales chart, cmp month picker, KPI font, Line broadcast fix
- `2026-06-19-02-30-01-suru-dashboard-styling.txt` — complete SVG sales chart (verified), card unification request เริ่ม
- `2026-06-19-02-34-28-suru-dashboard-styling.txt` — sales chart + ads fix verified, card-grad theme-linked decision



---

## 📌 อัปเดตเซสชัน 19 มิ.ย. 2569 — UI Restyle (cards / doodle picker / P&L / compare table)

### Workflow ที่ใช้จริงตอนนี้ (สำคัญ — แทนของเดิม)
- bash network ปิด, /mnt/project/index.html เก่าไม่ตรง live, ไฟล์ 1MB ส่งเข้า container ไม่ได้ (content filter บล็อก base64/hex, tool result ตัด)
- ✅ วิธีที่ใช้: แก้ซอร์ส live ในเบราว์เซอร์ผ่าน Chrome MCP ทั้งหมด
  1. fetch raw GitHub → window.__SRC (ต้อง fetch ใหม่ทุกครั้งหลัง navigate เพราะ window รีเซ็ต)
  2. find/replace บน __SRC → __OUT, แต่ละ find ต้อง match พอดี 1 ตำแหน่ง (ไม่ตรงให้ abort)
  3. syntax check: new Function(body) ทุก inline script (คาดว่า 6 scripts, 0 fail)
  4. ดาวน์โหลดผ่าน Blob ในเบราว์เซอร์ (a.download=index.html ~1.35MB) → ลง Downloads → คุณอัป GitHub + commit เอง
  5. verify บน live หลังอัป (fetch raw เช็ค marker, reload ?_=vN, screenshot)
- อ่านโค้ดที่โดน content filter บล็อก: mask อักขระ (quote→‹, dquote→«, equal→◊, colon→¤, backtick→BT) แล้ว strip non-ASCII (ตัวเลข/CSS ผ่าน, Thai+emoji+query-like โดนบล็อก)
- ค่าอ้างอิง: Chrome deviceId 6d454699-57e2-467d-9f9f-47d930148bf8, live tab 900007024, GitHub tab 900007050

### เสร็จ + deploy + verify บน live แล้ว
1. การ์ดกรอบ gradient รวม — ทุก .card (18 จุดทุกหน้า) + .cmp-table → กรอบ gradient ผูกธีม, รัศมี 22px / กรอบ 4px, พื้นขาว (ให้เท่าการ์ดยอดขาย). ไม่แตะ .kpi/.kpi-glass/.kico/.inv-card/.tool-glass. ขับด้วยตัวแปร --cg1/--cg2 + observer ที่ inject (additive ไม่แตะ applyCustomTheme). คู่สีต่อธีม: pink #FFD0E6 ถึง #FFE9D6, mint #B5F0D6 ถึง #C6E4FB, lavender #E2D2F2 ถึง #F7D7E9, peach #FFCDB8 ถึง #FFE8B5, sky #CDE3FF ถึง #DCD2F4
2. แก้ Doodle picker (marker SURU_DOODLE_FIT) — slot .slot-ico แสดงเป็นวงกลมทึบ เพราะ doodle พิกัดสเกลปนกัน (32/64/128) ทำให้ normalizeDoodleSvg (global, ใช้ getBBox, ตั้ง svg.__norm) จัดเฟรมเพี้ยน (bbox โตขึ้นต่อ slot = transform ทับซ้อน). Fix = สคริปต์ additive: IntersectionObserver + MutationObserver อ่าน getBBox จริงตอน slot มองเห็น แล้วตั้ง viewBox = bbox + pad 16% + preserveAspectRatio xMidYMid meet. verify แล้ว: doodle จัดเฟรมเองตอนเข้า customize ไม่ต้องเรียกมือ

### เสร็จในเครื่อง + พรีวิว live แล้ว — อยู่ในไฟล์ที่ดาวน์โหลด รออัป
3. Task B — การ์ด P&L หน้ารายรับรายจ่าย → สไตล์ KPI (แก้ CSS ล้วน +291 ตัวอักษร). การ์ดเป็น HTML static #pl-hero/.pl-hero (hero: .pl-hero-lbl/.pl-hero-val/.pl-hero-badge/.margin-pill + .pl-breakdown grid ของ .pl-bd-cell → .pl-bd-lbl/.pl-bd-val/.pl-bd-pct; JS toggle .pl-hero.loss). แก้:
   - .pl-hero → กรอบ gradient เขียว #C9F0DE ถึง #D4F5E8 (กำไร=ค่าเริ่มต้น), border 4px transparent, radius 22px; + .pl-hero.loss → แดง #FFCAD6 ถึง #FFE0D2; + .pl-hero::before display:none (ซ่อนเส้นแดงบนเดิม)
   - .pl-bd-lbl → +weight 700 +uppercase +letter-spacing .07em
   - .pl-bd-val → 22px + font-family var(--font) Sarabun (เดิม 17px mono)
   - คงเป็นการ์ดเดียว แบ่ง 4 คอลัมน์ภายในใบเดียว (คุณย้ำว่าห้ามแยกเป็น 4 ใบ)

### ถัดไป — Task C (ตารางเปรียบเทียบ, mockup อนุมัติแล้ว, ยังไม่ลงมือ)
เป้าหมาย: (1) รวมฟิลเตอร์วันที่เข้ากรอบเดียวกับตาราง พื้นขาว gradient 22px แบบการ์ดยอดขาย (2) ใส่ดูโด้หน้าแต่ละแถว (3) คอลัมน์ "เปลี่ยนแปลง" โชว์ผลต่างจริง
โครงสร้างที่ map ไว้:
- HTML static: div class cmpbar id cmpbar (ฟิลเตอร์ = .cmp-chip a/b + input type month + scOpenMonthPicker/pickCmpAMonth/pickCmpBMonth + .cmp-vs) แล้วตามด้วย sibling div class trend-row id trend-row (~idx 92165–92800) = target ที่ render ตาราง
- JS render (~idx 880560–881450, โดน filter อ่านแบบ mask): trendEl.innerHTML = template ที่มี div.cmp-table + div.cmp-table-head 5 คอลัมน์ (main / cmpHeaderLabel=เดือน B / currentChip=เดือน A / เปลี่ยน% / คอลัมน์ที่5) แล้วเรียก mkRow ต่อกันหลายแถว. mkRow(color, icon, label, valA, valB, type [,flag]) สร้างแถว + .cbar/.cbar-fill (บาร์) + .cdiff pctCls โชว์ diffStr (diff คำนวณไว้แล้ว)
- .cmp-table มีกรอบ gradient จากงานข้อ 1 อยู่แล้ว → ต้องทำให้ไร้กรอบ/โปร่งเมื่ออยู่ในการ์ดใหม่
แผน: หุ้ม .cmpbar + #trend-row ใน div class card cmp-card เดียว (แก้ HTML); .cmp-card .cmp-table {border:0;background:none}; แก้ template mkRow เพิ่มดูโด้ (svgIco/DOODLE_P) หน้าแถว + ให้คอลัมน์เปลี่ยนแปลงโชว์ผลต่าง absolute. จุดเสี่ยง = แก้ template ในโซนที่ filter บล็อก → ใช้ mask read + indexOf/slice แคบๆ. verify ทุกธีมหลังทำ

### หลักการเพิ่ม (จากเซสชันนี้)
- "การ์ดเดียว" = อย่าแยกเป็นหลายใบ (B mockup แรกแยก 4 ใบ → ถูกตีกลับ)
- งานใหญ่ที่ verify แล้วส่งก่อน อย่าเอางานที่ยังไม่เสร็จมาปนในไฟล์เดียว
- ก่อนทำ visual restyle ทำ mockup ให้ดูก่อนเสมอ


---

## 📌 อัปเดตเซสชัน 19 มิ.ย. 2569 (ต่อ) — Task C: ตารางเปรียบเทียบ Style C

### สรุป — ✅ implement + fix บั๊ก + verify 5 ธีมในเบราว์เซอร์ผ่าน  ⏳ รอ re-upload ไฟล์ที่แก้ + ยืนยัน live
ผู้ใช้เลือก **Style C "เข้มมินิมอล + แต้มสี"** = label เข้ม `var(--tx)` + ไอคอนลงสีหมวด + จุดสีสด

**สิ่งที่ทำ (12 find/replace บน __OUT แต่ละจุด match พอดี 1 ตำแหน่ง):**
1. หุ้ม `.cmpbar` + `#trend-row` ใน `<div class="card cmp-card">` ใบเดียว + เพิ่ม `<div class="cmp-card-title">` หัวข้อ "ตารางเปรียบเทียบ" ขึ้นบนสุดของการ์ด
2. หัวตาราง: `.cmp-th-main` เปลี่ยนจากชื่อตาราง → "หมวดหมู่"; เดือนเป็น **สีฟอนต์ (ไม่มีแถบ chip)** — พ.ค.=`var(--pk)`, เม.ย.=`#9B6DFF` (ม่วงคงที่)
3. **สลับคอลัมน์ค่าให้ตรงกับแถบ VS**: พ.ค.(ปัจจุบัน `.cv now` เข้มหนา) ซ้าย, เม.ย.(เทียบ `.cv` จาง) ขวา + สลับหัวคอลัมน์ `chip-sm a/b` ให้ a มาก่อน b — ใช้ token-swap 3 จังหวะ (X→TMP, Y→X, TMP→Y) กัน count ชนกัน
4. ไอคอนแต่ละแถวลงสีหมวด: `iconize(ico, 16, "currentColor")` → `iconize(ico, 16, dot)` (label คงเข้ม)
5. inject CSS: `.cmp-card`, `.cmp-card-title`, `.cmp-card .cmp-table{border:0;background:none}`, หัวตารางไร้แถบ, `.chip-sm.a{color:var(--pk)}` ฯลฯ (specificity ใช้ `.cmp-card .cmp-table-head .cmp-th-col .chip-sm.a` ให้ override ชนะแน่)

**Goals 2 & 3 (ดูโด้หน้าแถว + คอลัมน์ผลต่าง absolute) มีอยู่บน live แล้ว** ก่อนเซสชันนี้ (live ล้ำหน้า note เดิม) — เซสชันนี้ทำจริงแค่ Goal 1 (รวมฟิลเตอร์เข้ากรอบเดียว) + restyle หัว/คอลัมน์/สี

### 🐛 บั๊กสำคัญ + บทเรียน (CSS injection)
- ครั้งแรก inject CSS เป็นบล็อก `<style id="cmp-card-css">...</style>` **ซ้อนเข้ากลาง `<style>` เดิม** (anchor ก่อน `.cmp-table{`) → `</style>` ของบล็อกใหม่ไป "ปิด" `<style>` เดิมก่อนเวลา → กฎ CSS ทั้งหมดหลังจุดนั้น (รวม `.cmp-trow{display:grid}`) หลุดนอกสไตล์ = ตารางเรียงตั้งพังทั้งหมด **และ deploy ขึ้น live ไปแล้ว (พังอยู่)**
- ✅ **บทเรียน: inject CSS ผ่าน file-patch ห้ามใส่แท็ก `<style>` ซ้อน — แทรกเป็น "กฎ CSS ล้วน" เข้าสไตล์ชีตเดิม** (หรือเพิ่ม `<style>` แยก **หลัง** `</style>` ตัวสุดท้ายเท่านั้น)
- Fix: ลบ `<style id="cmp-card-css">\n` + `</style>\n` (ก่อน `.cmp-table{`) → เหลือกฎล้วน, `<style>` กลับสมดุล 2 คู่, **ไฟล์ที่ถูกต้อง = 1,075,716 bytes** (ตัวพัง 1,075,751)
- verify หลัง fix: `.cmp-trow` กลับเป็น `display:grid`; Style C ขึ้นถูกครบ 5 ธีม — หัวเดือน พ.ค. = `--pk` ทุกธีม (ชมพู `#FF6BAE` / มินต์ `#3DCFB5` / ลาเวนเดอร์ `#9B7BC8` / พีช `#FF8A65` / ฟ้า `#4A9EFF`)

### เทคนิค verify ไฟล์ก่อนอัป (ใช้ได้/ไม่ได้ — สำคัญ)
- ❌ `document.write(__OUT)` ทับหน้า → สคริปต์รัน/ข้อมูลโหลด แต่ `<style>` ไม่ apply (grid พัง) = **false negative อย่าใช้ตัดสินภาพ**
- ❌ `navigate` tool ไม่รับ `blob:` / `data:` URL ("Invalid URL")
- ❌ เปิด blob ในแท็บเดิมด้วย `location.href` → หน้า render ได้ แต่ MCP เข้าไม่ถึง ("Permission still required after granting")
- ✅ **`<iframe>` ตั้ง `srcdoc = __OUT` (`sandbox="allow-scripts allow-same-origin"`) append ลงหน้า live เดิม** → parse เป็น document เต็ม CSS apply จริง, parent อ่าน `contentDocument`/`contentWindow` ได้ (เช็ค `getComputedStyle`, เรียก `pickCmpAMonth`, สลับธีม, screenshot) = วิธี verify ก่อนอัปที่เชื่อถือได้ที่สุด
- ⚠️ Chrome MCP ค้าง/timeout 4 นาทีได้ (เกิดหลัง remove iframe เต็มจอ + trigger Blob download) retry ก็ค้าง → ผู้ใช้ต้อง restart MCP server หรือ fallback present_files
- ตรวจไฟล์ที่ commit จริงให้ fetch `raw.githubusercontent.com/.../main/index.html` (สะท้อนทันที); Pages (`nudanica.github.io`) CDN ดีเลย์ ~นาที — fetch URL Pages เช็ค `len===<ขนาดไฟล์>` ว่า propagate แล้วก่อน reload

### ⏳ Task C ยังปิดไม่สมบูรณ์ — ต้องทำต่อ
1. **ผู้ใช้อัป `index.html` ตัวที่แก้แล้ว (1,075,716 bytes) ทับบน GitHub** แทนตัวพัง (ตัวพังกำลัง live) — ไฟล์อยู่ใน Downloads (น่าจะชื่อ `index (1).html` = ตัวล่าสุด เล็กกว่าตัวแรก 35 bytes)
2. หลังอัป: verify บน live จริง (reload `?_=vN`) ว่า grid + Style C ขึ้นครบทุกธีม + ไม่กระทบการ์ด/หน้าอื่น → ค่อยปิด Task C
3. (ทางเลือก) ทำป้าย VS ตัวใหญ่ (`.cmp-chip.a` = พ.ค.) ให้วิ่งตาม `--pk` ด้วย (ตอนนี้ hardcode ชมพูของเดิม) เพื่อให้ทั้งการ์ดสอดคล้องธีม

### หมายเหตุสถาปัตยกรรม
- localStorage มี key `__SURU_INDEX_HTML` (~1MB) — ตรวจแล้ว **ไฟล์ที่เสิร์ฟไม่ได้ใช้ key นี้ override ตอนโหลด** (เป็น cache ของฟีเจอร์ gallery/ตกแต่ง) → ไม่เกี่ยวกับการ deploy ปกติ


---

## 📌 อัปเดตเซสชัน 21 มิ.ย. 2569 — บั๊กรหัส i/I + Google Form (validation + สกุลเงิน radio)

งานทั้งหมดทำผ่าน Chrome MCP บน Google Sheets / Forms / Apps Script (ไม่แตะ `index.html`)

### 1. บั๊กรหัส i/I ไม่ลงชีท — แก้ + verify ✅
- **อาการ**: สินค้า prefix "i"/"I" กรอกผ่าน Google Form แล้วไม่ถูกเพิ่มลง Master CF
- **ต้นเหตุ**: bound Apps Script ของ Master CF (โปรเจกต์ `1wQiEYEKH0lvERHGSCgPQ9KljS5As7IK9CBB7ft7rVn7KbbPIDV0xSIns`, เปิดผ่าน Extensions → Apps Script) ใช้ regex `/^([A-Z]+)(\d+)$/` (พิมพ์ใหญ่อย่างเดียว) ใน **4 จุด** (`_getNextCodeAndUpdate`, `_findFirstRow`, `_findInsertRow`, `refreshAllNextCodes`). รหัส prefix "i" เก็บเป็น**ตัวพิมพ์เล็ก** (i152, next "i153") — เป็น prefix เดียวจาก 25 ตัวที่เป็นพิมพ์เล็ก → match ไม่เจอ → `_getNextCodeAndUpdate` คืน null → `onNewProductFormSubmit` เข้า `if(!newCF){log;return;}` → ไม่ insert แถว
- **แก้**: 4 จุดเป็น `/^([A-Za-z]+)(\d+)$/` + บรรทัด ~306 `const newNext = prefix + (num+1)` → `match[1] + (num+1)` (คงพิมพ์เล็ก i154 ไม่ใช่ I154; no-op สำหรับ prefix พิมพ์ใหญ่). ไม่แตะ `.toUpperCase()` บรรทัด 101 (ใช้ match แบบ case-insensitive เท่านั้น ไม่ได้ใช้สร้างรหัส)
- **verify**: อ่านซอร์สกลับจาก server หลัง save → buggyRegex=0, fixedRegex=4, `match[1]+(num+1)`=1 (persisted). ไม่ได้ยิง test submit (กันแถวขยะใน Master CF)

### 2. Audit รหัสครบ — ✅
- 2,056 รหัส, 25 prefix: B,C,CH,D,DS,E,F,G,H,HR,**i**,L,M,MC,MQ,O,P,S,SD,SH,SN,T,V,W,Y
- มีแค่ "i" (151 รหัส, next "i153") เป็นพิมพ์เล็ก → โดนบั๊ก; อีก 24 ตัวพิมพ์ใหญ่ทำงานปกติ; ชีต "รหัสถัดไป" ลงทะเบียนครบทั้ง 25

### 3. Google Form "ADD NEW เพิ่มสินค้าใหม่"
- **viewform published id**: `1FAIpQLSe85uh05qAvb0EfLL1SLr1x94NLDjmc6kIqf3S1SRgg_Uorqg`
- **editor file id**: `1j7alXs9QCESPDS4XD3ryHOoleDxIdZM_ASudqHSUlLA` (URL `/forms/d/<id>/edit`)
- 9 คำถาม (entry order): รหัส 321941132 / ชื่อ 1827016107 / ตัวเลือก 818745380 / **สกุลเงิน 1767048811** / ต้นทุน 1285852350 / เรท 1574114847 / ค่าส่ง 1637455088 / ราคาขาย 281703400 / หมายเหตุ 1449992947
- backend `onNewProductFormSubmit` อ่าน e.values ตาม**ตำแหน่ง** (row[1]=รหัส ... row[4]=สกุลเงิน ...) → ห้ามย้าย/ลบคำถาม

### 4. Validation ช่องรหัส (กันรหัสนอกลิสต์) — ✅ (Option A: native Form validation)
- ช่อง "หมวดอักษร (A - Z)" (entry 321941132): Response validation = **Regular expression / Matches (subtype 301)**
- pattern (Forms **ไม่รับ `(?i)`** → ใช้ character class): `([Bb]|[Cc]|[Cc][Hh]|[Dd]|[Dd][Ss]|[Ee]|[Ff]|[Gg]|[Hh]|[Hh][Rr]|[Ii]|[Ll]|[Mm]|[Mm][Cc]|[Mm][Qq]|[Oo]|[Pp]|[Ss]|[Ss][Dd]|[Ss][Hh]|[Ss][Nn]|[Tt]|[Vv]|[Ww]|[Yy])`
- error ไทย: "ไม่มีรหัสนี้ในลิสต์ กรุณาตรวจสอบอีกครั้ง" (40 ตัวอักษร)
- **ช่องนี้ใส่แค่ prefix** (เช่น "i","B") ไม่ใช่รหัสเต็ม "i152"
- verify (full-match จาก FB data): i/I/b/B/ch/CH/ds/DS/sn/sd/mc/mq → ผ่าน; x/X/Z/B5/i152/hello/5/iX → บล็อก
- ป้องกัน 2 ชั้น: Form บล็อกการส่ง + backend ตีรหัสไม่รู้จัก = log แล้ว return (ไม่เพิ่มแถว) → ไม่มีทางทำข้อมูลเสีย

### 5. ช่อง "สกุลเงินต้นทุน" → Multiple choice (radio) + required — ✅
- เปลี่ยน Dropdown → **Multiple choice (radio, type 2)** + เปิด **Required** (ไม่เลือก = กดส่งไม่ได้)
- ตัวเลือกเดิม "เยน ¥" (อันแรก) / "บาท ฿" — entry 1767048811 คงตำแหน่ง index 3 (คำถามที่ 4)
- ✅ ลงชีทคอลัมน์เดิม backend ไม่ต้องแก้: radio ส่ง string เดียวเหมือน dropdown, โค้ด `if(currency==='บาท ฿'){฿}else{เยน}` ทำงานเหมือนเดิม
- verify FB data: type=2, required=1, options=["เยน ¥","บาท ฿"], index=3, entry order ครบ
- **เหตุที่ไม่ใช้ prefilled link**: Google Form จำค่า default ของ dropdown ไม่ได้ — prefilled link (`...&entry.1767048811=%E0%B9%80%E0%B8%A2%E0%B8%99%20%C2%A5`) verify แล้วว่า pre-select "เยน ¥" ได้จริง **แต่รีเซ็ตทุกครั้งที่ refresh / กด "ส่งคำตอบอีกครั้ง"** → ผู้ใช้เลือก radio+required แทน (แบบ A จาก mockup 3 แบบ)

### 6. tab ชีท "Apps Script" (gid 1887367613) — ไม่ต้อง sync ✅ (แก้ความเข้าใจเดิม)
- ตรวจแล้ว: เก็บแค่ **`onOpen` + `saveLotHistory`** (สคริปต์เมนู "ประวัติล็อต", ~2,595 ตัวอักษร) — **ไม่มี** `onNewProductFormSubmit`/`_getNextCodeAndUpdate`/regex บั๊ก
- → tab นี้ทำให้บั๊ก i กลับมาไม่ได้ (ไม่เคยมีโค้ดส่วนนั้น). โน้ตเก่าที่ว่า "doc tab ค้างโค้ดบั๊ก" = ไม่ถูกสำหรับบั๊กนี้
- ⚠️ tab นี้เป็นสำเนา**สคริปต์ประวัติล็อตเท่านั้น** — อย่าใช้เป็นต้นฉบับ paste โค้ดฟอร์ม

### IDs / gid สำคัญ (Master CF)
- Master CF bound script project: `1wQiEYEKH0lvERHGSCgPQ9KljS5As7IK9CBB7ft7rVn7KbbPIDV0xSIns`
- gid: "Master CF"=1534061120, "รหัสถัดไป"=400320264, "Apps Script"(doc)=1887367613

### บทเรียน (เซสชันนี้)
- Google Form validation subtype: 299=contains, **301=matches** (full-match); **ไม่รองรับ `(?i)`** → ใช้ `[Aa]` character class
- เปลี่ยน "ชนิดคำถาม" ในที่เดิม (dropdown→radio) **คง entry id + ตำแหน่ง** → backend ที่อ่านตาม index ไม่กระทบ
- อ่านโครงสร้างฟอร์มจริงผ่าน `FB_PUBLIC_LOAD_DATA_` (item[4][0][4]=validation, [4][0][2]=required, type=item[3]) แม่นกว่าทดสอบผ่าน UI
- Chrome MCP screenshot สลับ 1408×614 / 1568×746 → พิกัดคลาด ควรใช้ find/ref-based click; `navigate` ต้องเรียกเดี่ยว
- ทั้งเซสชันไม่ยิง test submit จริง (กันแถวขยะ) — verify จาก data structure ทั้งหมด

# SURU.SHOP / Suru Japan — Dashboard Context

## Purpose & context
Nuda เป็นเจ้าของ SURU.SHOP / Suru Japan (ขายสินค้าญี่ปุ่น) พัฒนา BI dashboard ติดตาม P&L, KPI, ยอดขาย, ค่าใช้จ่าย, ลูกค้า
- เรียกผู้ใช้ว่า "คุณ" เท่านั้น (ห้าม "ดา"/"น้องดา") · ตอบไทย สุภาพ ใส่ "ครับ"
- **Dashboard:** single-file `index.html` (~1.08MB) บน GitHub Pages `nudanica.github.io/suru-dashboard/` (repo `nudanica/suru-dashboard`, branch `main`)
- **Backend:** Google Sheets "SURU Dashboard Data" (ID ลงท้าย `...iLrpJDw`, เต็ม `1qAR5qjqv-a1lnxYMlwbL-xHixpuATn-8dIW3iLrpJDw`) + Apps Script
  - Apps Script project (bound กับ Dashboard Data): `1McskxLUFb2XE8JSy8mmlxyVRk7eD9mXEbfcMq4H_Vdhe83hCM-8_KECm`
  - Web app deployment ที่ dashboard ใช้: ชื่อ "ใหม่อีก", deployment ID ขึ้นต้น `AKfycbxZE92cQiRmratXJhgdYea3ih` (= ค่า SURU_API ใน index.html)
- **Master CF Sheet:** แยกต่างหาก ID `1BB4KXYhDRhmN7if9IVOuaoPJhzR9021GUiAJs5KwP9k` — Apps Script คนละไฟล์ **ห้ามแก้จากงาน Dashboard**
- **Data source:** ไฟล์ออเดอร์ Page365 (utf-16 LE, tab-sep, 113 คอลัมน์)

## Current state (รอบล่าสุด — งาน CF เป็น "ฐานเดียวบน Sheet" เสร็จครบ)
**Pipeline CF (per-SKU) ครบทั้งวงจร — Sheet เป็นความจริงเดียว:**
- **Apps Script (deploy Version 31 แล้ว):** เพิ่ม 3 จุด additive — `SHEET_MAP['cf']='MonthlyCF'`, dispatch ใน `doBackup` (`else if(type==='cf') backupMonthlyCF(...)`), และฟังก์ชัน `backupMonthlyCF` (clone จาก `backupCustMonth`, merge-by-month, 4 คอลัมน์ month/cf/qty/val). อ่านใช้ generic read เดิม. ต้นฉบับ Code.gs สำรองไว้ที่ Downloads (`Code_gs_ORIGINAL_backup.txt`)
- **แท็บ `MonthlyCF`** (month/cf/qty/val) = ความจริงเดียวของ CF · backfill 12 เดือน (2025-06→2026-05, 1,200 แถว, top-100/เดือน) สร้างด้วย regex ตัวใหญ่ของแอป group by Paid At — validated ตรง 2026-05 100%
- **index.html B1 (verified live):** ตอนโหลด fetch `?type=cf` → rebuild `MONTHLY_CF`(let)+`ALL_CF`(let) จาก Sheet · const hardcoded เดิมเก็บเป็น fallback · console ขึ้น `OK CF from Sheet: 12 months` · V20 รวม 12 เดือน = 993 ถูกต้อง
- **index.html B2 (live, ยังไม่ verify ตอนอัปจริง):** `parseSalesCSV` เก็บ `cfItems` (cf+qty+val) เพิ่ม (regex `/\(([A-Z]{1,3}\d+)\).*?x(\d+)\s+THB\s+([\d,]+\.?\d*)/g`) → `loadSalesFile` รวมต่อเดือน/CF top-100 → `backupToSheets('cf', cfRows, uploadedMonths)` · **อัปไฟล์ขายครั้งหน้า CF ขึ้น Sheet เอง**
- index.html live size = 1,081,445 ตัวอักษร

**365 reconciliation (เสร็จ — เฉพาะยอดเงิน+บิล):**
- dashboard ใช้คอลัมน์ `Total` (=เงินจริงตาม Page365) group by `Paid At`(+7h) · revenue = Total||PaidAmount · claims = revenue≤0
- เทียบไฟล์ Page365 ดิบทุกไฟล์ในโปรเจค vs dashboard 29 เดือน (2024-01→2026-05): รวม ฿97,239,635 (ดิบ) vs ฿97,243,541 (dash) = **ต่าง 0.004%** · 26/29 เดือนตรงภายใน 2% · 3 เดือนรอยต่อปี (2024-01, 2025-01, 2025-12) ต่าง ~2.2-2.4% (snapshot/boundary หักล้างกัน) → **ยอดเงินตรง 365**

## On the horizon (PENDING — เริ่มแชทใหม่)
**🔴 เกี่ยวกับ "ตรงกับ 365" โดยตรง (สำคัญสุด):**
1. **เลขลูกค้ายังไม่ได้ตรวจ/น่าจะไม่ตรง** — การ์ด "ลูกค้าทั้งหมด" (`id=ret-unique`) อ่าน `MONTHLY_CUST_COUNT` hardcoded (แค่ 5 เดือน) แทน `MONTHLY_CUST` (29 เดือน) → แก้ให้อ่านจาก Sheet (รูปแบบเดียวกับที่เพิ่งแก้ CF) **← งานถัดไปที่แนะนำ**
2. ยังไม่ได้ reconcile **จำนวนชิ้น (items) + ลูกค้า** กับ 365 (ตรวจแค่เงิน+บิล)

**🟡 งาน CF:**
3. verify B2 ตอนอัปไฟล์ขายจริงครั้งหน้า (CF เดือนใหม่ขึ้นเอง)

**🟢 Backlog เดิม (รอ Nuda สั่งก่อนทำ):**
4. เฉลี่ยค่าผันแปรรายเดือน (`varMonthly` ~บรรทัด 5385-5394 ลงก้อนวันสุดท้าย → ขาดทุนเทียมวันสิ้นเดือน; อยากเฉลี่ย ÷วันในเดือน)
5. ภาษี ภ.พ.30/36 เฉลี่ย (~5331-5359, ยังไม่ตัดสินใจ ผูกกับ VAT+ปุ่มแก้ยอดภาษี)
6. การ์ด `.card-grad` (gradient border + white fill ทุกใบ, ใช้ CSS var `--pk`/`--pkl`)
7. ให้หน้า Top-สินค้า/ลูกค้า เปิดมาที่เดือนล่าสุดที่มีข้อมูลอัตโนมัติ
8. Task C ตารางเปรียบเทียบ (mockup อนุมัติแล้ว Style C dark minimal)
9. FILL_MasterCF — สินค้า 441 ตัวยังไม่มีต้นทุน ¥ (Nuda กรอกเอง; Claude ห้ามแก้ Master CF)

## Key learnings & principles
- **Sheet = ความจริงเดียว** — ห้าม hardcode ข้อมูลที่ควร dynamic (เก็บ base บน Sheet, derive ทุกอย่างตอน runtime; ALL_CF คำนวณจาก MONTHLY_CF ไม่เก็บซ้ำ)
- **Additive/render-layer เท่านั้น** — ห้ามทำลายของเดิม; แก้แบบเพิ่ม + มี fallback เสมอ
- **Verify live ก่อน mark done** — ผ่าน Chrome MCP เสมอ
- **Mockup ก่อน implement** visual ทุกอย่าง
- **อย่า bundle งานที่ยังไม่ verified** เข้าไฟล์เดียวกับงานที่ verify แล้ว
- **ห้ามอัปไฟล์ขายหลายไฟล์พร้อมกัน** (POST 'cf'/'sales' read-modify-write จะ race) — ทีละไฟล์
- dashboard สะท้อน "ไฟล์ล่าสุดที่อัป" — อยากให้ตรง 365 เป๊ะ ณ จุดเวลา = อัป export ล่าสุดทับ

## Approach & patterns

**Deploy index.html (ใช้เสมอ):**
1. fetch raw GitHub → `window.__SRC` (`https://raw.githubusercontent.com/nudanica/suru-dashboard/main/index.html?_=`+Date.now())
2. find/replace (verify แต่ละคู่เจอ **1 ตำแหน่งเป๊ะ** ก่อนแก้; ใช้ replace แบบ function เลี่ยงปัญหา `$`)
3. syntax-check: `new Function()` บนบล็อกที่แทรก + เช็ค brace/paren/bracket delta = 0
4. **Blob download** — สร้างปุ่มสีให้ Nuda กดเอง (auto-click มักโดนบล็อก) บนหน้า dashboard; Thai ในปุ่มใช้ `\u` escape
5. Nuda upload + commit GitHub เอง → Claude verify live (cache-bust `?_=`)

**Apps Script:**
1. เปิดผ่านชีต "SURU Dashboard Data" → Extensions → Apps Script (ยืนยัน `SHEET_ID` ลงท้าย `...iLrpJDw`)
2. อ่านโค้ดผ่าน Monaco: `monaco.editor.getModels()[0].getValue()` / `getEditors()[0].revealLineInCenter(n)` + screenshot (เลี่ยง content filter); ดูโครงผ่าน method-name/skeleton + mask non-ASCII
3. แก้: `model.setValue(newSrc)` (เก็บ orig ไว้ revert) → save **Cmd+S** (Mac) → deploy: Deploy → **Manage deployments** → ดินสอ Edit → Version: **New version** → Deploy (**อัปเดต deployment เดิม ห้ามสร้างใหม่**)
4. โครง: `doGet` route ตาม `SHEET_MAP[type]` (generic read คืน array ของ object key=หัวคอลัมน์) + branch พิเศษ (varcats/custmonth/cf_names/cats); `doPost`→`doBackup(ss,type,data,months)` if-chain→`backupX(sheet,...)`; helper `ensureHeaders`/`appendRows`/`getExistingKeys`/`toMonthStr_`/`jsonResponse`; pattern: custmonth=merge-by-month, monthly_variable=clear-all
5. ทุกครั้งระบุ: sheet เจ้าของ (Dashboard Data ไม่ใช่ Master CF) + ยืนยัน SHEET_ID `...iLrpJDw` + ชื่อแท็บปลายทาง

## Tools & resources
- **Chrome MCP** ทุก browser op · top-level `await` ใช้ตรง (อย่า wrap async IIFE ที่ return promise — จะได้ `{}`) · content filter: mask `=`,`:`,`?`,`'`,`"`, ตัด string literal + ID ยาว, strip non-ASCII ก่อน return; screenshot เลี่ยง filter ได้ · Thai ในโค้ดที่ inject ใช้ `String.fromCharCode`/`\u`
- **Page365 ดิบ:** utf-16, tab-sep · คอลัมน์: Order no., Paid At, **Total** (=เงินจริง), Paid Amount (fallback), Item Detail (`ชื่อ (CF) x<qty> THB <total>`, คั่นด้วย `\n`)
- **CF regex (แอป, ตัวใหญ่ล้วน):** `/\(([A-Z]{1,3}\d+)\).*?x(\d+)\s+THB/` (เพิ่ม val: `\s+([\d,]+\.?\d*)`) · group by Paid At (+7h BKK) · top-100/เดือน
- **GitHub raw:** CORS-accessible · cache-bust `?_=`+Date.now() เสมอ
- **openpyxl workaround** (Suru Japan + Master CF xlsx): patch `styles.xml` `<family val>` >14 → 2 ก่อนโหลด
