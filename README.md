# Stickers | ستكرس — موقع متجر إلكتروني لبيع الستيكرات المطبوعة

موقع كامل لعرض وبيع **ستيكرات ورقية/فينيل فيزيائية مخصصة** (وليست ستيكرات واتساب)، مبني بـ HTML/CSS/JS خالص + Tailwind + GSAP + AOS + Font Awesome، ويعمل بالكامل مجانًا على **GitHub Pages** بدون أي خادم خلفي (backend) — الحفظ والإشعارات تتم عبر خدمات مجانية: **Google Sheets + Apps Script**، **Cloudinary**، و**Telegram Bot**.

هذا الدليل موجّه لشخص **مبتدئ تمامًا**، اتبع الخطوات بالترتيب.

---

## 📁 محتويات المشروع

```
stickers-store/
├── index.html              الصفحة الرئيسية
├── order.html               صفحة إتمام الطلب
├── admin.html                لوحة التحكم
├── assets/
│   ├── css/style.css         التصميم
│   ├── js/main.js             تفاعلات الصفحة الرئيسية
│   ├── js/order.js            منطق صفحة الطلب (الأهم — فيه إعدادات Cloudinary وApps Script)
│   ├── js/admin.js            منطق لوحة التحكم (فيه كلمة المرور وإعدادات Apps Script)
│   └── js/algeria-data.js     قائمة الولايات الـ58 وبلدياتها
├── google-apps-script/
│   └── Code.gs                الكود الذي تلصقه في Google Apps Script
└── README.md                  هذا الملف
```

---

## الخطوة 1 — نشر الموقع على GitHub Pages (مجانًا)

