

```markdown
# 🔒 Rubingo Proxy Guide | راهنمای پروکسی روبینگو

---

## 📖 فهرست مطالب

- [پروکسی چیست؟](#-پروکسی-چیست)
- [نصب و آماده‌سازی](#-نصب-و-آماده‌سازی)
- [روش‌های تنظیم پروکسی](#-روشهای-تنظیم-پروکسی)
- [فعال و غیرفعال کردن](#-فعال-و-غیرفعال-کردن)
- [تغییر پروکسی در زمان اجرا](#-تغییر-پروکسی-در-زمان-اجرا)
- [مثال‌های کاربردی](#-مثالهای-کاربردی)
- [فرمت آدرس پروکسی](#-فرمت-آدرس-پروکسی)
- [جدول متدها](#-جدول-متدها)
- [سوالات متداول](#-سوالات-متداول)
- [عیب‌یابی](#-عیبیابی)

---

## 🤔 پروکسی چیست؟

> **💡 توضیح ساده:**
> 
> پروکسی مثل یک **واسطه** بین شما و اینترنت است.
> وقتی پروکسی فعال باشد، درخواست‌های شما ابتدا به سرور پروکسی می‌روند
> و سپس از آنجا به روبیکا ارسال می‌شوند.
> 
> **چرا استفاده کنیم؟**
> - دور زدن محدودیت‌های شبکه
> - افزایش امنیت و حریم خصوصی
> - تغییر IP برای جلوگیری از بلاک شدن

### بدون پروکسی:
```
شما ──────────────────────────► روبیکا
```

### با پروکسی:
```
شما ────► سرور پروکسی ────► روبیکا
```

---

## 📦 نصب و آماده‌سازی

> **💡 توضیح ساده:**
> 
> قبل از استفاده از پروکسی، باید آدرس پروکسی را داشته باشید.
> این آدرس معمولاً از سرویس‌دهنده پروکسی به شما داده می‌شود.

### آدرس پروکسی شما چیزی شبیه این است:

```
http://username:password@host:port
```

**مثال واقعی:**
```
http://spzl3fr6ok:zvnucb12yw9ZB=u3AW@gate.decodo.com:10001
```

| بخش | مقدار | توضیح |
|-----|-------|-------|
| `http://` | پروتکل | نوع اتصال |
| `spzl3fr6ok` | username | نام کاربری پروکسی |
| `zvnucb12yw9ZB=u3AW` | password | رمز عبور پروکسی |
| `gate.decodo.com` | host | آدرس سرور پروکسی |
| `10001` | port | پورت سرور پروکسی |

---

## 🔧 روش‌های تنظیم پروکسی

### روش ۱: تنظیم هنگام ساخت کلاینت (پیشنهادی ✅)

> **💡 توضیح ساده:**
> 
> وقتی کلاینت (ربات) را می‌سازید، آدرس پروکسی را هم بدهید.
> **توجه:** فقط دادن آدرس کافی نیست! باید آن را فعال هم کنید.

```go
package main

import (
    "log"
    "github.com/yourname/rubingo"
)

func main() {
    // آدرس پروکسی خودتان
    myProxy := "http://spzl3fr6ok:zvnucb12yw9ZB=u3AW@gate.decodo.com:10001"

    // ساخت کلاینت با پروکسی
    client, err := rubingo.NewClient("my_bot",
        rubingo.WithProxy(myProxy),          // ← آدرس پروکسی
        rubingo.WithProxyEnabled(true),       // ← فعال کردن پروکسی
    )
    if err != nil {
        log.Fatal(err)
    }

    // از اینجا به بعد همه درخواست‌ها از پروکسی رد می‌شوند
    client.Connect()
    // ...
}
```

> **⚠️ نکته مهم:**
> 
> اگر فقط `WithProxy(myProxy)` بنویسید ولی `WithProxyEnabled(true)` ننویسید،
> پروکسی **غیرفعال** خواهد بود! هر دو لازم است.

---

### روش ۲: تنظیم بدون فعال‌سازی (فعال کردن بعداً)

> **💡 توضیح ساده:**
> 
> آدرس پروکسی را ذخیره کنید ولی هنوز فعال نکنید.
> بعداً در هر جای کد که خواستید فعالش کنید.

```go
// فقط آدرس را تنظیم کن (هنوز غیرفعال است)
client, _ := rubingo.NewClient("my_bot",
    rubingo.WithProxy("http://user:pass@host:port"),
    // WithProxyEnabled نداریم → پس غیرفعال است
)

client.Connect()

// ... کدهای دیگر (بدون پروکسی کار می‌کنند) ...

// حالا فعالش کن!
client.EnableProxy()

// از اینجا به بعد با پروکسی است
```

