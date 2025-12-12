# Kloros+ Giriş

## Chloros ve Chloros (Tarayıcı) Giriş

Kullanıcı <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> kenar çubuğu menüsü, Chloros+ hesabınızda oturum açmanıza ve ek özelliklerin kilidini açmanıza olanak tanır.

Giriş yaptığınızda hesap bilgileriniz gösterilecektir:

<figure><img src=".gitbook/assets/user_account.JPG" alt="" width="375"><figcaption></figcaption></figure>

## CLI Girişi

CLI işlemeyi etkinleştirmek için Chloros+ kimlik bilgilerinizle oturum açın.

**Sözdizimi:**

```bash
chloros-cli login <email> <password>
```

**Örnek:**

```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% ipucu stili = "uyarı" %}
**Özel Karakterler**: `$`, `!` gibi karakterler veya boşluk içeren şifrelerin etrafında tek tırnak kullanın.
{%son ipucu %}

**Çıktı:**

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>

### Planın Sona Ermesi

GUI'deki planın sona ermesi, lisansınızın ne zaman geçersiz hale geleceğini gösterir. Yinelenen aylık abonelikler için son kullanma tarihi ayın sonundadır. Yıllık abonelikler için bu süre, aboneliği başlattığınız tarihten sonraki bir yıldır. Lisans kontrolü, doğrulama için 30 günlük ödemesiz dönemle birlikte aylık bir internet bağlantısı gerektirir.

### Cihaz Sınırı

Her Chloros+ planı farklı sayıda kayıtlı cihaz sunar. Chloros+ hesabıyla giriş yaptığınız her cihaz, kayıtlı cihaz sayınıza dahil edilecektir. MAPIR Cloud hesap sayfanızda bir cihazı yeniden adlandırabilir ve kaldırabilirsiniz.

<table><thead><tr><th width="168.5999755859375" align="right">Chloros+ Plan</th><th align="center">COPPER</th><th align="center">BRONZE</th><th align="center">SILVER</th><th align="center">GOLD</th></tr></thead><tbody><tr><td align="right">Cihazlar Destekleniyor</td><td align="center">2</td><td align="center">2</td><td align="center">5</td><td align="center">10</td></tr></tbody></table>
