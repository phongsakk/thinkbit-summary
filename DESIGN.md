# UI Design — เอกสารคู่มือ (HTML → PDF)

ออกแบบหน้าเอกสารคู่มือให้ดูเป็นราชการ  
อ่านบนเว็บแล้วใกล้เคียงกระดาษ A4 พิมพ์ PDF แล้วยังคม อ่านง่าย ไม่ขาดหัวตาราง

---

## 1. หลักการ


| ข้อ        | กำหนด                                                                               |
| ---------- | ----------------------------------------------------------------------------------- |
| กระดาษ     | A4 (`210mm × 297mm`) แนวตั้ง                                                        |
| ฟอนต์      | **TH Sarabun New** (ฟอนต์ราชการ) ตัวสำรอง `Sarabun`                                 |
| ตัวหนังสือ | สีดำ `#111111` พื้นขาว ไม่พึ่งสีในการสื่อความ                                       |
| เส้น       | ตาราง/กรอบยังดำบาง — เส้นใต้หัวข้อและเส้นกัน section ใช้สี theme (`--section`)     |
| สี         | ใช้เท่าที่จำเป็น — ตารางและกล่อง warning ใช้พื้น theme อ่อน **ห้ามเข้ม**           |
| พิมพ์      | ออกแบบให้สวยแม้ปิด “Background graphics”                                            |
| เน้นคำ     | ตัวหนา (`font-weight: 700`) **ห้าม** กรอบ/พื้นแบบ badge รอบ `POST`, ชื่อฟิลด์, รหัส |
| ลิงก์      | ลิงก์ทั่วไป — สีลิงก์ + ขีดเส้นใต้ ห้ามทำให้เหมือนข้อความธรรมดา                     |
| ภาษา       | คำที่เป็น tech scope ใช้ภาษาอังกฤษตามระบบ **ไม่ทับศัพท์** เช่น environment, endpoint, token, revoke, rollback, rotate |
| คำถาม      | หัวข้อหรือประโยคที่เป็นคำถามลงท้ายด้วย `?`                                          |


อย่าใช้เงา, มุมโค้งใหญ่, การ์ดสี, ปุ่ม, แท็บ

ตัวอย่าง CSS ในเอกสารนี้คัดลอกใส่ `<style>` ใน HTML ฉบับนั้น **ไม่สร้างไฟล์ `.css` แยก**  
ส่งให้ผู้อ่านได้ไฟล์ `.html` เดียว เปิดแล้วใช้ได้ ไม่ต้องแนบ stylesheet

---

## 2. Token

เอกสารคู่สัญญา / ราชการ (`CLIENT_SPECT`):

```css
:root {
  --font: "TH Sarabun New", "Sarabun", "Angsana New", "Tahoma", sans-serif;
  --ink: #111111;
  --mute: #444444;
  --rule: #111111;
  --rule-soft: #b0b0b0;
  --fill: #eceff3;
  --row: #f5f7f9;
  --warn: #eceff3;
  --paper: #ffffff;
  --desk: #cfc9c0;
  --navy: #1b365d;
  --section: #1b365d;
  --note: #f7f7f7;
  --link: #0563c1;

  --fs-cover: 22pt;
  --fs-h1: 18pt;
  --fs-h2: 16pt;
  --fs-body: 14pt;
  --fs-small: 11pt;
  --fs-meta: 10pt;

  --lh: 1.45;
}
```

เอกสาร Thinkbit ภายใน (`API_SPECT`) — สีจากโลโก้ THINKBIT:

```css
:root {
  --font: "TH Sarabun New", "Sarabun", "Angsana New", "Tahoma", sans-serif;
  --ink: #010025;
  --mute: #3d3d55;
  --rule: #010025;
  --rule-soft: #b8b8c8;
  --fill: #ecf6f9;
  --row: #f6fafc;
  --warn: #f6eef3;
  --paper: #ffffff;
  --desk: #d9e4ee;
  --navy: #010025;
  --section: #3363c2;
  --note: #f5f8fb;
  --link: #3363c2;
  --cyan: #62b2ce;
  --purple: #5f2992;
  --magenta: #a3286b;

  --fs-cover: 22pt;
  --fs-h1: 18pt;
  --fs-h2: 16pt;
  --fs-body: 14pt;
  --fs-small: 11pt;
  --fs-meta: 10pt;

  --lh: 1.45;
}
```