---

### روش ۳: تنظیم بعد از ساخت کلاینت

> **💡 توضیح ساده:**
> 
> اگر هنگام ساخت کلاینت آدرس پروکسی را نداشتید،
> می‌توانید بعداً آن را تنظیم کنید.

```go
client, _ := rubingo.NewClient("my_bot")
client.Connect()

// بعداً آدرس پروکسی را تنظیم و فعال کن
client.SetProxy("http://user:pass@host:port")
client.EnableProxy()
```

---

## ⚡ فعال و غیرفعال کردن

> **💡 توضیح ساده:**
> 
> مثل یک **کلید برق** عمل می‌کند:
> - `EnableProxy()` = روشن کردن 🟢
> - `DisableProxy()` = خاموش کردن 🔴
> - `IsProxyEnabled()` = چک کردن روشن یا خاموش بودن ❓

### فعال کردن:

```go
client.EnableProxy()
```

### غیرفعال کردن:

```go
client.DisableProxy()
```

### بررسی وضعیت:

```go
if client.IsProxyEnabled() {
    fmt.Println("پروکسی فعال است ✅")
} else {
    fmt.Println("پروکسی غیرفعال است ❌")
}
```

> **💡 نکته:**
> 
> وقتی پروکسی را فعال می‌کنید، **فعال می‌ماند** تا زمانی که غیرفعالش کنید.
> نیازی نیست قبل از هر درخواست دوباره `EnableProxy()` بزنید.
> اما اگر هم هر بار بزنید، **هیچ مشکلی ایجاد نمی‌شود**.

---

## 🔄 تغییر پروکسی در زمان اجرا

> **💡 توضیح ساده:**
> 
> در حین اجرای برنامه می‌توانید:
> - آدرس پروکسی را عوض کنید
> - پروکسی را خاموش و روشن کنید
> 
> نیازی به ریستارت برنامه نیست!

```go
// شروع با پروکسی اول
client.SetProxy("http://user1:pass1@proxy1.com:8080")
client.EnableProxy()

// ارسال شماره (با پروکسی ۱)
result, _ := client.SendCodeExt("989123456789", "")

// تغییر به پروکسی دوم
client.SetProxy("http://user2:pass2@proxy2.com:9090")
// نیازی به EnableProxy نیست چون قبلاً فعال شده

// ارسال کد تایید (با پروکسی ۲)
signIn, _ := client.SignInExt(code, phone, hash, tmpSession)

// غیرفعال کردن پروکسی
client.DisableProxy()

// ارسال پیام (بدون پروکسی)
client.SendMessage(...)

// دوباره فعال کردن پروکسی
client.EnableProxy()
```

---

## 📚 مثال‌های کاربردی

### مثال ۱: لاگین کامل با پروکسی همیشه فعال

> **💡 توضیح ساده:**
> 
> ساده‌ترین حالت: پروکسی را فعال کن و همه کارها با پروکسی انجام شود.

```go
package main

import (
    "bufio"
    "fmt"
    "log"
    "os"
    "strings"

    "github.com/yourname/rubingo"
)

func main() {
    // ═══════════════════════════════════════════════════════
    //  ساخت کلاینت با پروکسی فعال
    // ═══════════════════════════════════════════════════════
    client, err := rubingo.NewClient("my_bot",
        rubingo.WithProxy("http://spzl3fr6ok:zvnucb12yw9ZB=u3AW@gate.decodo.com:10001"),
        rubingo.WithProxyEnabled(true),
        rubingo.WithSessionCallback(func(data rubingo.SessionData) {
            fmt.Printf("✅ Session saved: %s\n", data.GUID)
        }),
    )
    if err != nil {
        log.Fatal(err)
    }

    if err := client.Connect(); err != nil {
        log.Fatal(err)
    }

    reader := bufio.NewReader(os.Stdin)

    // ═══════════════════════════════════════════════════════
    //  مرحله ۱: ارسال شماره (با پروکسی ✅)
    // ═══════════════════════════════════════════════════════
    fmt.Print("📱 Phone number: ")
    phone, _ := reader.ReadString('\n')
    phone = strings.TrimSpace(phone)

    result, err := client.SendCodeExt(phone, "")
    if err != nil {
        log.Fatal(err)
    }
    fmt.Println("✅ Code sent!")

    // ═══════════════════════════════════════════════════════
    //  مرحله ۲: ارسال کد تایید (با پروکسی ✅)
    // ═══════════════════════════════════════════════════════
    fmt.Print("🔑 Code: ")
    code, _ := reader.ReadString('\n')
    code = strings.TrimSpace(code)

    signIn, err := client.SignInExt(code, phone, result.PhoneCodeHash, result.TmpSession)
    if err != nil {
        log.Fatal(err)
    }

    client.ApplySignInResult(signIn)
    fmt.Printf("✅ Logged in! GUID: %s\n", signIn.UserGUID)

    // ═══════════════════════════════════════════════════════
    //  مرحله ۳: استفاده از ربات (با پروکسی ✅)
    // ═══════════════════════════════════════════════════════
    client.OnMessage(func(u *rubingo.Update) {
        fmt.Printf("📩 %s: %s\n", u.AuthorGUID(), u.Text())
        u.Reply("سلام! پیام شما دریافت شد")
    })

    fmt.Println("🤖 Bot is running with proxy...")
    client.Run()
}
```

