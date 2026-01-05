# Chloros+ Giriş

## Chloros ve Chloros (Tarayıcı) Giriş

Kullanıcı <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> kenar çubuğu menüsü, Chloros+ hesabınıza giriş yapmanızı ve ek özelliklerin kilidini açmanızı sağlar.

Giriş yaptığınızda hesap bilgileriniz gösterilecektir:

<figure><img src=".gitbook/assets/user_account.JPG" alt="" data-size="line"><figcaption></figcaption></figure>## CLI Giriş

CLI işlemini etkinleştirmek için Chloros+ kimlik bilgilerinizle giriş yapın.

**Sözdizimi:**

```bash
chloros-cli login <email> <password>
```

{% hint style=&quot;info&quot; %}
**SDK Kullanıcıları**: Python SDK ayrıca önbelleğe alınmış kimlik bilgilerini temizlemek için programlı bir `logout()` yöntemi sağlar. Ayrıntılar için [Python SDK belgelerine](api-python-sdk.md#logout) bakın.
{% endhint %}

**Örnek:**

```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% hint style=&quot;warning&quot; %}
**Özel Karakterler**: `$`, `!` gibi karakterler veya boşluklar içeren şifrelerin etrafına tek tırnak işareti kullanın.
{% endhint %}

**Çıktı:**

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>### Planın Sona Erme Tarihi

GUI&#x27;deki planın sona erme tarihi, lisansınızın ne zaman geçersiz hale geleceğini gösterir. Aylık aboneliklerde sona erme tarihi ayın sonudur. Yıllık aboneliklerde ise aboneliğinizi başlattığınız tarihten bir yıl sonradır. Lisans kontrolü, 30 günlük bir ödemesiz dönem ile aylık internet bağlantısı gerektirir.

### Cihaz Sınırı

Her Chloros+ planı, farklı sayıda kayıtlı cihaz sunar. Chloros+ hesabıyla oturum açtığınız her cihaz, kayıtlı cihaz sayınıza eklenir. MAPIR Cloud hesap sayfanızdan bir cihazın adını değiştirebilir ve kaldırabilirsiniz.

<table><thead><tr><th width="168.5999755859375" align="right">Chloros+ Planı</th><th align="center">BAKIR</th><th align="center">BRONZ</th><th align="center">GÜMÜŞ</th><th align="center">ALTIN</th></tr></thead><tbody><tr><td align="right">Desteklenen Cihazlar</td><td align="center">2</td><td align="center">2</td><td align="center">5</td><td align="center">10</td></tr></tbody></table>