ขนาดตัวอักษรใช้ **pt** ทั้งจอและพิมพ์ เพื่อให้ PDF ได้สเกลเดียวกับเอกสารราชการ

---

## 3. หน้ากระดาษและการพิมพ์

```css
@page {
  size: A4 portrait;
  margin: 18mm 18mm 20mm 22mm;
}

@media print {
  html, body {
    background: #fff !important;
    color: var(--ink);
  }
  .document {
    width: auto;
    min-height: 0;
    margin: 0;
    padding: 0;
    box-shadow: none;
    border: 0;
  }
  a { color: var(--link); text-decoration: underline; }
  .no-print { display: none !important; }
}
```

ระยะขอบ: ซ้าย `22mm` (เข้าเล่ม) อื่น ๆ `18–20mm`  
ความกว้างเนื้อหาที่ใช้ได้ประมาณ `170mm`

---

## 4. โครง HTML

```html
<!doctype html>
<html lang="th">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>ค้นหาไวน์และราคาแนะนำขายเบื้องต้น — ฉบับผู้เรียก</title>
  <style>
    /* คัดลอก CSS ทั้งฉบับจากข้อ 2 เป็นต้นไป ใส่ที่นี่ ห้ามแยกไฟล์ */
    @import url("https://fonts.googleapis.com/css2?family=Sarabun:wght@400;700&display=swap");
    :root { /* ... */ }
  </style>
</head>
<body>
  <article class="document">
    <header class="doc-head">
      <p class="org">กรมสรรพสามิต</p>
      <p class="doc-type">เอกสารคู่มือ · ฉบับผู้เรียก</p>
      <h1 class="doc-title">ค้นหาไวน์และราคาแนะนำขายเบื้องต้น</h1>
      <p class="doc-sub">Wine Search API Specification</p>
      <table class="meta">
        <tr>
          <th>เลขที่เอกสาร</th><td>WINE-SEARCH-EXCISE-SPEC</td>
          <th>วันที่</th><td>20 สิงหาคม 2569</td>
        </tr>
        <tr>
          <th>รุ่น</th><td>1.0</td>
          <th>ชั้นความลับ</th><td>ใช้ภายใน</td>
        </tr>
      </table>
    </header>

    <nav class="toc">
      <h2>สารบัญ</h2>
      <ol>
        <li><a href="#s1">วัตถุประสงค์</a></li>
        <li><a href="#s2">การเข้าถึงระบบ</a></li>
      </ol>
    </nav>

    <section id="s1">
      <h2>1. วัตถุประสงค์</h2>
      <p>...</p>
    </section>
  </article>
</body>
</html>
```

ลำดับบนหน้าแรกต้องเป็น: **หน่วยงาน → ประเภทเอกสาร → ชื่อเรื่อง → ตารางควบคุมเอกสาร → สารบัญ → เนื้อหา**

ห้าม:

- `doc.css`, `style.css`, หรือไฟล์ CSS อื่น
- `<link rel="stylesheet" href="...">` — ฟอนต์ใช้ `@import` ใน `<style>` เดียวกัน ไม่มีข้อยกเว้นแยกไฟล์ CSS
- inline `style=""` กระจายตามแท็ก ถ้าทำเป็นคลาสใน `<style>` ได้

---

## 5. หน้าจอ (พรีวิวกระดาษ)

บนเว็บให้เห็นเป็นแผ่น A4 กลางพื้นโต๊ะ ไม่ยืดเต็มจอ

```css
* { box-sizing: border-box; }

html { font-size: 14pt; }

body {
  margin: 0;
  font-family: var(--font);
  color: var(--ink);
  background: var(--desk);
  line-height: var(--lh);
}

a {
  color: var(--link);
  text-decoration: underline;
}

.document {
  width: 210mm;
  min-height: 297mm;
  margin: 16px auto 48px;
  padding: 18mm 18mm 20mm 22mm;
  background: var(--paper);
  color: var(--ink);
  box-shadow: 0 0 0 1px #bbb, 0 12px 40px rgba(0, 0, 0, 0.18);
}

h2, h3 {
  page-break-after: avoid;
}
```

---

## 6. ส่วนหัวเอกสาร

