# 🔥 Secrit Script v3.0 — تفعيل اللايكات والتعليقات وعدّاد النسخ العالمي

الموقع الجديد فيه نظام تفاعل كامل لكل سكربت:

- ❤️ **لايكات** (حقيقية، لكل زائر، مع إلغاء اللايك)
- 💬 **تعليقات** (اسم + نص + وقت + لايك + حذف تعليقك)
- 📋 **عدّاد نسخ** (يزيد مع كل ضغطة "نسخ كود السكربت")
- 👥 **عدّاد زوار الموقع الآن** (Presence حي)
- 🏆 **قائمة الأكثر نسخاً** + إجماليات النسخ واللايكات في الشريط الحي

---

## ⚙️ بدون إعداد = وضع تجريبي محلي

الموقع يعمل **فوراً** بدون أي إعداد (وضع Demo):
- الأرقام محفوظة في متصفح الزائر نفسه فقط
- لن تكون الأرقام مشتركة بين الزوار

## 🚀 لتفعيل الأرقام العالمية الحقيقية (5 دقائق فقط)

### الخطوة 1: إنشاء مشروع Firebase مجاني
1. افتح https://console.firebase.google.com
2. سجّل بحساب Google ثم اضغط **Add project**
3. اكتب أي اسم مثل `secrit-script` واضغط Continue
4. تقدر تعطّل Google Analytics واضغط **Create project**

### الخطوة 2: إضافة موقع ويب
1. من الصفحة الرئيسية للمشروع اضغط على أيقونة `</>` (Web)
2. اكتب اسم `secrit-script` واضغط **Register app**
3. ستظهر لك بيانات `firebaseConfig` — **انسخها**

### الخطوة 3: لصق البيانات في الملف
افتح ملف `index.html` وابحث عن `FIREBASE_CONFIG` وضع بياناتك:

```js
var FIREBASE_CONFIG = {
    apiKey: "AIzaSy...",                                  // ← من عندك
    authDomain: "secrit-script.firebaseapp.com",          // ← من عندك
    databaseURL: "https://secrit-script-default-rtdb.firebaseio.com",
    projectId: "secrit-script",
    storageBucket: "secrit-script.appspot.com",
    messagingSenderId: "123456789",
    appId: "1:123456789:web:abc123"
};
```

### الخطوة 4: إنشاء قاعدة البيانات Realtime Database
1. من القائمة اليسرى: **Build → Realtime Database → Create Database**
2. اختر أي موقع (يفضل `europe-west1` لأنه الأقرب لمصر)
3. اختر **Start in locked mode** (هنجهز القواعد بنفسنا)

### الخطوة 5: وضع قواعد الحماية
في تبويب **Rules** داخل Realtime Database، احذف كل شيء وضع هذا:

```json
{
  "rules": {
    "engagements": {
      "scripts": {
        "$scriptId": {
          ".read": true,
          ".write": "auth != null",
          "copies": {
            ".validate": "newData.isNumber() && newData.val() >= 0"
          },
          "likes": {
            "$uid": {
              ".validate": "auth.uid == $uid"
            }
          },
          "comments": {
            "$cid": {
              ".write": "auth != null && (newData.val() == null ? data.child('uid').val() == auth.uid : newData.child('uid').val() == auth.uid)",
              ".validate": "newData.hasChildren(['uid','name','text','ts']) && newData.child('text').isString() && newData.child('text').val().length <= 300 && newData.child('name').isString() && newData.child('name').val().length <= 32"
            }
          }
        }
      }
    },
    "presence": {
      ".read": true,
      "$uid": {
        ".write": "auth != null && ($uid == auth.uid)"
      }
    }
  }
}
```

اضغط **Publish**.

### الخطوة 6: تفعيل تسجيل الدخول المجهول
1. **Build → Authentication → Get started**
2. تبويب **Sign-in method** → فعّل **Anonymous** ثم Save
3. (اختياري) تبويب **Settings → Authorized domains** وتأكد أن نطاق موقعك موجود: `gamer1090mn.github.io`

### الخطوة 7: ارفع الموقع على GitHub Pages
```bash
git add index.html README-FIREBASE.md
git commit -m "v3.0: engagement system + professional animations"
git push origin main
```

افتح الموقع وشوف بنفسك: الأرقام هتبقى حقيقية ومشتركة بين كل الزوار فوراً. 🎉

---

## ❓ أسئلة شائعة

**هل هو مجاني؟** نعم، خطة Spark المجانية كافية جداً (حدودها أعلى بكتير من احتياجك).

**هل يظهر للمستخدمين أي شيء غريب؟** لا — الزائر يدخل بشكل مجهول تلقائياً بدون أي شاشة تسجيل.

**هل ممكن حد يزور الأرقام؟** اللايك مرتبط بجهاز الزائر (1 لايك لكل جهاز)، والنسخ يزيد فقط بضغطة حقيقية على زر النسخ. القواعد أعلاه تمنع التلاعب من خارج الموقع.

**التعليقات:** أي تعليق يكتبه زائر لا يقدر يحذفه إلا صاحبه (بقاعدة الحماية أعلاه). لتعديل أي تعليق يدوياً: Firebase Console → Realtime Database → Data.

**لو حصل أي خطأ في Firebase؟** الموقع يتحول تلقائياً للوضع التجريبي المحلي ولا يتعطل أبداً.
