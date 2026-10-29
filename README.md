# Roziy Xalqaro Maktabi — veb-sayt

Shovot tumani (Xorazm) dagi Roziy xususiy maktabi (1–9-sinflar) uchun to'liq sayt: **Django REST** backend va **React (Vite)** frontend.

Loyihaning asosiy g'oyasi — **shaffoflik va ishonch**: ota-ona ariza holatini real vaqtda ko'ra oladi, kunlik taomnomani rasm bilan ko'radi, har bir sinfda farzandi nimalarga erishishini oddiy tilda biladi, va haqiqiy ota-onalar videosini ko'radi.

## Tuzilma

```
roziy_school/
├── roziy_maktabi/        # Backend (Django + DRF) — manage.py shu yerda
│   ├── config/           # settings, urls
│   ├── core/             # sayt sozlamalari (singleton), statik sahifalar
│   ├── accounts/         # ota-ona ro'yxatdan o'tishi, JWT login
│   ├── academics/        # sinflar, fanlar, rivojlanish xaritasi (RoadmapItem)
│   ├── admissions/       # qabul arizalari + ariza raqami bilan holat tekshirish
│   ├── meals/            # haftalik taomnoma (rasmlar bilan)
│   ├── gallery/          # fotogalereya
│   ├── staff/            # o'qituvchilar
│   ├── testimonials/     # ota-onalar fikri (matn / video)
│   ├── news/             # yangiliklar
│   └── contacts/         # aloqa xabarlari
└── roziy-frontend/       # Frontend (React + Vite)
    └── src/
        ├── api/          # axios klient va endpointlar
        ├── components/   # Header, Footer
        └── pages/        # Home, About, Academics, Meals, Admissions, ApplicationStatus, ...
```

## Asosiy imkoniyatlar