```css
.doc-head {
  text-align: center;
  border-bottom: 3px double var(--section);
  padding-bottom: 12pt;
  margin-bottom: 16pt;
}

.org {
  margin: 0;
  font-size: 14pt;
  font-weight: 700;
  letter-spacing: 0.12em;
  color: var(--navy);
}

.doc-type {
  margin: 2pt 0 8pt;
  font-size: var(--fs-small);
}

.doc-title {
  margin: 0 0 4pt;
  font-size: var(--fs-cover);
  font-weight: 700;
  line-height: 1.3;
}

.doc-sub {
  margin: 0 0 12pt;
  font-size: 14pt;
  color: var(--mute);
}

.meta {
  width: 100%;
  border-collapse: collapse;
  text-align: left;
  font-size: var(--fs-small);
}

.meta th {
  width: 18%;
  background: var(--fill);
  font-weight: 700;
}

.meta th, .meta td {
  border: 0.6pt solid var(--rule);
  padding: 4pt 8pt;
}
```

ถ้ามีตรากรม: วาง `<img class="emblem">` กึ่งกลาง **เหนือ** ชื่อหน่วยงาน สูง `18mm` พื้นโปร่ง

```css
.emblem { display: block; height: 18mm; width: auto; margin: 0 auto 8pt; }
```

---

## 7. หัวข้อและเนื้อหา

เลขหัวข้อเป็นตัวเลขราชการ: `1.` `1.1` `1.1.1` ไม่ใช้หัวข้อลอย

```css
h2 {
  font-size: var(--fs-h1);
  font-weight: 700;
  margin: 18pt 0 8pt;
  padding-bottom: 3pt;
  border-bottom: 0.75pt solid var(--section);
  page-break-after: avoid;
  break-after: avoid;
  break-inside: avoid;
}

section > h2 {
  break-before: page;
  page-break-before: always;
}

h3 {
  font-size: var(--fs-h2);
  font-weight: 700;
  margin: 14pt 0 6pt;
}

p { margin: 0 0 8pt; font-size: var(--fs-body); text-align: justify; }

ul, ol { margin: 0 0 10pt; padding-left: 22pt; }
li { margin-bottom: 3pt; }

ul { list-style: none; }
ul > li { position: relative; }
ul > li::before {
  content: "■";
  position: absolute;
  left: -16pt;
  top: 0;
  height: calc(1em * var(--lh));
  display: flex;
  align-items: center;
  font-size: 0.85em;
  line-height: 1;
  color: var(--section);
}

.toc {
  margin: 0 0 18pt;
  page-break-after: always;
}
.toc ol { padding-left: 22pt; }
```

ลิงก์ในสารบัญและเนื้อหาใช้สไตล์ลิงก์ทั่วไป (ดูข้อ 10.1) ห้าม `color: inherit; text-decoration: none`

สารบัญขึ้นหน้าใหม่หลังจบ (`page-break-after: always`)  
หัวข้อหลักในเนื้อหา (`section > h2`) ขึ้นหน้าใหม่ตอนพิมพ์ หัวข้อย่อย `h3` ไม่ขึ้นหน้าใหม่

อย่าใส่ `page-break-before` ที่ `.toc h2` จะแยกปกออกจากสารบัญโดยไม่จำเป็น

ย่อหน้าแรกของแต่ละข้อไม่ย่อหน้าพิเศษ  
ถ้าต้องการย่อหน้าแบบหนังสือราชการ: `text-indent: 2em` เฉพาะ `p` ในเนื้อหา ไม่ใช้กับตารางและรายการ

---

## 8. ตาราง

ตารางเป็นองค์ประกอบหลักของคู่มือ ต้องพิมพ์ครบเส้น และหัวตารางซ้ำเมื่อข้ามหน้า

```html
<table>
  <thead>
    <tr>
      <th>ฟิลด์</th>
      <th>ชนิด</th>
      <th>บังคับ</th>
      <th>คำอธิบาย</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>Name</code></td>
      <td>string</td>
      <td>ใช่</td>
      <td>ชื่อไวน์</td>
    </tr>
  </tbody>
</table>
```

```css
table {
  width: 100%;
  border-collapse: collapse;
  font-size: 12pt;
  margin: 0 0 12pt;
}

th, td {
  border: 0.6pt solid var(--rule);
  padding: 5pt 8pt;
  vertical-align: top;
  text-align: left;
}

thead th {
  background: var(--fill);
  font-weight: 700;
  text-align: center;
}

tbody tr:nth-child(even) { background: var(--row); }

thead { display: table-header-group; }
tr { page-break-inside: avoid; }
```


