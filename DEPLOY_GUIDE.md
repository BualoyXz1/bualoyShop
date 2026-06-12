# 🚀 คู่มือ Deploy POS บน Vercel (Step-by-Step)

คู่มือนี้จะพาคุณ deploy ระบบ POS ขึ้น Vercel ให้ใช้งานได้จริง ทีละขั้นตอน

---

## 📋 สิ่งที่ต้องเตรียม

- [ ] บัญชี GitHub (ถ้ายังสมัครไม่ได้ → ข้ามไป **Plan B** ด้านล่าง)
- [ ] บัญชี Vercel (สมัครฟรีที่ https://vercel.com)
- [ ] โค้ดใน folder `POS-Vercel` พร้อมแล้ว

---

## 🎯 Step 1: เตรียม GitHub Repository

### 1.1 สร้าง Repository ใหม่

1. ไปที่ https://github.com/new
2. ตั้งชื่อ: `pos-vercel`
3. เลือก **Public** (แนะนำ) หรือ **Private**
4. ❌ **อย่าติ๊ก** "Add README" หรืออะไรเลย
5. คลิก **"Create repository"**

### 1.2 Push โค้ดขึ้น GitHub

เปิด Command Prompt:

```cmd
cd D:\AI_PROJECT\POS-Vercel

# ตั้งค่า Git (ถ้ายังไม่เคย)
git config --global user.email "your-email@example.com"
git config --global user.name "Your Name"

# เริ่มต้น Git
git init
git add .
git commit -m "Initial commit"
git branch -M main

# เชื่อมต่อกับ GitHub (แทน YOUR_USERNAME)
git remote add origin https://github.com/YOUR_USERNAME/pos-vercel.git
git push -u origin main
```

**ถ้า push ไม่ได้ (ถ้าเป็น Private repo):**
- ต้องใช้ **Personal Access Token** แทน password
- ไปที่ https://github.com/settings/tokens → **Generate new token (Classic)**
- เลือก scope: `repo`
- Copy token → ใช้แทน password ตอน push

✅ **เช็คว่าสำเร็จ:** Refresh หน้า GitHub repo จะเห็นโค้ดทั้งหมด

---

## 🎯 Step 2: สร้าง Vercel Project

### 2.1 Import จาก GitHub

1. ไปที่ https://vercel.com/dashboard
2. คลิก **"Add New..."** → **"Project"**
3. เลือก **"Import Git Repository"**
4. เลือก repo `pos-vercel` (ถ้าไม่เห็น → คลิก **"Adjust GitHub App Permissions"**)
5. คลิก **"Import"**

### 2.2 ตั้งค่า Project

หน้า **"Configure Project"**:

| ตัวเลือก | ค่าที่ตั้ง |
|----------|-----------|
| **Project Name** | `pos-vercel` (หรืออะไรก็ได้) |
| **Framework Preset** | Next.js ✅ (เลือกอัตโนมัติ) |
| **Root Directory** | `./` (ปล่อยว่าง) |
| **Build Command** | **ว่างไว้ก่อน** (จะตั้งทีหลัง) |
| **Output Directory** | ว่างไว้ |

### 2.3 ❌ อย่ากด Deploy ก่อน!

กด **"Environment Variables"** เพื่อเพิ่มตัวแปร (ขั้นตอนถัดไป)

---

## 🎯 Step 3: ตั้งค่า Environment Variables (สำคัญมาก!)

ใน **"Environment Variables"** → เพิ่มตัวแปรเหล่านี้:

### 3.1 เพิ่มทีละตัว

คลิก **"Add Variable"** แล้วใส่:

**Variable 1:**
- **Key:** `ADMIN_PASSWORD`
- **Value:** `your-password-123` (รหัสผ่านเข้าหลังบ้าน)
- **Environment:** เลือกทั้ง 3 (Production, Preview, Development)

**Variable 2:**
- **Key:** `PROMPTPAY_ID`
- **Value:** `0812345678` (เบอร์มือถือ 10 หลัก หรือเลขบัตรประชาชน 13 หลัก)
- **Environment:** เลือกทั้ง 3

**Variable 3:**
- **Key:** `SESSION_SECRET`
- **Value:** `random-long-string-at-least-32-characters-abcd1234`
- **Environment:** เลือกทั้ง 3

✅ **ตอนนี้จะมี 3 ตัวแปร** → คลิก **"Deploy"**

---

## 🎯 Step 4: Deploy ครั้งแรก (จะ Fail ไม่เป็นไร!)

### 4.1 Deploy

- คลิก **"Deploy"**
- รอ 2-3 นาที
- **ผลลัพธ์:** ❌ Build Failed (ปกติ! เพราะยังไม่มี database)

### 4.2 แก้ Build Command

1. ไปที่ **Settings** → **General**
2. เลื่อนลงหา **"Build & Development Settings"**
3. คลิก **"Override"** ที่ **Build Command**
4. ใส่:
   ```
   prisma generate && next build
   ```
5. คลิก **"Save"**

---

## 🎯 Step 5: สร้าง PostgreSQL Database

### 5.1 สร้าง Database

1. ไปที่ tab **"Storage"** (บนสุด)
2. คลิก **"Create Database"**
3. เลือก **"Postgres"**
4. ตั้งชื่อ: `pos-db` (หรืออะไรก็ได้)
5. เลือก **Region:** ที่ใกล้คุณ (แนะนำ Singapore)
6. คลิก **"Create"**

### 5.2 เชื่อมต่อกับ Project

1. หลังสร้างเสร็จ → คลิก **"Connect Project"**
2. เลือก project `pos-vercel`
3. คลิก **"Connect"**

✅ **Vercel จะเพิ่ม environment variables ให้อัตโนมัติ:**
- `POSTGRES_PRISMA_URL`
- `POSTGRES_URL_NON_POOLING`

---

## 🎯 Step 6: Redeploy

### 6.1 Deploy ใหม่

1. กลับไปที่ tab **"Deployments"**
2. คลิกที่ deployment ที่ fail (อันล่าสุด)
3. คลิกปุ่ม **"⋯"** (3 จุด) → **"Redeploy"**
4. คลิก **"Redeploy"**

### 6.2 ดู Build Logs

- รอ 2-3 นาที
- คลิก **"Building"** → ดู logs
- **ผลลัพธ์ที่ต้องการ:** ✅ **"Build Completed"**

### 6.3 เปิด URL

- คลิก **"Visit"** หรือ URL ที่ได้ (เช่น `https://pos-vercel.vercel.app`)
- **จะเห็น:** หน้าเปล่า หรือ error ว่า "table not found" (ปกติ! ยังไม่มีตาราง)

---

## 🎯 Step 7: สร้างตารางใน Database

### 7.1 Copy Database URL

1. ไปที่ **Settings** → **Environment Variables**
2. คลิกที่ `POSTGRES_PRISMA_URL`
3. คลิก **"Show"** → **Copy** ค่า (ยาวมาก ขึ้นต้นด้วย `postgres://`)

### 7.2 Push Schema จากเครื่องของคุณ

เปิด Command Prompt:

```cmd
cd D:\AI_PROJECT\POS-Vercel

# สร้างไฟล์ .env
notepad .env
```

ใส่ใน `.env` (แทนด้วย URL ที่ copy มา):

```
POSTGRES_PRISMA_URL="postgres://..."
POSTGRES_URL_NON_POOLING="postgres://..."
```

แล้วรัน:

```cmd
# ติดตั้ง dependencies (ถ้ายังไม่เคย)
npm install

# Push schema → สร้างตาราง
npx prisma db push
```

✅ **ผลลัพธ์:** 
```
✔ Generated Prisma Client
✔ The database is in sync with the schema
```

---

## 🎯 Step 8: Seed ข้อมูลตัวอย่าง (Optional)

```cmd
npx prisma db seed
```

✅ **ผลลัพธ์:** `Seeded 6 products.`

---

## 🎯 Step 9: ทดสอบเว็บไซต์

### 9.1 เปิด URL

เปิด `https://pos-vercel.vercel.app` (URL ของคุณ)

### 9.2 ทดสอบหน้าหลัก

- ✅ ควรเห็นสินค้า (ถ้า seed แล้ว)
- ✅ กดสินค้า → เพิ่มลงตะกร้า
- ✅ กด "ดูตะกร้า" → "ชำระเงิน"
- ✅ ทดสอบ QR Code

### 9.3 ทดสอบหลังบ้าน

1. เปิด `/admin/login`
2. Login ด้วย `ADMIN_PASSWORD` ที่ตั้งไว้
3. ✅ เพิ่มสินค้า → อัพโหลดรูป
4. ✅ ไปที่ `/admin/reports` → ดูรายงาน
5. ✅ ไปที่ `/admin/stock` → ดูสต๊อก

---

## 🎉 สำเร็จ!

ตอนนี้คุณมี POS system ที่:
- ✅ เข้าได้จากทุกที่ (มือถือ/คอม)
- ✅ Database บน cloud (Postgres)
- ✅ Auto-deploy เมื่อ push GitHub
- ✅ HTTPS ฟรี
- ✅ ใช้งานได้จริง

**URL:** `https://pos-vercel.vercel.app`

---

## 🔄 อัปเดตโค้ด (ในอนาคต)

เมื่อต้องการแก้ไขโค้ด:

```cmd
cd D:\AI_PROJECT\POS-Vercel

# แก้โค้ด
# ...

# Commit & Push
git add .
git commit -m "Update feature"
git push

# Vercel จะ auto-deploy ให้ทันที!
```

---

## ❓ Troubleshooting

### ปัญหา: Build Failed

1. ดู **Build Logs** → อ่าน error
2. ตรวจสอบ **Environment Variables** ครบหรือไม่
3. ตรวจสอบ **Build Command:** `prisma generate && next build`

### ปัญหา: "Table not found" error

→ ยังไม่ได้รัน `prisma db push` → กลับไปทำ **Step 7**

### ปัญหา: QR Code ไม่ขึ้น

→ ตรวจสอบ `PROMPTPAY_ID` ใน Environment Variables

### ปัญหา: Login ไม่ได้

→ ตรวจสอบ `ADMIN_PASSWORD` ใน Environment Variables

---

## 📱 ใช้งานบนมือถือ

1. เปิด URL บนมือถือ
2. แตะ **Share** → **"Add to Home Screen"**
3. ตั้งชื่อ: `POS ร้านค้า`
4. เปิดจากไอคอน → ใช้แบบ app ได้เลย!

---

**หมายเหตุ:** รูปสินค้าที่อัพโหลดจะหายหลัง redeploy (Vercel limitation) → ใช้ Vercel Blob สำหรับ production

**ขอให้โชคดี!** 🎊
