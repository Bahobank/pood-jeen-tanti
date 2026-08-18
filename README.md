# พูดจีนทันที (Pood Jeen Tanti)

เว็บแอปสำหรับคนไทยที่เดินทางไปประเทศจีน — **ค้นหาเป็นภาษาไทย → แตะ → โทรศัพท์พูดภาษาจีนทันที**

- 300 ประโยค / 20 หมวด (ใช้ Dataset ที่กำหนดมาแบบตรงตัว ไม่มีการแปลหรือแก้ไขเพิ่มเอง)
- React + TypeScript + Vite, Mobile-first, PWA, ไม่มี Backend, ไม่มี API เสียเงิน
- เสียงใช้ Web Speech API (`zh-CN`) ของตัวเครื่อง
- Favorite / Recent 30 รายการ / ใช้บ่อย เก็บใน localStorage — ไม่ต้องล็อกอิน

## รันในเครื่อง

```bash
npm install
npm run dev       # เปิด http://localhost:5173
npm run build     # ผลลัพธ์อยู่ในโฟลเดอร์ dist/
npm run preview
```

## นำขึ้นออนไลน์ (แนะนำ เพื่อให้ติดตั้ง PWA บนมือถือได้)

โฟลเดอร์ `dist/` เป็นไฟล์ static ล้วน อัปโหลดขึ้นที่ไหนก็ได้ที่เป็น **HTTPS** เช่น
Netlify (ลากโฟลเดอร์ dist วางในหน้า Deploy), Vercel, Cloudflare Pages หรือ GitHub Pages

เมื่อเปิดผ่าน HTTPS แล้ว:
- iPhone: Safari → ปุ่มแชร์ → "เพิ่มไปยังหน้าจอโฮม"
- Android: Chrome → เมนู → "ติดตั้งแอป"

หลังเปิดครั้งแรก Service Worker จะแคชไว้ทั้งหมด ใช้งานต่อได้แม้ไม่มีเน็ต (สำคัญมากตอนอยู่จีน)

## ทำเป็นแอปมือถือ (Play Store / App Store)

โปรเจกต์ห่อด้วย Capacitor 8 แล้ว — โฟลเดอร์ `android/` และ `ios/` พร้อมใช้

```bash
npm run android   # build + sync + เปิด Android Studio
npm run ios       # build + sync + เปิด Xcode (ต้องใช้ Mac)
```

รายละเอียดครบทุกขั้นตอน ค่าใช้จ่าย และข้อควรระวังของสโตร์ อ่านที่ `คู่มือขึ้นสโตร์.md`

## เลขบัญชีสนับสนุน

แก้ที่ `src/config/support.ts` ไฟล์เดียว — QR พร้อมเพย์สร้างในเครื่องผู้ใช้เอง (EMVCo) ไม่ต้องใช้ API

## โครงสร้าง

```
data/raw.txt              Dataset ต้นฉบับ (ไทย | จีน | pinyin | คำอ่านไทย)
scripts/gen.mjs           แปลง raw.txt → src/data/phrases.ts (สร้างเฉพาะ id + keywords)
scripts/verify.mjs        ตรวจว่าข้อความจีน/pinyin/คำอ่านไทย ตรงกับต้นฉบับ 100%
src/data/phrases.ts       ข้อมูล 300 ประโยค + 20 หมวด + alternatives
src/lib/tts.ts            Web Speech API (zh-CN, voiceschanged, ความเร็ว 0.7/0.9/1.0)
src/lib/store.ts          localStorage: favorites / recents / counts / settings
src/lib/search.ts         ค้นหาแบบ partial จาก ไทย/จีน/pinyin/คำอ่านไทย/keywords
src/components/           PhraseCard, BottomNav, ShowToChinese, Settings, Icon
test/run.mjs              ชุดทดสอบจริงด้วย Playwright (47 เคส)
```

## แก้ไขข้อมูล

แก้ที่ `data/raw.txt` เท่านั้น แล้วรัน

```bash
node scripts/gen.mjs && node scripts/verify.mjs
```

## ทดสอบ

```bash
npm run build
node test/run.mjs      # เปิด Chromium จริง ทดสอบค้นหา/TTS/Favorite/Recent/PWA/Responsive
```

ทดสอบผ่านแล้วที่ขนาด 390×844, 430×932, 412×915 และ 1440×900

## หมายเหตุเรื่องเสียงบน iPhone

iOS โหลดรายชื่อเสียงแบบ async แอปจึงรอ event `voiceschanged` และเลือกเสียงที่เป็น
Mandarin / zh-CN ก่อนเสมอ ถ้าเครื่องยังไม่มีเสียงภาษาจีน ให้ไปที่
ตั้งค่า › การช่วยการเข้าถึง › เนื้อหาที่พูด › เสียงพูด › ภาษาจีน (จีนกลาง) แล้วดาวน์โหลดก่อน
