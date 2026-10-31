# نظام إدارة المكتبة الإلكترونية الذكي (ELMS)
## University Electronic Library Management System
### جامعة السودان للعلوم والتكنولوجيا - عمادة شؤون المكتبات

نظام متكامل، إنتاجي، ومبني وفق أعلى المعايير الهندسية والأمنية لإدارة المكتبات الجامعية الكبرى. تم تصميمه وفق وثيقة متطلبات المشروع الأكاديمي (الفصول 1–5)، ويقدم تجربة استخدام عربية أصيلة (RTL) مع تكامل تام للبيانات، وسرعة فائقة، ومقاومة عالية للتزامن وحالات السباق.

---

## 🌟 أبرز ميزات النظام

- **معمارية غير متزامنة فائقة الأداء (Async FastAPI & asyncpg):** استجابة في أجزاء من الثانية وقدرة على معالجة آلاف الطلبات المتزامنة.
- **سلامة الجرد الصارمة (Strict Inventory Integrity):** معادلة مخزون قطعية لا تقبل الكسر:
  $$\text{إجمالي النسخ} = \text{النسخ المتاحة} + \text{النسخ المعارة} + \text{النسخ التالفة أو المفقودة}$$
- **مكتب الإعارة السريع (Fast Circulation Desk):**
  - فحص أهليّة فوري للطالب وفق 5 قواعد عمل قطعية (BR-01 إلى BR-05).
  - إعارة وإرجاع بنقرة زر واحدة أو بمفتاح الاختصار السريع `F2`.
  - معالجة ذرية مع أقفال صفوف PostgreSQL الحصرية (`SELECT ... FOR UPDATE`) لمنع الإعارة المزدوجة.
- **نافذة التراجع الإنساني (30-Second Undo Window):** إمكانية التراجع الفوري عن حركات الإعارة والإرجاع الخاطئة خلال 30 ثانية مع عداد تنازلي واستعادة كاملة للحالة الأصلية.
- **الإرجاع الآمن المتكرر (Idempotent Return):** يضمن عدم مضاعفة رصيد النسخ المتاحة حتى في حال تكرار النقر أو تقطع الاتصال.
- **حساب الغرامات والتحصيل المالي:**
  - احتساب آلي لغرامات التأخير اليومية باستثناء العطلات الرسمية.
  - دعم السداد الجزئي والكلي مع إصدار أرقام إيصالات مالية.
  - ميزة الإعفاء الإداري المسبب والمحصور فقط بمدير النظام والموثق في سجل التدقيق.
- **الحجوزات وقوائم الانتظار الذكية:** تقديم المستفيد التالي في قائمة الانتظار تلقائياً بمجرد إرجاع الكتاب مع مهلة استلام محددة (48 ساعة).
- **إخلاء الطرف الأكاديمي والتحقق الرقمي:**
  - فحص إلكتروني فوري لخلو ذمة الطالب من أي إعارات أو غرامات.
  - إصدار شهادة إخلاء طرف رقمية برمز تحقق فريد (`ELMS-CLR-XXXXXXXX`).
  - بوابة تحقق عامة مفتوحة للجامعات والجهات الحكومية لفحص صحة الشهادة بدون تسجيل دخول.
- **مركز التقارير الإدارية (RPT-01 إلى RPT-08):**
  - معاينة تفاعلية فورية.
  - تصدير ملفات Excel منسقة بالكامل من اليمين إلى اليسار (RTL).
  - تصدير ملفات PDF عربية احترافية مع تشكيل الحروف السليم وترقيم صفحات «صفحة X من Y».
- **البحث الذكي والتوصيات (Collaborative Filtering):**
  - بحث ذكي بالمقاطع الثلاثية `pg_trgm` وتطبيع النص العربي (الألف، التاء المربوطة، التشكيل).
  - خوارزمية توصيات تقترح كتباً إضافية استعارها طلاب آخرون استعاروا نفس الكتاب.

---

## 🛠️ البنية التقنية (Tech Stack)

- **قاعدة البيانات:** PostgreSQL 16 مع إضافات `pg_trgm`، `unaccent`، `pgcrypto` ومشغلات DB Triggers.
- **الخلفية (Backend):** Python 3.12، FastAPI، SQLAlchemy 2.0 (Async)، Pydantic v2، Alembic، APScheduler، ReportLab، openpyxl، Passlib (Bcrypt).
- **الواجهة الأمامية (Frontend):** Modern Vanilla JS Single Page Application (SPA)، تصميم متجاوب، متطابق مع هوية `view_models.html` وبخطوط Cairo و Tajawal.
- **البنية التحتية (DevOps):** Docker Compose، Nginx Reverse Proxy، سكريبتات النسخ الاحتياطي التلقائي (Cron + Bash).

---

## 🚀 التشغيل السريع (Quick Start)

### 1. المتطلبات الأساسية
- تثبيت Python 3.12+ و PostgreSQL 16.
- قاعدة بيانات باسم `library_db` مع صلاحيات للمستخدم `nice` بكلمة مرور `1234`.

### 2. التشغيل المحلي المباشر (Local Development)

```bash
# 1. الدخول إلى مجلد المشروع
cd /home/nice/PycharmProjects/digital_library

# 2. تفعيل البيئة الافتراضية
source .venv/bin/activate

# 3. تثبيت الاعتماديات (إن لم تكن مثبتة مسبقاً)
pip install -r backend/requirements.txt

# 4. تطبيق ترحيلات قاعدة البيانات (Alembic Migrations)
cd backend
alembic upgrade head

# 5. تشغيل خادم التطبيق
uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
```

افتح المتصفح مباشرة على: **`http://localhost:8000`**

### 3. التشغيل عبر Docker Compose (Production-Ready)