1. أنشئ حسابًا على [github.com](https://github.com) إن لم يكن لديك واحد.
2. اضغط **New repository**، سمِّه مثلًا `stickers-store`، اجعله **Public**، ثم **Create repository**.
3. من صفحة المستودع اضغط **Add file → Upload files**، واسحب كل ملفات ومجلدات هذا المشروع (حافظ على نفس الهيكلة/المجلدات).
4. اذهب إلى **Settings → Pages**.
5. تحت **Branch** اختر `main` والمجلد `/root`، ثم **Save**.
6. انتظر دقيقة، سيظهر لك رابط الموقع بالشكل: `https://username.github.io/stickers-store/`

الموقع الآن منشور، لكن الطلبات لن تُحفظ ولن تصلك إشعارات حتى تُكمل الخطوات التالية.

---

## الخطوة 2 — إنشاء Google Sheet لحفظ الطلبات

1. اذهب إلى [sheets.google.com](https://sheets.google.com) وأنشئ **جدول بيانات فارغ جديد**.
2. سمِّه مثلًا "طلبات ستكرس".
3. لا تحتاج لإنشاء أعمدة يدويًا — الكود سينشئها تلقائيًا.

---

## الخطوة 3 — ربط Google Apps Script بالجدول

1. داخل نفس الجدول، من القائمة اذهب إلى **Extensions (الإضافات) → Apps Script**.
2. ستفتح صفحة كود جديدة، احذف الكود الافتراضي الموجود بداخلها بالكامل.
3. افتح ملف `google-apps-script/Code.gs` من هذا المشروع، انسخ **كل محتواه**، والصقه في محرر Apps Script.
4. في أعلى الكود ستجد ثلاثة أسطر إعدادات:
   ```js
   const TELEGRAM_BOT_TOKEN = "PASTE_YOUR_BOT_TOKEN_HERE";
   const TELEGRAM_CHAT_ID   = "PASTE_YOUR_CHAT_ID_HERE";
   const SHEET_NAME = "Orders";
   ```
   اترك `SHEET_NAME` كما هو الآن، وسنعود لملء التوكن ومعرف الدردشة بعد إنشاء بوت Telegram (الخطوة 5).
5. احفظ المشروع (أيقونة القرص المرن 💾)، وسمِّه "Stickers Backend".

### نشر الكود كـ "Web App"

1. اضغط **Deploy (نشر) → New deployment**.
2. اضغط على أيقونة الترس ⚙️ بجانب "Select type" واختر **Web app**.
3. اضبط:
   - **Execute as:** Me (حسابك)
   - **Who has access:** Anyone
4. اضغط **Deploy**.
5. سيطلب منك Google **صلاحيات** — اضغط **Authorize access**، اختر حسابك، ثم "Advanced" → "Go to Stickers Backend (unsafe)" → "Allow". (هذا طبيعي لأنه سكربت أنشأته أنت بنفسك).
6. بعد النشر، ستحصل على رابط شبيه بهذا:
   ```
   https://script.google.com/macros/s/AKfycbxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/exec
   ```
   **انسخ هذا الرابط** — ستحتاجه في الخطوة 6.

> ⚠️ في كل مرة تُعدّل فيها الكود لاحقًا، يجب أن تعمل **Deploy → Manage deployments → تعديل (✏️) → New version → Deploy** حتى تُطبَّق التعديلات على نفس الرابط.

---

## الخطوة 4 — إنشاء حساب Cloudinary (لرفع صور التصاميم مجانًا)

1. اذهب إلى [cloudinary.com](https://cloudinary.com) وأنشئ حسابًا مجانيًا.
2. من لوحة التحكم (Dashboard)، انسخ قيمة **Cloud name** (تظهر أعلى الصفحة).
3. اذهب إلى **Settings ⚙️ → Upload**.
4. انزل إلى قسم **Upload presets** واضغط **Add upload preset**.
5. غيّر **Signing Mode** من `Signed` إلى **`Unsigned`** (مهم جدًا — لأن الموقع بدون خادم).
6. احفظ، وانسخ **اسم الـ Preset** (upload preset name).

---

## الخطوة 5 — إنشاء بوت Telegram لإشعارات الطلبات

1. افتح تطبيق Telegram وابحث عن **@BotFather**.
2. أرسل له `/newbot` واتبع التعليمات (اسم البوت + username ينتهي بـ `bot`).
3. سيعطيك **Bot Token** بالشكل: `123456789:AAF-xxxxxxxxxxxxxxxxxxxxxxx` — انسخه.
4. الآن ابحث عن اسم البوت الذي أنشأته في Telegram واضغط **Start** لبدء محادثة معه.
5. لمعرفة **Chat ID** الخاص بك: افتح هذا الرابط في المتصفح بعد استبدال TOKEN بتوكنك:
   ```
   https://api.telegram.org/botTOKEN/getUpdates
   ```
   أرسل أي رسالة للبوت أولًا، ثم أعد تحميل الرابط أعلاه، وابحث عن `"chat":{"id":XXXXXXXXX`. هذا الرقم هو chat id الخاص بك.

6. عد إلى Apps Script (الخطوة 3) وضع القيمتين:
   ```js
   const TELEGRAM_BOT_TOKEN = "123456789:AAF-xxxxxxxxxxxxxxxxxxxxxxx";
   const TELEGRAM_CHAT_ID   = "987654321";
   ```
7. احفظ، ثم **Deploy → Manage deployments → ✏️ → New version → Deploy** لتحديث الرابط بنفس التعديلات.

---

## الخطوة 6 — ربط كل شيء داخل الموقع

افتح `assets/js/order.js` وعدّل أعلى الملف:

```js
const CONFIG = {
  CLOUDINARY_CLOUD_NAME: "ضع Cloud name هنا",
  CLOUDINARY_UPLOAD_PRESET: "ضع اسم الـ Upload Preset هنا",
  APPS_SCRIPT_URL: "ضع رابط /exec من Apps Script هنا",
  ...
};
```

ثم افتح `assets/js/admin.js` وعدّل نفس رابط Apps Script:

```js
const CONFIG = {
  APPS_SCRIPT_URL: "نفس رابط /exec",
  ADMIN_PASSWORD_HASH: "..."
};
```

ارفع الملفات المعدّلة مرة أخرى إلى GitHub (يمكنك تعديل الملف مباشرة من واجهة GitHub بالضغط على أيقونة القلم ✏️ ثم Commit).

---

## الخطوة 7 — تغيير كلمة مرور لوحة التحكم

كلمة المرور الافتراضية هي `admin123` — **غيّرها فورًا**:

1. افتح موقعك، أضف `/admin.html` في نهاية الرابط، ثم افتح أدوات المطوّر في المتصفح (F12) → تبويب **Console**.
2. اكتب:
   ```js
   await sha256("كلمة_المرور_الجديدة_التي_تريدها")
   ```
3. انسخ الناتج (سلسلة طويلة من الأحرف والأرقام).
4. افتح `assets/js/admin.js` وضع الناتج مكان قيمة `ADMIN_PASSWORD_HASH`.
5. احفظ وارفع الملف مرة أخرى إلى GitHub.

> ملاحظة أمان: هذه حماية أساسية مناسبة لموقع بلا خادم، لكنها ليست بمستوى حماية تسجيل دخول حقيقي على خادم. لا تستخدم كلمة مرور حساسة تستخدمها في حسابات أخرى.

---

## الخطوة 8 — تجربة كل شيء

1. افتح موقعك ← اضغط "اطلب الآن" ← أكمل الخطوات الثلاث وأرسل طلبًا تجريبيًا.
2. تحقق من:
   - ظهور الصورة في مكتبة Media الخاصة بحساب Cloudinary.
   - إضافة سطر جديد في Google Sheet.
   - وصول رسالة فورية في Telegram.
3. افتح `admin.html`، سجّل الدخول، وتأكد من ظهور الطلب في الجدول، وجرّب البحث والتصدير CSV.

---

## أسئلة شائعة

**هل يمكن إضافة كل بلديات الجزائر الـ1541 بدل القائمة المختصرة؟**
نعم — افتح `assets/js/algeria-data.js` وأضف أي بلدية ناقصة داخل مصفوفة `communes` الخاصة بولايتها.

**هل التسعير في صفحة الطلب نهائي؟**
لا، هو تقديري فقط بناءً على `PRICE_TABLE` في `order.js`، ويمكنك تعديل الأسعار من هناك مباشرة.

**الموقع لا يرسل الطلبات؟**
تأكد أن رابط Apps Script ينتهي بـ `/exec` (وليس `/dev`)، وأن صلاحية الوصول When deploying مضبوطة على "Anyone".

**كيف أغيّر رقم واتساب الفوتر وزر التواصل؟**
ابحث في `index.html` عن `wa.me/213000000000` واستبدل الرقم برقمك الفعلي بصيغة دولية بدون الصفر الأول (مثال: `213555xxxxxx`).

---

صُمم هذا المشروع خصيصًا لمتجر **Stickers | ستكرس** (Instagram: [@stikrs_star.27](https://instagram.com/stikrs_star.27)) 🏷️