| Imkoniyat | Tavsif |
|---|---|
| Ariza holatini kuzatish | Ariza yuborilganda `RZ-XXXXXX` raqami beriladi. Ota-ona raqam + telefon orqali holatni (Yangi / Ko'rib chiqilmoqda / Qabul qilindi / Rad etildi) va admin izohini ko'radi. |
| Rivojlanish xaritasi | Har bir sinf uchun "farzandingiz nimalarga erishadi" bosqichlari (admin panelda Grade ichida inline tahrirlanadi). |
| Taomnoma | Haftalik menyu, har bir taom rasmi bilan. |
| Video-intervyular | YouTube havolalari saytda to'g'ridan-to'g'ri pleer sifatida; yuklangan video fayl ham qo'llab-quvvatlanadi. |
| Ota-ona akkaunti | Ro'yxatdan o'tish va JWT orqali kirish. |
| Admin panel | Barcha kontent `/admin/` orqali boshqariladi. |

## Ishga tushirish (lokal)

Talab: **Python 3.11+** va **Node.js 20+**.

### 1. Backend

```powershell
cd roziy_maktabi
pip install -r requirements.txt
copy .env.example .env        # kerak bo'lsa qiymatlarni tahrirlang
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

Backend: <http://127.0.0.1:8000> · Admin: <http://127.0.0.1:8000/admin/>

`.env` da `DB_ENGINE` bo'sh qoldirilsa, SQLite ishlatiladi (PostgreSQL shart emas). PostgreSQL uchun `DB_ENGINE=postgres` va `DB_NAME`, `DB_USER`, `DB_PASSWORD`, `DB_HOST`, `DB_PORT` ni to'ldiring.

### 2. Frontend (alohida terminalda)

```powershell
cd roziy-frontend
npm install
npm run dev
```

Frontend: <http://localhost:5173>

Backend manzili `.env` dagi `VITE_API_BASE_URL` orqali beriladi (standart: `http://127.0.0.1:8000/api`).

### 3. Production build

```powershell
npm run build      # dist/ papkasi hosil bo'ladi
npm run lint
```

## Muhit o'zgaruvchilari

**Backend (`roziy_maktabi/.env`)**

| O'zgaruvchi | Izoh |
|---|---|
| `DJANGO_SECRET_KEY` | Production'da albatta tasodifiy, uzun qiymat bering |
| `DJANGO_DEBUG` | Production'da `False` |
| `DJANGO_ALLOWED_HOSTS` | Vergul bilan ajratilgan domenlar |
| `DB_ENGINE`, `DB_NAME`, `DB_USER`, `DB_PASSWORD`, `DB_HOST`, `DB_PORT` | Bo'sh `DB_ENGINE` = SQLite |
| `CORS_ALLOWED_ORIGINS` | Frontend manzil(lar)i, vergul bilan |

**Frontend (`roziy-frontend/.env`)**

| O'zgaruvchi | Izoh |
|---|---|
| `VITE_API_BASE_URL` | Backend API manzili, masalan `https://api.sayt.uz/api` |

## API endpointlari

Barcha manzillar `/api/` bilan boshlanadi.

| Manzil | Usul | Vazifa |
|---|---|---|
| `core/settings/` | GET | Sayt sozlamalari (telefon, litsenziya, to'lov, ...) |
| `core/pages/` | GET | Statik sahifalar |
| `academics/grades/` | GET | Sinflar (fanlar va `roadmap_items` bilan) |
| `academics/subjects/` | GET | Fanlar |
| `admissions/apply/` | POST | Ariza yuborish — javobda `tracking_code` qaytadi |
| `admissions/status/` | POST | `tracking_code` + `parent_phone` bilan holatni tekshirish |
| `meals/` | GET | Haftalik taomnoma |
| `gallery/` | GET | Galereya kategoriyalari va rasmlar |
| `staff/` | GET | O'qituvchilar |
| `testimonials/` | GET | Ota-onalar fikri |
| `news/` | GET | Yangiliklar |
| `contacts/send/` | POST | Aloqa xabari yuborish |
| `accounts/register/` | POST | Ota-ona ro'yxatdan o'tishi |
| `accounts/me/` | GET | Joriy foydalanuvchi (JWT kerak) |
| `auth/login/`, `auth/refresh/` | POST | JWT olish / yangilash |

## Kontentni to'ldirish (admin panelda)

Sayt "tirik" ko'rinishi uchun `/admin/` da quyidagilarni to'ldiring:

1. **Sayt sozlamalari** — telefon, manzil, litsenziya rasmlari, oylik to'lov.
2. **Sinflar → Roadmap items** — har sinf uchun 4–6 ta bosqich, oddiy tilda.
3. **Taomnoma** — kunlik taomlar va ularning haqiqiy rasmlari.
4. **Testimonials** — YouTube havolasi yoki video fayl bilan ota-onalar intervyusi.
5. **Galereya va O'qituvchilar** — haqiqiy fotosuratlar.
6. **Arizalar** — holatni o'zgartiring va `status_note` ga ota-onaga ko'rinadigan izoh yozing.

## Texnologiyalar

- **Backend:** Django 6.1, Django REST Framework, SimpleJWT, django-filter, django-cors-headers, Pillow
- **Frontend:** React, React Router, Axios, Vite, oxlint
- **Dizayn:** Fraunces (sarlavhalar) + Inter (matn), navy / oltin / krem palitra

## Production uchun eslatmalar

- `DJANGO_DEBUG=False`, kuchli `DJANGO_SECRET_KEY`, aniq `DJANGO_ALLOWED_HOSTS` va `CORS_ALLOWED_ORIGINS`.
- Yuklangan rasm/videolar (`media/`) va statik fayllar uchun Nginx yoki bulutli saqlash sozlang.
- `python manage.py collectstatic` va Gunicorn/uWSGI orqali ishga tushiring.
- `admissions/status/` endpointiga so'rovlar sonini cheklash (rate limit) qo'shish tavsiya etiladi.