---

### مثال ۲: لاگین با کنترل پروکسی در هر مرحله

> **💡 توضیح ساده:**
> 
> در این مثال، پروکسی فقط برای بعضی مراحل فعال است
> و برای بعضی مراحل غیرفعال.

```go
package main

import (
    "bufio"
    "fmt"
    "log"
    "os"
    "strings"

    "github.com/yourname/rubingo"
)

func main() {
    client, err := rubingo.NewClient("my_bot",
        rubingo.WithProxy("http://user:pass@host:port"),
        // پروکسی غیرفعال (پیش‌فرض)
    )
    if err != nil {
        log.Fatal(err)
    }

    client.Connect()
    reader := bufio.NewReader(os.Stdin)

    // ═══════════════════════════════════════════════════════
    //  مرحله ۱: ارسال شماره → پروکسی فعال
    // ═══════════════════════════════════════════════════════
    client.EnableProxy()
    fmt.Printf("🔒 Proxy: %v\n", client.IsProxyEnabled()) // true

    fmt.Print("Phone: ")
    phone, _ := reader.ReadString('\n')
    phone = strings.TrimSpace(phone)

    result, err := client.SendCodeExt(phone, "")
    if err != nil {
        log.Fatal(err)
    }

    // ═══════════════════════════════════════════════════════
    //  مرحله ۲: ارسال کد تایید → پروکسی غیرفعال
    // ═══════════════════════════════════════════════════════
    client.DisableProxy()
    fmt.Printf("🔓 Proxy: %v\n", client.IsProxyEnabled()) // false

    fmt.Print("Code: ")
    code, _ := reader.ReadString('\n')
    code = strings.TrimSpace(code)

    signIn, err := client.SignInExt(code, phone, result.PhoneCodeHash, result.TmpSession)
    if err != nil {
        log.Fatal(err)
    }

    client.ApplySignInResult(signIn)

    // ═══════════════════════════════════════════════════════
    //  مرحله ۳: اجرای ربات → پروکسی دوباره فعال
    // ═══════════════════════════════════════════════════════
    client.EnableProxy()
    fmt.Printf("🔒 Proxy: %v\n", client.IsProxyEnabled()) // true

    client.OnMessage(func(u *rubingo.Update) {
        u.Reply("سلام!")
    })

    client.Run()
}
```

---

### مثال ۳: تغییر پروکسی بین درخواست‌ها

> **💡 توضیح ساده:**
> 
> اگر چند پروکسی دارید، می‌توانید بین آنها جابجا شوید.

```go
package main

import (
    "fmt"
    "log"

    "github.com/yourname/rubingo"
)

func main() {
    proxies := []string{
        "http://user1:pass1@proxy1.com:8080",
        "http://user2:pass2@proxy2.com:9090",
        "http://user3:pass3@proxy3.com:7070",
    }

    client, err := rubingo.NewClient("my_bot",
        rubingo.WithProxy(proxies[0]),
        rubingo.WithProxyEnabled(true),
    )
    if err != nil {
        log.Fatal(err)
    }

    client.Connect()

    // استفاده از پروکسی ۱
    fmt.Println("Using proxy 1")
    client.SendCodeExt("989123456789", "")

    // تغییر به پروکسی ۲
    client.SetProxy(proxies[1])
    fmt.Println("Switched to proxy 2")

    // تغییر به پروکسی ۳
    client.SetProxy(proxies[2])
    fmt.Println("Switched to proxy 3")

    // غیرفعال کردن پروکسی (اتصال مستقیم)
    client.DisableProxy()
    fmt.Println("Direct connection (no proxy)")
}
```

