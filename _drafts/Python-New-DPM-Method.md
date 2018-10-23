---
layout: post
title: روش جدید برای مدیریت Dependencyها در پایتون
image: /assets/2018-10-24-Python-New-DPM-Method/pipenv.png
---

![Pipenv](/assets/2018-10-24-Python-New-DPM-Method/pipenv.png){:style="margin: 1em auto; display: block" hspace="10" vspace="10" height="300"}
خیلی وقت بود که توی پایتون کمبود ابزار‌های مثل yarn، npm و … احساس می‌شد
 تا اینکه چند وقت پیش ابزاری به اسم [Pipenv](https://github.com/pypa/pipenv) ساخته شد، 
 تا هر چیز خوبی که این پکیج‌منیجر‌ها میدن رو تو پایتون هم داشته باشیم و در عین حال ویندوز رو هم آدم حساب کنه (مثل اینکه اون یکی‌ها حساب نمی‌کنن).

پایتون به تنهایی خیلی از قابلیت‌ها رو نداره و برای اینکه مثلا به یه دیتابیس خاص وصل شیم مجبوریم 
از ابزار‌هایی مثل pip استفاده کنیم تا پکیج درایور اون دیتابیس رو نصب کنیم.
 
در عین حال وقتی این پکیج‌ها رو نصب می‌کنیم اگر اون‌ها رو توی یه محیط ایزوله نصب نکنیم ممکنه به پایتون اصلی سیستم صدمه بخوره و در کل شلوغ بشه. برای همین دهه‌هاست برنامه‌نویس‌های پایتون از virtualenv تو محیط پروژه‌هاشون استفاده میکنن.
virtualenv به ما این امکان رو میده که یه کپی از ورژن انتخابی پایتون (۲.۷، ۳.۶ و … ) داشته باشیم و تو پروژه ازش استفاده کنیم. هر بلایی که میخوایم سرش بیاریم و 
اگر هم خراب شد پاکش کنیم و یه بار دیگه بسازیمش.


Pipenv ابزاری هست که همون‌طور که از اسمش مشخصه (pip + env = pipenv) هر دو این قابلیت‌ها رو به ما میده.
این ابزار رو [Kenneth Reitz](https://www.kennethreitz.org/) (سازنده ماژول معروف requests توی پایتون) نوشته.

کار با این ماژول خیلی ساده هست و برای نصبش دستور زیر رو میزنیم:
‍‍‍

```bash
~$ pip install --user pipenv
```

pip feeze
pipenv piplock

pipenv vs requirements

manager virtual env

get started:
pip install pipenv

mkdir dir
cd dir
pipenv

no more source

pipenv install flask

steps

cd subdirectory
pip env install
--where

pipenv shell
exit

package safty