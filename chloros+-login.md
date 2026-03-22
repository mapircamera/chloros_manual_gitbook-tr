# Chloros+ Giriş

## Chloros ve Chloros (Tarayıcı) Giriş

Kullanıcı <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> kenar çubuğu menüsü, Chloros+ hesabınıza giriş yapmanızı ve ek özelliklerin kilidini açmanızı sağlar.

Giriş yaptığınızda hesap bilgileriniz gösterilecektir:

<figure><img src=".gitbook/assets/user_account.JPG" alt="" width="375"><figcaption></figcaption></figure>## CLI Giriş

CLI işlemeyi etkinleştirmek için Chloros+ kimlik bilgilerinizi kullanarak giriş yapın. Linux&#x27;te (GUI yok), lisansınızı etkinleştirmenin tek yolu budur.

**Sözdizimi:**

```bash
chloros-cli login <email> <password>
```

{% hint style="info" %}
**SDK Kullanıcıları**: Python SDK ayrıca önbelleğe alınmış kimlik bilgilerini temizlemek için programlı bir `logout()` yöntemi de sunar. Ayrıntılar için [Python SDK belgelerine](api-python-sdk.md#logout) bakın.
{% endhint %}

**Örnek:**

```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% hint style="warning" %}
**Özel Karakterler**: `$`, `!` gibi karakterler veya boşluklar içeren şifrelerin etrafına tek tırnak işareti koyun.
{% endhint %}

**Çıktı:**

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>### Kimlik Bilgileri Depolama

Önbelleğe alınmış kimlik bilgileri, platforma özgü bir konumda depolanır:

| Platform | Kimlik Bilgileri Önbellek Yolu |
| --- | --- |
| **Windows** | `%APPDATA%\Chloros\cache\` |
| **Linux** | `~/.cache/chloros/` |

### Planın Süresinin Dolması

GUI&#x27;deki planın süresinin dolması, lisansınızın ne zaman geçersiz hale geleceğini gösterir. Tekrarlayan aylık abonelikler için süre, ayın sonunda dolmaktadır. Yıllık abonelikler için ise, aboneliği başlattığınız tarihten bir yıl sonra dolmaktadır. Lisans kontrolü, doğrulama için aylık internet bağlantısı gerektirir ve 30 günlük bir ödemesiz süre tanınır.

### Cihaz Sınırı

Her Chloros+ planı, farklı sayıda kayıtlı cihaz sunar. Chloros+ hesabınızla oturum açtığınız her cihaz, kayıtlı cihaz sayınıza dahil edilir. MAPIR Cloud hesap sayfanızdan bir cihazın adını değiştirebilir ve cihazı kaldırabilirsiniz.

<table><thead><tr><th width="168.5999755859375" align="right">Chloros+ Planı</th><th align="center">COPPER</th><th align="center">BRONZE</th><th align="center">GÜMÜŞ</th><th align="center">ALTIN</th></tr></thead><tbody><tr><td align="right">Desteklenen Cihazlar</td><td align="center">2</td><td align="center">2</td><td align="center">5</td><td align="center">10</td></tr></tbody></table>
