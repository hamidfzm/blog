---
layout: post
title: آشنایی با Neon؛ پستگرس سرورلس
---

اگر چند سال است که با Postgres کار می‌کنید، احتمالاً با همان الگوی همیشگی آشنایید:
یک سرور (یا یک کلاستر RDS) که همیشه روشن است، صورت‌حساب ماهانه‌اش هم همیشه روشن است،
و هر بار که می‌خواهید یک محیط آزمایشی برای یک تیکت بسازید، یا باید از روی پشتیبان یک
دیتابیس دیگر بالا بیاورید یا کل داده‌ها را در محیط استیجینگ به اشتراک بگذارید.

[Neon](https://neon.tech) یک نسخه‌ی **سرورلس** از Postgres است که این الگو را عوض می‌کند.

### چیزی که Neon فرق دارد

- **جدا بودن compute و storage.** ذخیره‌سازی روی S3 می‌نشیند و compute هر وقت لازم
  باشد بالا می‌آید. اگر کسی به دیتابیس وصل نباشد، compute به‌خواب می‌رود
  (scale-to-zero) و در عمل پولی هم برایش نمی‌دهید.
- **برنچ گرفتن از دیتابیس مثل گیت.** یک کپی نوشتاری-روی-نوشتن از کل دیتابیس را در
  چند ثانیه بسازید. مناسب برای محیط آزمایشی هر pull request، یا تست مهاجرت‌های
  schema بدون ترس از خراب کردن production.
- **بازگشت در زمان.** نسخه‌های قبلی دیتابیس به‌صورت پیش‌فرض نگه‌داشته می‌شوند؛
  می‌توانید یک برنچ از پنج دقیقه‌ی پیش بسازید بدون هیچ کار اضافه.
- **سازگار با هر کتابخانه‌ی Postgres.** پروتکل همان Postgres است، پس درایور psycopg،
  pgx، Prisma، Drizzle، SQLAlchemy و … بدون تغییر کار می‌کنند.

### یک نمونه‌ی سریع

ساخت دیتابیس از CLI:

```bash
npm i -g neonctl
neonctl auth
neonctl projects create --name my-app
```

اتصال از پایتون با psycopg:

```python
import os
import psycopg

with psycopg.connect(os.environ["DATABASE_URL"]) as conn:
    with conn.cursor() as cur:
        cur.execute("select version()")
        print(cur.fetchone()[0])
```

ساخت یک برنچ برای یک pull request:

```bash
neonctl branches create --name pr-1234 --parent main
```

این یک دیتابیس کامل و قابل‌نوشتن می‌سازد که محتوای دقیق دیتابیس اصلی را در لحظه‌ی
ساخت دارد، با یک connection string جدا.

### کجاها به درد می‌خورد

- پروژه‌هایی که الگوی **مصرفِ نامنظم** دارند (مثلاً ابزار داخلی یا کرون‌جاب‌ها).
- محیط‌های **استیجینگ به ازای هر PR** که می‌خواهید واقعاً جدا باشند.
- پروژه‌های جانبی که نمی‌خواهید برای دیتابیس‌شان ماهی ۲۰ دلار بدهید وقتی دو روز
  در ماه استفاده می‌شود.
- اپلیکیشن‌های Edge / Serverless که تعداد کانکشن زیاد می‌سازند؛ Neon یک
  [HTTP/WebSocket proxy](https://neon.tech/docs/serverless/serverless-driver)
  برای این کار دارد.

### کجاها به‌دردتان نمی‌خورد

- بار خیلی سنگین و پایدار write که نیاز به یک نمونه‌ی همیشه گرم با CPU/RAM ثابت
  دارد؛ آنجا یک Postgres مدیریت‌شده‌ی معمولی هم می‌تواند ارزان‌تر باشد.
- وقتی مقررات data residency خیلی محدود است و باید روی زیرساخت خودتان بنشیند.

---

<div dir="ltr" markdown="1">

## Introducing Neon: serverless Postgres

If you've been around Postgres long enough, you know the usual shape: one
always-on server (or an RDS cluster), an always-on monthly bill, and any time
you need a sandbox for a ticket you either restore from a snapshot or share
the staging database with the rest of the team.

[Neon](https://neon.tech) is a **serverless** take on Postgres that changes
that shape.

### What's different

- **Separation of compute and storage.** Storage lives on S3; compute spins up
  on demand and goes to sleep when no one is connected (**scale-to-zero**),
  meaning you effectively don't pay for idle.
- **Git-style database branches.** Spin up a copy-on-write clone of the whole
  database in seconds. Perfect for a per-PR preview environment, or for
  testing a schema migration without touching production.
- **Point-in-time restore by default.** Older versions of the database are
  retained, so you can branch from "five minutes ago" without setting up
  anything special.
- **Wire-compatible.** It speaks the Postgres protocol, so existing drivers
  (psycopg, pgx, Prisma, Drizzle, SQLAlchemy, ...) work as-is.

### Quick taste

Create a project from the CLI:

```bash
npm i -g neonctl
neonctl auth
neonctl projects create --name my-app
```

Connect from Python with psycopg:

```python
import os
import psycopg

with psycopg.connect(os.environ["DATABASE_URL"]) as conn:
    with conn.cursor() as cur:
        cur.execute("select version()")
        print(cur.fetchone()[0])
```

Branch the database for a pull request:

```bash
neonctl branches create --name pr-1234 --parent main
```

You get a fully writable database that contains exactly what `main` had at
branch time, with its own connection string.

### Where it shines

- Projects with **spiky or unpredictable traffic** (internal tools, cron jobs).
- **Per-PR staging environments** that really need to be isolated.
- Side projects where you don't want to pay $20/month for a database you use
  two days a month.
- Edge / serverless apps that create a lot of short-lived connections; Neon
  ships an
  [HTTP/WebSocket proxy](https://neon.tech/docs/serverless/serverless-driver)
  built for this.

### Where it doesn't

- Heavy, steady write workloads that want a permanently-warm instance with
  fixed CPU/RAM; a regular managed Postgres can come out cheaper.
- Strict data residency rules that require the database to live on your own
  infrastructure.

</div>