---

### مثال ۴: لاگین با 2FA و پروکسی

> **💡 توضیح ساده:**
> 
> اگر حساب کاربری شما رمز دو مرحله‌ای (Two-Factor) دارد،
> باید ابتدا رمز دو مرحله‌ای را ارسال کنید سپس کد تایید را.
> در تمام این مراحل می‌توانید از پروکسی استفاده کنید.

```go
package main

import (
    "bufio"
    "errors"
    "fmt"
    "log"
    "os"
    "strings"

    "github.com/yourname/rubingo"
)

func main() {
    client, err := rubingo.NewClient("my_bot",
        rubingo.WithProxy("http://user:pass@host:port"),
        rubingo.WithProxyEnabled(true), // همه مراحل با پروکسی
    )
    if err != nil {
        log.Fatal(err)
    }

    client.Connect()
    reader := bufio.NewReader(os.Stdin)

    fmt.Print("📱 Phone: ")
    phone, _ := reader.ReadString('\n')
    phone = strings.TrimSpace(phone)

    // ═══════════════════════════════════════════════════════
    //  ارسال شماره
    // ═══════════════════════════════════════════════════════
    result, err := client.SendCodeExt(phone, "")

    // اگر 2FA فعال بود
    if err != nil && errors.Is(err, rubingo.ErrSendPassKey) {
        fmt.Printf("🔐 2FA required! Hint: %s\n", result.AuthStatus.HintPassKey)

        for {
            fmt.Print("Enter 2FA password: ")
            passKey, _ := reader.ReadString('\n')
            passKey = strings.TrimSpace(passKey)

            result, err = client.SendCodeExt(phone, passKey)
            if err == nil {
                break
            }

            if errors.Is(err, rubingo.ErrInvalidPassKey) {
                fmt.Println("❌ Wrong password, try again")
                continue
            }

            log.Fatal(err)
        }
    } else if err != nil {
        log.Fatal(err)
    }

    fmt.Println("✅ Code sent!")

    // ═══════════════════════════════════════════════════════
    //  ارسال کد تایید
    // ═══════════════════════════════════════════════════════
    fmt.Print("🔑 Code: ")
    code, _ := reader.ReadString('\n')
    code = strings.TrimSpace(code)

    signIn, err := client.SignInExt(code, phone, result.PhoneCodeHash, result.TmpSession)
    if err != nil {
        log.Fatal(err)
    }

    client.ApplySignInResult(signIn)
    fmt.Printf("✅ Welcome %s %s!\n", signIn.FirstName, signIn.LastName)
}
```

---

### مثال ۵: بدون پروکسی (حالت پیش‌فرض)

> **💡 توضیح ساده:**
> 
> اگر نیازی به پروکسی ندارید، کافیست هیچ تنظیم پروکسی ننویسید.
> به صورت پیش‌فرض **غیرفعال** است.

```go
// بدون هیچ تنظیم پروکسی - اتصال مستقیم
client, _ := rubingo.NewClient("my_bot")
client.Connect()
// همه چیز مستقیم و بدون پروکسی کار می‌کند
```

---

## 📋 فرمت آدرس پروکسی

> **💡 توضیح ساده:**
> 
> پروکسی‌ها فرمت‌های مختلفی دارند. در جدول زیر می‌توانید ببینید
> کدام فرمت‌ها پشتیبانی می‌شوند.

### فرمت‌های پشتیبانی شده:

| نوع | فرمت | مثال |
|-----|------|------|
| HTTP با رمز | `http://user:pass@host:port` | `http://admin:1234@proxy.com:8080` |
| HTTP بدون رمز | `http://host:port` | `http://proxy.com:8080` |
| HTTPS با رمز | `https://user:pass@host:port` | `https://admin:1234@proxy.com:443` |
| SOCKS5 با رمز | `socks5://user:pass@host:port` | `socks5://admin:1234@proxy.com:1080` |
| SOCKS5 بدون رمز | `socks5://host:port` | `socks5://proxy.com:1080` |

### ⚠️ نکته مهم درباره کاراکترهای خاص در رمز عبور:

اگر رمز عبور پروکسی شما شامل کاراکترهای خاص مثل `@` ، `=` ، `#` باشد:

