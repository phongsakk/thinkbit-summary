# ค้นหาไวน์และราคาแนะนำขายเบื้องต้น

Wine Search API Specification · ฉบับผู้เรียก

API สำหรับค้นหาข้อมูลไวน์ และราคาแนะนำขายเบื้องต้น (บาท) จากชื่อสินค้า หรือรูปฉลาก

| Environment | Method | URL | Content-Type |
|---|---|---|---|
| UAT | `POST` | `https://api-taitaxes.excise.go.th/uat/cloud/apiv2-Winesearch_Excise` | `application/json` |
| Production | `POST` | `https://api-taitaxes.excise.go.th/cloud/apiv2-Winesearch_Excise` | `application/json` |

Access Token **คนละ token ต่อ environment** — token ของ UAT ใช้กับ Production ไม่ได้

เรียกเฉพาะ URL ในตารางนี้ ตาม environment ที่ใช้งาน

---

## 1. Access Token

ต้องส่ง Access Token ทุกครั้งที่เรียก API

```
Authorization: Bearer <access_token>
```

ไม่มี token หรือ token ไม่ถูกต้อง **จะไม่ค้นข้อมูล** และได้ `401`

### ได้ token อย่างไร?

ผู้มีสิทธิ์ออก Access Token คือ **ผู้ดูแลระบบ** และ/หรือ **ผู้พัฒนาระบบ** ผู้เรียกออกเองไม่ได้

1. ติดต่อผู้ดูแลระบบ หรือผู้พัฒนาระบบ เพื่อขอ Access Token
2. ผู้ออกสิทธิ์ออกให้ **หนึ่ง token ต่อ environment** (UAT คนละ token กับ Production) แล้วส่งค่า token มาทางช่องทางที่ตกลงกัน
3. เก็บ token ไว้ฝั่งระบบผู้เรียก (environment / secret store) — **ห้ามฝังในโค้ดที่เผยแพร่ หรือส่งต่อให้บุคคลอื่น**
4. นำค่าที่ได้ไปใส่ header `Authorization: Bearer ...` ทุก request

ไม่มีการลงทะเบียนหรือออก token ผ่าน URL ค้นไวน์นี้

### อายุการใช้งาน

Access Token **ไม่มีวันหมดอายุในตัว** — ออกให้แล้วใช้ต่อเนื่องได้จนกว่าผู้ดูแลระบบ หรือผู้พัฒนาระบบจะแจ้งให้เปลี่ยน

ขอ token ใหม่เมื่อ:

- ได้รับแจ้งให้ rotate / เปลี่ยน token
- เรียก API แล้วได้ `401` ทั้งที่ส่ง token ครบ

| กรณี | HTTP | Body (text) |
|---|---|---|
| ไม่ส่ง `Authorization` | `401` | `You are not authenticate !` |
| Token ไม่ถูกต้อง | `401` | `Invalid Token` |
| Token ใช้ไม่ได้ | `401` | `You are Not authenticate !!!` |

### ตัวอย่างการส่ง

```http
POST /cloud/apiv2-Winesearch_Excise HTTP/1.1
Host: api-taitaxes.excise.go.th
Authorization: Bearer <access_token>
Content-Type: application/json
```

UAT ใช้ path `/uat/cloud/apiv2-Winesearch_Excise` และ token ของ UAT

---

## 2. Request

ห้ามส่ง field นอกตารางนี้

