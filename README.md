<div dir="rtl">

# مصنع التطبيقات

تطبيق موبايل يبني المواقع والتطبيقات بالذكاء الاصطناعي. تكتب الكود أو تطلب من Claude أو Gemini أن يكتبه ويعدّله، ثم تحوله إلى ملف APK وتثبته على هاتفك.

## ماذا يفعل

- **المفاتيح:** تحفظ مفاتيح Claude وGemini وSupabase وGitHub على جهازك فقط.
- **الكود:** محرر كود وزر تشغيل يعرض الناتج، مع حفظ المشاريع وفتحها من Supabase.
- **تعديل:** تكتب طلبك بالعربية، فيعدّل Claude أو Gemini الكود ويضع النتيجة في المحرر، وفيه زر تراجع.
- **APK:** يرسل الكود إلى مستودع البناء على GitHub ثم يعطيك رابط تنزيل التطبيق.

## هيكل المشروع

| الملف | وظيفته |
|---|---|
| `www/index.html` | التطبيق كله في ملف واحد |
| `capacitor.config.json` | إعدادات تحويل التطبيق إلى أندرويد |
| `package.json` | مكتبات Capacitor |
| `.github/workflows/build-apk.yml` | يبني APK على GitHub تلقائياً |
| `.gitignore` | يتجاهل `node_modules` و`android` |

## بناء التطبيق نفسه

1. ارفع كل الملفات إلى مستودع جديد، وانتبه أن مجلد `.github` يجب أن يُرفع أيضاً.
2. افتح تبويب **Actions** ثم **Build APK** ثم **Run workflow**. إذا رفعت على الفرع `main` يبدأ البناء وحده.
3. بعد نحو 5 دقائق نزّل `app-debug` من قسم **Artifacts** وثبّت ملف APK الذي بداخله.
4. عند التثبيت يطلب أندرويد السماح بالتثبيت من مصدر غير معروف. وافق مرة واحدة.

## تجهيز بناء التطبيقات من داخل التطبيق

1. أنشئ مستودعاً ثانياً باسم `apk-factory` وارفع إليه محتويات مشروع `apk-factory` كاملة.
2. من GitHub افتح **Settings** ثم **Developer settings** ثم **Fine-grained tokens**، وأنشئ رمزاً لمستودع `apk-factory` فقط بصلاحيتين: **Contents** و**Actions** بمستوى Read and write.
3. في التطبيق افتح تبويب **المفاتيح**، ألصق الرمز، واكتب اسم المستودع بصيغة `اسمك/apk-factory`، ثم اضغط حفظ.
4. افتح تبويب **APK** واضغط **ابنِ APK**.

## تجهيز Supabase

شغّل هذا في SQL Editor مرة واحدة:

    create table if not exists projects (
      name text primary key,
      code text not null,
      updated_at timestamptz default now()
    );
    alter table projects enable row level security;
    create policy "open" on projects
      for all using (true) with check (true);

هذه السياسة تفتح الجدول لأي شخص يملك المفتاح، فاستخدمها للتجربة فقط.

## ملاحظات أمان

- المفاتيح تُحفظ في `localStorage` على جهازك ولا تُرفع إلى أي مستودع.
- لا تضع مفاتيحك داخل الكود الذي تبنيه، لأن أي شخص يثبّت التطبيق يستطيع قراءتها.
- الملف الناتج نسخة تجريبية (debug) للاستخدام الشخصي وليس للنشر في Google Play.

</div>