```go
// ❌ اشتباه - اگر رمز شامل @ باشد
proxy := "http://user:p@ss@host:port"

// ✅ درست - از URL encoding استفاده کنید
proxy := "http://user:p%40ss@host:port"
```

| کاراکتر | URL Encoded |
|----------|-------------|
| `@` | `%40` |
| `#` | `%23` |
| `=` | `%3D` |
| `:` | `%3A` |
| `/` | `%2F` |
| `?` | `%3F` |
| `&` | `%26` |
| `+` | `%2B` |
| ` ` (فاصله) | `%20` |

---

## 📊 جدول متدها

### 🔧 تنظیمات (Options) - هنگام ساخت کلاینت

| متد | توضیح | مثال |
|-----|-------|------|
| `WithProxy(url)` | تنظیم آدرس پروکسی | `WithProxy("http://user:pass@host:port")` |
| `WithProxyEnabled(bool)` | فعال/غیرفعال هنگام ساخت | `WithProxyEnabled(true)` |

### ⚡ متدها - در زمان اجرا

| متد | توضیح | برگشتی |
|-----|-------|--------|
| `client.SetProxy(url)` | تغییر آدرس پروکسی | - |
| `client.EnableProxy()` | فعال کردن پروکسی | - |
| `client.DisableProxy()` | غیرفعال کردن پروکسی | - |
| `client.IsProxyEnabled()` | آیا پروکسی فعال است؟ | `bool` |

### 📌 رفتار پیش‌فرض

| حالت | توضیح |
|------|-------|
| پیش‌فرض | پروکسی **غیرفعال** است |
| بعد از `EnableProxy()` | پروکسی **فعال می‌ماند** تا `DisableProxy()` صدا شود |
| بعد از `DisableProxy()` | پروکسی **غیرفعال می‌ماند** تا `EnableProxy()` صدا شود |
| `SetProxy()` | فقط آدرس را عوض می‌کند، وضعیت فعال/غیرفعال تغییر نمی‌کند |

---

## 🌍 پروکسی روی چه بخش‌هایی اعمال می‌شود؟

> **💡 توضیح ساده:**
> 
> وقتی پروکسی فعال باشد، **تمام** ارتباطات از پروکسی رد می‌شوند.

| بخش | توضیح | با پروکسی؟ |
|-----|-------|------------|
| `SendCodeExt()` | ارسال شماره تلفن | ✅ بله |
| `SignInExt()` | ارسال کد تایید | ✅ بله |
| `SendCode()` + 2FA | ارسال رمز دو مرحله‌ای | ✅ بله |
| `GetDCs()` | دریافت URL سرورهای روبیکا | ✅ بله |
| `SendMessage()` | ارسال پیام | ✅ بله |
| `DownloadFile()` | دانلود فایل | ✅ بله |
| `Upload()` | آپلود فایل | ✅ بله |
| `Run()` | اتصال WebSocket | ✅ بله |
| تمام API calls | هر درخواست به روبیکا | ✅ بله |

---

## ❓ سوالات متداول

### ۱. آیا باید قبل از هر درخواست `EnableProxy()` بزنم؟

> **نه!** یک بار کافی است. اما اگر هر بار هم بزنید مشکلی ایجاد نمی‌شود.

```go
client.EnableProxy()  // ← فقط یک بار

client.SendCodeExt(...)   // با پروکسی ✅
client.SignInExt(...)     // با پروکسی ✅
client.SendMessage(...)   // با پروکسی ✅
```

---

### ۲. اگر آدرس پروکسی اشتباه باشد چه می‌شود؟

> درخواست‌ها **خطا** می‌دهند. یک پیام هشدار در لاگ چاپ می‌شود:

```
[rubingo] Warning: invalid proxy URL: ...
```

---

### ۳. آیا می‌توانم بدون پروکسی شروع کنم و بعداً فعال کنم؟

> **بله!** دقیقاً:

```go
client, _ := rubingo.NewClient("my_bot")  // بدون پروکسی
client.Connect()

// ... بعداً ...
client.SetProxy("http://user:pass@host:port")
client.EnableProxy()
```

---

### ۴. آیا `SetProxy()` پروکسی را فعال هم می‌کند؟

> **نه!** فقط آدرس را تغییر می‌دهد. باید جداگانه فعال کنید:

```go
// ❌ فقط آدرس عوض شد، ولی هنوز غیرفعال است
client.SetProxy("http://user:pass@host:port")

// ✅ حالا باید فعال هم کنید
client.EnableProxy()

// یا هر دو با هم:
client.SetProxy("http://user:pass@host:port")
client.EnableProxy()
```