| Field | Type | Required | Description |
|---|---|---|---|
| `ReqNo` | string | yes | เลขที่คำขอของฝั่งผู้เรียก ใช้จับคู่ request/response |
| `Name` | string | yes | ชื่อไวน์ — ถ้าเป็น `""` (string ว่าง) และมี `Piclabel` จะค้นจากรูปฉลาก |
| `CategoryName` | string | yes | กลุ่ม/ประเภท ตามที่ฝั่งผู้เรียกจัดไว้ — ไม่มี master data และไม่มีรายการค่าที่ต้องตรง |
| `Vintage` | string | yes | ปี เช่น `"2018"` หรือ `"NV"` |
| `BottleSize` | string | yes | ขนาดขวด — ดูค่าที่รองรับด้านล่าง |
| `Avb` | number | yes | แอลกอฮอล์ (%) ต้องเป็นตัวเลข ไม่ใช่ string |
| `Country` | string | yes | ประเทศ |
| `Region` | string | yes | ภูมิภาค |
| `Piclabel` | string | no | รูปฉลากเป็น base64 ของไฟล์รูป (jpeg / png) — ใช้เมื่อ `Name` เป็น `""` (string ว่าง) |

`CategoryName` ส่งตามการจัดกลุ่มของฝั่งผู้เรียกได้เลย ไม่มีรายการกลางให้เลือก และไม่ต้องตรงกับค่าที่กำหนดไว้ล่วงหน้า ใช้เป็นข้อความกำกับคำขอ

`Piclabel` ต้องเป็น string ของรูปแบบ **base64** ส่งได้ 2 แบบ: raw base64 เช่น ขึ้นต้น `/9j/` (JPEG) หรือ data URI `data:image/jpeg;base64,...` / `data:image/png;base64,...` ห้ามส่ง URL รูป และห้ามส่ง binary ใน JSON

### `BottleSize` ที่รองรับ

| ค่าที่ส่ง | ความหมาย |
|---|---|
| `375ml` หรือ `0.375` | Half Bottle (375ml) |
| `750ml` หรือ `0.750` | Bottle (750ml) |
| `1500ml` หรือ `1.500` | Magnum (1.5L) |
| `3000ml` หรือ `3.000` | Double Magnums (3L) |
| ค่าอื่น | ใช้ค่าที่ส่งมาตรงๆ |

---

## 3. Response

สำเร็จทั่วไปเป็น JSON

```json
{
  "Success": true,
  "Message": "",
  "ReqNo": "",
  "Query": "",
  "Vintage": "",
  "Size": "",
  "Data": [],
  "Suggestions": []
}
```

| Field | Type | Description |
|---|---|---|
| `Success` | boolean | ผลโดยรวม |
| `Message` | string | ข้อความสถานะ (ภาษาไทย) |
| `ReqNo` | string | ค่าเดียวกับที่ส่งไป |
| `Query` | string | ชื่อที่ใช้ค้น (`Name`) |
| `Vintage` | string | ปีจาก request |
| `Size` | string | `BottleSize` ตามที่ส่งมา |
| `Data` | array | ผลหลัก |
| `Suggestions` | array | รายการใกล้เคียง |

### `Data[]`

| Field | Type | Description |
|---|---|---|
| `Name` | string | ชื่อไวน์ |
| `Pic` | string | URL รูป (ว่างได้) |
| `CategoryName` | string | ประเภท |
| `Country` | string | ประเทศ |
| `Region` | string | ภูมิภาค |
| `BottleSize` | string | ขนาดขวด (อาจถูกแปลงเป็นชื่อเต็ม เช่น `Bottle (750ml)`) |
| `Avb` | number | แอลกอฮอล์ (%) |
| `Vintage` | string | ปี |
| `RecommendPrice` | number | ราคาแนะนำ (บาท) |
| `RecommendMaxPrice` | number | ราคาสูงสุด (บาท) |
| `RecommendMinPrice` | number | ราคาต่ำสุด (บาท) |

### `Suggestions[]`

| Field | Type | Description |
|---|---|---|
| `Name` | string | ชื่อไวน์ |
| `Pic` | string | URL รูป |
| `CategoryName` | string | ประเภท |
| `Country` | string | ประเทศ |
| `Region` | string | ภูมิภาค |
| `BottleSize` | string | ขนาดขวด |
| `Avb` | number | แอลกอฮอล์ (%) |
| `Vintages` | array | ปีและราคา |