```bash
cd infra
docker compose up -d
```
سيعمل النظام فورياً متضمناً قاعدة البيانات، الخادم، وخادم Nginx الوسيط.

---

## 👥 بيانات تسجيل الدخول الافتراضية للتجربة

تم تزويد قاعدة البيانات مسبقاً ببيانات بذر واقعية (2,050 كتاباً، 300 طالب، 1,500 إعارة، 408 غرامة):

| الدور | اسم المستخدم (Username) | كلمة المرور (Password) | الصلاحيات والمهام |
| :--- | :--- | :--- | :--- |
| **مدير النظام (Admin)** | `admin` | `Admin@123456` | صلاحيات كاملة: إعدادات النظام، إعفاء الغرامات، إدارة المستخدمين والتقارير |
| **أمين المكتبة (Librarian)** | `librarian` | `Librarian@123456` | مكتب الإعارة السريع، الفهرسة، سداد الغرامات، فحص إخلاء الطرف |
| **طالب مسجل (Student)** | `student2024001` | `Student@123456` | استعراض الفهرس، الحجز المسبق، الاطلاع على إعاراته وغراماته |

> **ملاحظة ذكية:** تحتوي الواجهة الأمامية في الشريط العلوي على قائمة منسدلة سريعة للتبديل الفوري بين هذه الحسابات وتجربة صلاحيات كل دور بضغطة زر واحدة!

---

## 🧪 تشغيل حزمة الاختبارات الآلية (Automated Tests)

يحتوي النظام على 12 اختباراً آلياً تغطي كافة الحالات الحرجة بما فيها اختبار التزامن العالي:

```bash
# من داخل المجلد الرئيسي digital_library:
.venv/bin/pytest backend/tests/ -v
```

**نتيجة الاختبارات:**
```text
backend/tests/test_circulation_and_fines.py::test_issue_return_idempotency_and_fines PASSED
backend/tests/test_concurrency.py::test_concurrent_borrowing_single_copy PASSED
backend/tests/test_database_constraints.py::test_all_tables_exist PASSED
backend/tests/test_database_constraints.py::test_book_copies_integrity_constraint PASSED
backend/tests/test_database_constraints.py::test_duplicate_active_borrowing_rejected PASSED
backend/tests/test_database_constraints.py::test_fine_unique_per_borrowing_and_type PASSED
backend/tests/test_database_constraints.py::test_users_updated_at_trigger PASSED
backend/tests/test_database_constraints.py::test_inventory_integrity_formula PASSED
backend/tests/test_frontend_and_recommendations.py::test_frontend_static_routes_served PASSED
backend/tests/test_frontend_and_recommendations.py::test_book_recommendations_endpoint PASSED
backend/tests/test_reports.py::test_reports_generation_and_export PASSED
backend/tests/test_security_rbac.py::test_rbac_and_idor_protection PASSED

============================== 12 passed in 2.12s ==============================
```

---

## 💾 النسخ الاحتياطي التلقائي والاستعادة

تم تجهيز سكريبتات احترافية لأخذ نسخ احتياطية مضغوطة وتدويرها لمدة 30 يوماً:

```bash
# أخذ نسخة احتياطية فورية
chmod +x infra/backup/backup.sh
./infra/backup/backup.sh

# استعادة نسخة احتياطية محددة
chmod +x infra/backup/restore.sh
./infra/backup/restore.sh /var/backups/elms/elms_backup_20261016_080000.sql.gz
```

---

## 📁 هيكل مجلدات المشروع (Directory Layout)

```
digital_library/
├── backend/
│   ├── alembic/                 # ترحيلات قاعدة البيانات
│   ├── app/
│   │   ├── api/v1/routers/      # مسارات الـ API (Auth, Circulation, Catalog, ...)
│   │   ├── core/                # الإعدادات، قاعدة البيانات، الأمان، معالجة الأخطاء
│   │   ├── models/              # نماذج SQLAlchemy (17 جدول)
│   │   ├── schemas/             # مخططات Pydantic v2
│   │   ├── services/            # منطق الأعمال (Circulation, Fines, Reports, ...)
│   │   ├── tasks/               # المهام المجدولة (APScheduler)
│   │   └── main.py              # نقطة انطلاق التطبيق واستضافة الواجهة
│   ├── scripts/
│   │   └── seed.py              # بذر 2,050 كتاب و300 طالب وبيانات حية واقعية
│   └── tests/                   # حزمة الاختبارات الآلية
├── frontend/
│   ├── index.html               # واجهة المستخدم الرئيسية المتجاوبة (SPA)
│   ├── styles.css               # نظام التصميم البصري والألوان (RTL)
│   └── app.js                   # متحكم الواجهة والتفاعل مع الخادم
├── infra/
│   ├── db/init/01_init.sql      # سكريبت تهيئة PostgreSQL وإضافاتها
│   ├── backup/                  # سكريبتات النسخ الاحتياطي والاستعادة
│   ├── nginx/nginx.conf         # إعدادات Nginx العكسي
│   └── docker-compose.yml       # حاويات التطبيق للإنتاج
└── docs/
    ├── ASSUMPTIONS.md           # الافتراضات وقرارات التصميم
    ├── SECURITY.md              # التدابير الأمنية وحماية الثغرات
    ├── TRACEABILITY.md          # مصفوفة تتبع المتطلبات الكاملة (FR / NFR)
    ├── API.md                   # التوثيق المفصل لمسارات الـ API
    ├── UX.md                    # دليل تجربة وتصميم الواجهة
    └── TESTING.md               # دليل وسيناريوهات الاختبارات الآلية
```

---

**جامعة السودان للعلوم والتكنولوجيا - عمادة شؤون المكتبات** | تم التطوير والاختبار بعناية فائقة لضمان ديمومة واستقرار النظام الأكاديمي.
# ELMS
