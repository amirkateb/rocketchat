<div align="right">

**فارسی** | [English](README.en.md)

</div>

---

# نصب یک دستوری RocketChat

<div dir="rtl">

**نصب آسان RocketChat با Docker، SSL و پیکربندی خودکار**

[amirktb](https://katebsaber.ir)

---

## امکانات

- نصب با یک دستور
- SSL خودکار با Let's Encrypt و تمدید اتوماتیک
- **جدید:** تنظیم خودکار Cronjob برای نگهداری و تمدید سرتیفیکت SSL
- مبتنی بر  Docker Compose پروژه راکت چت
- پشتیبانی از Docker registry mirror برای ایران
- تولید خودکار رمزهای امن
- بررسی نیازمندی‌های سیستم قبل از نصب
- بررسی DNS دامنه
- پشتیبانی از Ubuntu، Debian، Rocky Linux، CentOS، AlmaLinux
---

## پیش‌نیازها

**سخت‌افزار:**
- حداقل 2GB رم (4GB پیشنهادی)
- حداقل 2 هسته CPU (پیشنهادی)
- حداقل 20GB فضای خالی

**نرم‌افزار:**
- Ubuntu 20.04+، Debian 10+، Rocky Linux 8+، CentOS 7+، AlmaLinux 8+
- دسترسی root یا sudo
- دامنه یا ساب‌دامنه که به IP سرور شما اشاره کند
- پورت‌های 80 و 443 باز باشند

---

## نصب

### روش پیشنهادی: دانلود و اجرا

</div>

```bash
curl -fsSL https://raw.githubusercontent.com/amirktb/rocketchat/main/rocketchat-installer.sh
chmod +x rocketchat-installer.sh
sudo ./rocketchat-installer.sh
```

<div dir="rtl">

### روش جایگزین: نصب یک خطی

⚠️ **توجه**: این روش ممکن است با ورودی تعاملی مشکل داشته باشد. روش بالا را امتحان کنید.

</div>

```bash
curl -fsSL https://raw.githubusercontent.com/amirktb/rocketchat/main/rocketchat-installer.sh | sudo bash
```

<div dir="rtl">

### یا clone کردن از گیت‌هاب

</div>

```bash
git clone https://github.com/amirktb/rocketchat.git
cd rocketchat-one-command
chmod +x rocketchat-installer.sh
sudo ./rocketchat-installer.sh
```

<div dir="rtl">

---

## مراحل نصب

اسکریپت این کارها را انجام می‌دهد:

1. بررسی منابع سیستم (رم، CPU)
2. تعیین مسیر نصب (پیش‌فرض: `~/amirktb-rocketchat`)
3. ایجاد ساختار پوشه‌ها (`data/mongodb`, `data/uploads`, ...)
4. دریافت اطلاعات دامنه و بررسی DNS
5. نصب یا آپدیت Docker
6. تولید رمزهای امن و ساخت فایل `.env`
7. تنظیم Cronjob هفتگی برای بروزرسانی SSL (اختیاری)
8. راه‌اندازی کانتینرها
9. **انتظار:** بررسی لاگ‌ها تا زمانی که سرور کاملاً آماده شود (جلوگیری از خطای Bad Gateway)
10. نمایش دستورات فایروال بر اساس سیستم عامل شما

---

## ساختار فایل‌ها

مسیر پیش‌فرض نصب `~/amirkyb-rocketchat` است:

</div>

```
amirktb-rocketchat/
├── docker-compose.yml       # تنظیمات Docker Compose
├── .env                     # متغیرها و رمزها
├── renew-cert.sh            # اسکریپت تمدید خودکار (توسط Cron اجرا می‌شود)
├── cron.log                 # لاگ‌های مربوط به Cronjob
├── data/
│   ├── mongodb/             # فایل‌های دیتابیس MongoDB
│   ├── uploads/             # فایل‌های آپلود شده
│   └── certs/               # گواهی‌های SSL
└── rocketchat-installer.sh  # اسکریپت نصب
```

<div dir="rtl">

---

## تنظیمات

### مشاهده رمزها

تمام رمزها و تنظیمات در فایل `.env` ذخیره می‌شوند:

</div>

```bash
cat .env
```

<div dir="rtl">

### دسترسی به RocketChat

بعد از نصب موفقیت‌آمیز:

</div>

```
آدرس: https://your-domain.com
نکته: اولین کاربری که ثبت‌نام می‌کند، ادمین می‌شود
```

<div dir="rtl">

---

## تنظیم فایروال

اسکریپت در پایان نصب، نوع فایروال شما را تشخیص می‌دهد (UFW یا Firewalld) و دستورات دقیق را نمایش می‌دهد.
مثال کلی برای UFW:

</div>

```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw reload
```

<div dir="rtl">

---

## نگهداری خودکار (Cronjob)

در حین نصب، از شما پرسیده می‌شود که آیا مایل به فعال‌سازی Cronjob هستید یا خیر. این قابلیت:
- هفته‌ای یک بار (یکشنبه ساعت ۳ صبح) اجرا می‌شود.
- اسکریپت `renew-cert.sh` را اجرا می‌کند.
- سرویس Traefik را ریستارت می‌کند تا از اعمال آخرین گواهی‌های SSL اطمینان حاصل شود.

---

## آپدیت

برای آپدیت RocketChat، فایل [UPDATE.md](docs/UPDATE.md) را ببینید.

---

## رفع مشکلات

برای مشکلات رایج و راه‌حل‌ها، فایل [TROUBLESHOOTING.md](docs/TROUBLESHOOTING.md) را ببینید.

### پشتیبان‌گیری

</div>

```bash
# بکاپ از پوشه data
tar -czf rocketchat-backup-$(date +%Y%m%d).tar.gz data/

# بکاپ از فایل env
cp .env .env.backup
```

<div dir="rtl">

---

## متوقف کردن / راه‌اندازی RocketChat

</div>

```bash
# ابتدا وارد پوشه نصب شوید
cd ~/netadminplus-rocketchat

# متوقف کردن سرویس‌ها
docker compose down

# راه‌اندازی سرویس‌ها
docker compose up -d

# مشاهده لاگ‌ها
docker compose logs -f
```

<div dir="rtl">

---

## حذف کامل

</div>

```bash
cd ~/netadminplus-rocketchat

# متوقف و حذف کانتینرها
docker compose down -v

# بازگشت به پوشه قبل
cd ..

# حذف پوشه نصب (⚠️ این کار همه داده‌ها را پاک می‌کند!)
rm -rf netadminplus-rocketchat/

# حذف Docker (اختیاری)
# Ubuntu/Debian: sudo apt remove docker-ce docker-ce-cli containerd.io
# Rocky/CentOS: sudo dnf remove docker-ce docker-ce-cli containerd.io
```

<div dir="rtl">


## لایسنس

MIT License - استفاده و تغییر آزاد است

---

## سازنده

*amirktb**


---

**ساخته شده با ❤️**

</div>