| กฎ                                      | ทำไม?                                                 |
| --------------------------------------- | ----------------------------------------------------- |
| มี `<thead>` เสมอ                       | พิมพ์หน้าถัดไปแล้วยังมีหัวคอลัมน์                     |
| พื้นตารางเป็นสี theme อ่อนเท่านั้น      | `--fill` หัวตาราง, `--row` แถวคู่ — ห้ามเข้ม ตัวหนังสือดำ |
| คอลัมน์รหัสใช้ `<code>` แล้ว **ตัวหนา** | แยกจากคำอธิบาย — ห้ามกรอบแบบ badge                    |
| ตารางกว้างเกิน                          | ลดเหลือ 12pt หรือแยกตาราง อย่าให้เลื่อนแนวนอนตอนพิมพ์ |


---

## 9. กล่องหมายเหตุ / ตัวอย่าง / คำเตือน

ใช้หลักการเดียวกับตาราง: พื้น theme อ่อน ตัวหนังสือดำ เส้นซ้ายเป็น accent  
**ห้าม** พื้นเข้ม และห้ามตัวหนังสือสีอ่อนบนพื้นสี

- หมายเหตุทั่วไป — พื้น `--row` (โทนเดียวกับแถวคู่)
- warning — พื้น `--warn` (โทน theme อ่อน) + เส้นซ้าย accent

```html
<aside class="callout">
  <p class="label">หมายเหตุ</p>
  <p>ต้องส่ง Access Token ทุกครั้ง ไม่มี token จะได้รหัส 401 และไม่ค้นข้อมูล</p>
</aside>

<aside class="callout warn">
  <p class="label">ข้อควรระวัง</p>
  <p><code>Avb</code> ต้องเป็นตัวเลข เช่น 14.5 ไม่ใช่ "14.5"</p>
</aside>
```

```css
.callout {
  border: 0.6pt solid var(--rule);
  border-left: 4pt solid var(--navy);
  padding: 8pt 10pt;
  margin: 0 0 12pt;
  page-break-inside: avoid;
  background: var(--row);
}

.callout.warn {
  background: var(--warn);
  border-left-color: var(--navy);
}

.callout .label {
  margin: 0 0 4pt;
  font-size: var(--fs-small);
  font-weight: 700;
  letter-spacing: 0.04em;
}
```

เอกสาร Thinkbit: `.callout.warn { border-left-color: var(--magenta); }` — เส้นซ้ายเป็น accent เส้นบาง พื้นยังอ่อน

---

## 10. โค้ดและตัวอย่าง HTTP

```html
<pre><code>POST /cloud/apiv2-Winesearch_Excise HTTP/1.1
Host: api-taitaxes.excise.go.th
Authorization: Bearer <access_token></code></pre>
```

```css
a {
  color: var(--link);
  text-decoration: underline;
}

code {
  font-family: inherit;
  font-size: inherit;
}

p code, td code, li code, th code {
  font-weight: 700;
  border: 0;
  padding: 0;
  background: none;
}

pre code {
  font-family: "Sarabun", "Courier New", monospace;
  font-size: 11pt;
  font-weight: 400;
  border: 0;
  padding: 0;
}
```

คำในเนื้อหาเช่น method `POST`, ชื่อฟิลด์ `Name` ใช้ตัวหนาอย่างเดียว  
บล็อกตัวอย่าง HTTP/JSON (`<pre>`) ยังเป็นโมโนสเปซ ไม่ต้องหนาทั้งก้อน

ลิงก์เป็นลิงก์ทั่วไป (`var(--link)` ขีดเส้นใต้) ทั้งสารบัญและเนื้อหา ห้ามทำให้เหมือนตัวหนังสือธรรมดา

```css
pre {
  border: 0.6pt solid var(--rule);
  padding: 10pt 12pt;
  margin: 0 0 12pt;
  background: #fff;
  white-space: pre-wrap;
  word-break: break-word;
  font-size: 11pt;
  line-height: 1.35;
}
```

`white-space: pre-wrap` กันโค้ดล้นขอบกระดาษตอนพิมพ์

---

## 11. ตัดหน้า PDF

```css
section > h2 { break-before: page; page-break-before: always; }
h2, h3 { break-after: avoid; break-inside: avoid; }
.callout, .meta { break-inside: avoid; }
tr { page-break-inside: avoid; }

p, li {
  orphans: 3;
  widows: 3;
}
```