`Vintages[]`

| Field | Type |
|---|---|
| `Vintage` | string |
| `RecommendPrice` | number |
| `RecommendMaxPrice` | number |
| `RecommendMinPrice` | number |

### `Message`

| Message | ความหมาย | วิธีใช้ |
|---|---|---|
| `พบข้อมูลแนะนำราคาขายเบื้องต้น` | พบราคาตรงรายการ | อ่านราคาจาก `Data` |
| `พบข้อมูลแนะนำราคาขายเบื้องต้นใกล้เคียง` | ไม่ตรงเป๊ะ มีรายการใกล้เคียง | `Data` มักเป็นค่าที่ส่งไป (ราคา 0) — ดู `Suggestions` |
| `ไม่พบข้อมูลแนะนำราคาขายเบื้องต้น` | ไม่มีราคาแนะนำ | `Data` เป็นค่าที่ส่งไป ราคาเป็น 0 |

---

## 4. HTTP Status

| Status | เมื่อไหร่? | Body |
|---|---|---|
| `200` | ค้นหาเสร็จ (พบ / ไม่พบ / ใกล้เคียง) | JSON ตามข้อ 3 |
| `400` | รูปแบบข้อมูลไม่ถูกต้อง | JSON `Success=false` |
| `401` | ไม่มี token หรือ token ไม่ผ่าน | text |
| `404` | ไม่พบข้อมูลไวน์ | text `Wine Data not found` |
| `500` | ข้อผิดพลาดฝั่ง server | JSON `Success=false` |

`200` ร่วมกับ `Success=false` เป็นไปได้ เมื่อค้นแล้วไม่มีผลที่ใช้ได้

---

## 5. Examples

### 5.1 ค้นจากชื่อ

```http
POST /cloud/apiv2-Winesearch_Excise HTTP/1.1
Host: api-taitaxes.excise.go.th
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "ReqNo": "REQ-20260820-0001",
  "Name": "Penfolds Grange",
  "CategoryName": "Red",
  "Vintage": "2018",
  "BottleSize": "750ml",
  "Avb": 14.5,
  "Country": "Australia",
  "Region": "South Australia"
}
```

พบราคา

```json
{
  "Success": true,
  "Message": "พบข้อมูลแนะนำราคาขายเบื้องต้น",
  "ReqNo": "REQ-20260820-0001",
  "Query": "Penfolds Grange",
  "Vintage": "2018",
  "Size": "750ml",
  "Data": [
    {
      "Name": "Penfolds Grange",
      "Pic": "https://example.com/wine.jpg",
      "CategoryName": "Red",
      "Country": "Australia",
      "Region": "South Australia",
      "BottleSize": "Bottle (750ml)",
      "Avb": 14.5,
      "Vintage": "2018",
      "RecommendPrice": 28500,
      "RecommendMaxPrice": 32000,
      "RecommendMinPrice": 25000
    }
  ],
  "Suggestions": []
}
```

ใกล้เคียง

```json
{
  "Success": true,
  "Message": "พบข้อมูลแนะนำราคาขายเบื้องต้นใกล้เคียง",
  "ReqNo": "REQ-20260820-0001",
  "Query": "Penfolds Grange",
  "Vintage": "2018",
  "Size": "750ml",
  "Data": [
    {
      "Name": "Penfolds Grange",
      "Pic": "",
      "CategoryName": "Red",
      "Country": "Australia",
      "Region": "South Australia",
      "BottleSize": "Bottle (750ml)",
      "Avb": 14.5,
      "Vintage": "2018",
      "RecommendPrice": 0,
      "RecommendMaxPrice": 0,
      "RecommendMinPrice": 0
    }
  ],
  "Suggestions": [
    {
      "Name": "Penfolds Grange Bin 95",
      "Pic": "https://example.com/wine.jpg",
      "CategoryName": "Red",
      "Country": "Australia",
      "Region": "South Australia",
      "BottleSize": "Bottle (750ml)",
      "Avb": 14.5,
      "Vintages": [
        {
          "Vintage": "2017",
          "RecommendPrice": 26000,
          "RecommendMaxPrice": 29000,
          "RecommendMinPrice": 23000
        }
      ]
    }
  ]
}
```