---

### ۵. آیا پروکسی SOCKS5 پشتیبانی می‌شود؟

> **بله!** فقط آدرس را با `socks5://` شروع کنید:

```go
rubingo.WithProxy("socks5://user:pass@host:1080")
```

---

### ۶. وقتی `DisableProxy()` می‌زنم، آدرس پروکسی پاک می‌شود؟

> **نه!** فقط غیرفعال می‌شود. آدرس ذخیره می‌ماند و هر وقت
> `EnableProxy()` بزنید دوباره با همان آدرس فعال می‌شود.

```go
client.SetProxy("http://user:pass@host:port")
client.EnableProxy()   // فعال ✅

client.DisableProxy()  // غیرفعال ❌ (آدرس هنوز هست)

client.EnableProxy()   // دوباره فعال ✅ (با همان آدرس قبلی)
```

---

## 🔧 عیب‌یابی

### مشکل: `connection refused` یا `timeout`

> **💡 راه‌حل:**
> - آدرس پروکسی را بررسی کنید
> - مطمئن شوید سرور پروکسی در دسترس است
> - پورت را بررسی کنید

```go
// تست اتصال به پروکسی
client.EnableProxy()
fmt.Println("Proxy enabled:", client.IsProxyEnabled())

_, err := client.SendCodeExt("989123456789", "")
if err != nil {
    fmt.Println("❌ Error:", err)
    
    // غیرفعال کردن پروکسی و تلاش مجدد
    client.DisableProxy()
    _, err = client.SendCodeExt("989123456789", "")
    if err != nil {
        fmt.Println("❌ Without proxy also failed:", err)
    } else {
        fmt.Println("✅ Works without proxy")
    }
}
```

---

### مشکل: `407 Proxy Authentication Required`

> **💡 راه‌حل:**
> نام کاربری یا رمز عبور پروکسی اشتباه است.

```go
// بررسی کنید:
// ❌ اشتباه
"http://wronguser:wrongpass@host:port"

// ✅ درست - با مقادیر صحیح
"http://correctuser:correctpass@host:port"
```

---

### مشکل: کاراکترهای خاص در رمز عبور

> **💡 راه‌حل:**
> کاراکترهای خاص را URL encode کنید:

```go
import "net/url"

// روش خودکار:
proxyURL := &url.URL{
    Scheme: "http",
    User:   url.UserPassword("username", "p@ss=word#123"),
    Host:   "proxy.com:8080",
}

client.SetProxy(proxyURL.String())
// نتیجه: http://username:p%40ss%3Dword%23123@proxy.com:8080
```

---

## 📐 دیاگرام عملکرد

```
┌─────────────────────────────────────────────────────────────┐
│                        شروع برنامه                          │
│                                                             │
│  client, _ := rubingo.NewClient("bot",                      │
│      rubingo.WithProxy("http://..."),                       │
│  )                                                          │
│  client.Connect()                                           │
│                                                             │
│  ┌─────────────────────────┐    ┌─────────────────────────┐ │
│  │   client.EnableProxy()  │    │  client.DisableProxy()  │ │
│  │   پروکسی: فعال 🟢       │    │  پروکسی: غیرفعال 🔴     │ │
│  │                         │    │                         │ │
│  │  شما ──► پروکسی ──► رو  │    │  شما ──────────────► رو │ │
│  │          بیکا           │    │         بیکا            │ │
│  └─────────────────────────┘    └─────────────────────────┘ │
│                                                             │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │              client.IsProxyEnabled()                    │ │
│  │              → true / false                             │ │
│  └─────────────────────────────────────────────────────────┘ │
│                                                             │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │  client.SetProxy("http://new-proxy:port")               │ │
│  │  → فقط آدرس عوض می‌شود                                  │ │
│  │  → وضعیت فعال/غیرفعال تغییر نمی‌کند                      │ │
│  └─────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

---

## 📄 License

MIT License

---

> **📌 خلاصه نهایی:**
> 
> 1. آدرس پروکسی بدهید: `WithProxy("http://...")` یا `SetProxy("http://...")`
> 2. فعال کنید: `WithProxyEnabled(true)` یا `EnableProxy()`
> 3. تمام! همه درخواست‌ها از پروکسی رد می‌شوند
> 4. هر وقت خواستید غیرفعال کنید: `DisableProxy()`
> 5. پیش‌فرض: **غیرفعال** 🔴
```