| ใช้เมื่อ                  | class / กฎ                                      |
| ------------------------- | ----------------------------------------------- |
| หัวข้อหลักขึ้นหน้าใหม่    | `section > h2 { break-before: page; }`          |
| ห้ามหัวข้อค้างท้ายหน้า    | `h2, h3 { break-after: avoid; }`                |
| ห้ามตารางขาดกลางแถว       | `tr { page-break-inside: avoid; }`              |
| ห้ามกล่องหมายเหตุขาดครึ่ง | `.callout { break-inside: avoid; }`             |


อย่าใส่ `page-break-before` ที่หัวข้อย่อย `h3` หรือที่ `.toc h2` — จะได้หน้าว่างเกิน / แยกปกออกจากสารบัญ

---

## 12. เลขหน้า (พิมพ์ PDF)

เบราว์เซอร์ทั่วไปไม่พิมพ์เลขหน้าจาก CSS ให้อัตโนมัติ  
ใส่ท้ายเอกสารบนจอ และใช้ Paged.js ตอนส่งออก PDF

```html
<script src="https://unpkg.com/pagedjs/dist/paged.polyfill.js"></script>
```

```css
@page {
  size: A4 portrait;
  margin: 18mm 18mm 20mm 22mm;

  @top-center {
    content: "ค้นหาไวน์และราคาแนะนำขายเบื้องต้น · ฉบับผู้เรียก";
    font-family: "TH Sarabun New", Sarabun, sans-serif;
    font-size: 10pt;
    color: #444;
  }

  @bottom-left {
    content: "WINE-SUMMIT-SPEC-001";
    font-size: 10pt;
  }

  @bottom-right {
    content: "หน้า " counter(page) " / " counter(pages);
    font-size: 10pt;
  }
}

.pagedjs_margin-top {
  border-bottom: 0.4pt solid var(--section);
}
```

หน้าแรก (ปก) ไม่ต้องมีหัววิ่ง:

```css
@page :first {
  @top-center { content: none; }
}
```

ถ้าไม่ใช้ Paged.js: วางท้ายเรื่องด้วยข้อความ `พิมพ์จากระบบเอกสาร · ตรวจเลขหน้าตอนส่งออก` และใช้เลขหน้าของไดอะล็อกพิมพ์

---

## 13. ฟอนต์ราชการ

ใช้ **TH Sarabun New** ไฟล์ `.ttf` ของหน่วยงานเป็นหลัก  
ถ้าไม่มี ให้โหลด Sarabun จาก Google Fonts เป็นตัวสำรอง **ผ่าน `@import` ใน `<style>` เดียวกัน** ห้ามเพิ่ม `<link rel="stylesheet">`

```css
@import url("https://fonts.googleapis.com/css2?family=Sarabun:wght@400;700&display=swap");
```

`@font-face` ก็ใส่ใน `<style>` เดียวกัน ถ้ามีไฟล์ `.ttf` ของหน่วยงานให้อ้าง `url("fonts/...")` ได้ แต่ **ห้ามแยก CSS**

```css
@font-face {
  font-family: "TH Sarabun New";
  src: url("fonts/THSarabunNew.ttf") format("truetype");
  font-weight: 400;
  font-display: swap;
}
@font-face {
  font-family: "TH Sarabun New";
  src: url("fonts/THSarabunNew-Bold.ttf") format("truetype");
  font-weight: 700;
  font-display: swap;
}
```

ตัวหนาใช้ weight `700` เท่านั้น ไม่ใช้ตัวเอียงกับเนื้อหาภาษาไทย

---

## 14. สีที่อนุญาต

ห้ามไล่สีบนหน้าเอกสาร ห้ามพื้นเข้ม ห้ามตัวหนังสือสีอ่อนบนพื้นสี  
ขาวดำอย่างเดียวยังอ่านครบ = ผ่าน

### 14.1 คู่สัญญา / ราชการ (`CLIENT_SPECT`)


| ใช้         | ค่า               | ที่                  |
| ----------- | ----------------- | -------------------- |
| หมึก        | `#111111`         | ตัวหนังสือทั้งหมด    |
| กรมท่า      | `#1B365D`         | ชื่อหน่วยงาน, เส้นใต้หัวข้อ, เส้นกัน section (`--section`) |
| ลิงก์       | `#0563C1`         | สารบัญและ URL        |
| หัวตาราง    | `#ECEFF3`         | `th` (`--fill` โทนกรมท่าอ่อน) |
| แถวคู่      | `#F5F7F9`         | `tbody` even และ callout (`--row`) |
| warning     | `#ECEFF3`         | `callout.warn` (`--warn`) |
| เส้นตาราง   | `#111111` `0.6pt` | ตาราง เส้นคั่น `pre` |
| พื้นกระดาษ  | `#FFFFFF`         | เอกสาร               |