### 5.2 ค้นจากรูปฉลาก

ส่ง `Name` เป็น `""` (string ว่าง) และใส่ `Piclabel` เป็น base64 ของไฟล์รูป

```json
{
  "ReqNo": "REQ-20260820-0002",
  "Name": "",
  "CategoryName": "Red",
  "Vintage": "2019",
  "BottleSize": "750ml",
  "Avb": 13.5,
  "Country": "France",
  "Region": "Bordeaux",
  "Piclabel": "/9j/4AAQSkZJRgABAQAAAQABAAD..."
}
```

ถ้า `Name` ไม่ว่าง ระบบจะค้นจากชื่อ และไม่ใช้ `Piclabel`

### 5.3 ไม่ส่ง token

```http
HTTP/1.1 401 Unauthorized

You are not authenticate !
```

### 5.4 ข้อมูลไม่ถูกต้อง

เช่น ขาด field, ส่ง `Avb` เป็น `"14.5"` (string), หรือส่ง field นอก spec

```json
{
  "Success": false,
  "Message": "Bad Request : กรุณาระบุประเภทข้อมูลให้ถูกต้อง.",
  "ReqNo": "",
  "Query": "",
  "Vintage": "",
  "Size": "",
  "Data": [],
  "Suggestions": []
}
```

### 5.5 ไม่พบราคา

```json
{
  "Success": true,
  "Message": "ไม่พบข้อมูลแนะนำราคาขายเบื้องต้น",
  "ReqNo": "REQ-20260820-0003",
  "Query": "Unknown Wine",
  "Vintage": "2020",
  "Size": "750ml",
  "Data": [
    {
      "Name": "Unknown Wine",
      "Pic": "",
      "CategoryName": "Red",
      "Country": "France",
      "Region": "Burgundy",
      "BottleSize": "Bottle (750ml)",
      "Avb": 13,
      "Vintage": "2020",
      "RecommendPrice": 0,
      "RecommendMaxPrice": 0,
      "RecommendMinPrice": 0
    }
  ],
  "Suggestions": []
}
```

---

## 6. หมายเหตุ

1. เรียก URL ตาม environment ในตารางหน้าแรก — UAT และ Production คนละ path คนละ Access Token
2. Access Token บังคับทุก request — ขอจากผู้ดูแลระบบ และ/หรือ ผู้พัฒนาระบบ ออกให้หนึ่ง token ต่อ environment แล้วใช้ต่อเนื่องได้
3. เก็บ token เป็นความลับ ห้ามเปิดเผยในเอกสารหรือโค้ดสาธารณะ
4. ราคาเป็นบาท (THB)
5. `Avb` ต้องเป็น number เช่น `14.5` ไม่ใช่ `"14.5"`
6. `CategoryName` ส่งตามที่ฝั่งผู้เรียกจัดกลุ่ม ไม่มี master data และไม่มีรายการค่าที่รับเฉพาะ
7. `Piclabel` ละได้ถ้าไม่ค้นจากรูป ถ้าส่งต้องเป็น base64 ของไฟล์รูป (jpeg / png) ไม่ใช่ URL และไม่ใช่ binary
8. ค้นจากรูปเมื่อ `Name` เป็น `""` (string ว่าง) และมี `Piclabel`
9. ค้นจากรูปหรือหลายรายการอาจใช้เวลานาน แนะนำ timeout ฝั่งผู้เรียกไม่ต่ำกว่า 10 นาที
10. ขนาด request ไม่เกิน 10 MB