### 14.2 Thinkbit ภายใน (`API_SPECT`)

สกัดจากโลโก้ THINKBIT (wordmark กรมท่าเข้ม + ribbon B ไล่ cyan → royal → purple → magenta)


| ชื่อ       | ค่า       | ที่มา / ใช้ในเอกสาร                                      |
| ---------- | --------- | -------------------------------------------------------- |
| Wordmark   | `#010025` | ตัวอักษร THINKBIT — หมึก, เส้นตาราง, ชื่อหน่วยงาน        |
| Cyan       | `#62B2CE` | พิกเซลซ้ายของไอคอน — accent อ่อน (อย่าใช้เป็นตัวหนังสือ) |
| Royal      | `#3363C2` | กลางตัว B — ลิงก์, เส้นใต้หัวข้อ, เส้นกัน section (`--section`) |
| Blue deep  | `#2141CC` | เงาไอคอน — ไม่ใช้เป็นพื้น                                |
| Purple     | `#5F2992` | โค้งบน — เส้น callout สำรอง                              |
| Magenta    | `#A3286B` | โค้งล่าง — เส้นซ้าย `callout.warn` เท่านั้น ไม่ใช้เป็นพื้น |
| หัวตาราง   | `#ECF6F9` | `th` (`--fill` โทน cyan อ่อน)                            |
| แถวคู่     | `#F6FAFC` | `tbody` even และ callout (`--row`)                       |
| warning    | `#F6EEF3` | `callout.warn` (`--warn` โทน magenta อ่อน)               |
| พื้นกระดาษ | `#FFFFFF` | เอกสาร                                                   |


ในหน้าใช้สีทึบทีละค่า ตาม token ข้อ 2 **ห้าม** ใส่ gradient ของโลโก้ลงเอกสาร

---

## 15. ความกว้างคอลัมน์ตาราง (แนะนำ)

ตาราง 4 คอลัมน์มาตรฐานของคู่มือ API

```css
.col-name { width: 22%; }
.col-type { width: 14%; }
.col-req  { width: 12%; }
.col-desc { width: auto; }
```

ตาราง 2 คอลัมน์ (คำอธิบายทั่วไป): ซ้าย `32%` ขวาที่เหลือ

---

## 16. ปุ่มพิมพ์ (จอเท่านั้น)

```html
<p class="no-print toolbar">
  <button type="button" onclick="window.print()">พิมพ์ / บันทึกเป็น PDF</button>
</p>
```

```css
.toolbar { text-align: right; margin: 0 0 12pt; }
.toolbar button {
  font: 14pt var(--font);
  padding: 4pt 12pt;
  border: 0.6pt solid var(--rule);
  background: #fff;
  cursor: pointer;
}
```

ตั้งค่าไดอะล็อกพิมพ์: กระดาษ A4, ขอบ Default, เปิด Headers/Footers ตามเบราว์เซอร์ถ้าไม่ใช้ Paged.js

---

## 17. รายการตรวจก่อนส่ง PDF

- [ ] **ไฟล์เดียว** — CSS ทั้งอยู่ใน `<style>` ไม่มี `doc.css` / ไม่มี `<link rel="stylesheet">`
- [ ] ฟอนต์ TH Sarabun New / Sarabun ทั้งฉบับ ไม่มีฟอนต์ปน
- [ ] หน้าแรกมีหน่วยงาน ชื่อเรื่อง เลขที่ วันที่ รุ่น
- [ ] หัวข้อเป็น `1.` `1.1` ไม่ใช่ขนาดตัวอักษรอย่างเดียว
- [ ] ตารางมีเส้นครบ มี `thead` หัวซ้ำเมื่อข้ามหน้า
- [ ] โค้ดไม่ล้นขวา (`pre-wrap`)
- [ ] ไม่มีเงา / มุมโค้ง / กล่องสีสด
- [ ] ขาวดำอ่านได้ทั้งฉบับ
- [ ] ไม่มีหัวข้อค้างท้ายหน้า ตามด้วยเนื้อหาหน้าใหม่
- [ ] เลขหน้า มุมขวาล่าง เมื่อใช้ Paged.js
- [ ] ขอบซ้ายกว้างพอเข้าเล่ม