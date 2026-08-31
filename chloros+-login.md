# Chloros+ Giriş

## GUI Girişi

<img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line">&#x27;in kenar çubuğu menüsü, Chloros+ hesabınıza giriş yapmanıza ve ek özelliklerin kilidini açmanıza olanak tanır.

**Her makine için yalnızca bir kez oturum açmanız yeterlidir.** GUI, CLI ve Python ile SDK aynı önbelleğe alınmış oturumu paylaşır — masaüstü GUI üzerinden oturum açmak, o cihazdaki CLI ve SDK&#x27;i de etkinleştirir (ve bunun tersi de `chloros-cli login` üzerinden geçerlidir).

Oturum açıldığında hesap bilgileriniz görüntülenecektir:

<figure><img src=".gitbook/assets/user_account.JPG" alt="" width="375"><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: re-shoot the logged-in user account panel in Chloros 1.2.0 — plan name display and the registered-device list UI may have changed; must show plan name, expiration, and device list. -->
## Plan Seviyeleri

| Plan | `plan_id` | Tür |
| --- | --- | --- |
| Iron | `0` | Ücretsiz |
| Copper | `1` | Ücretli (Chloros+) |
| Bronze | `2` | Ücretli (Chloros+) |
| Gümüş | `3` | Ücretli (Chloros+) |
| Altın | `4` | Ücretli (Chloros+) |

Her bir ücretli kademenin neleri içerdiğini öğrenmek için [paketler ve fiyatlar](https://cloud.mapir.camera/pricing) sayfasına bakın.

### CLI / SDK erişimi için ücretli bir seviye gereklidir

CLI ve Python SDK erişimi için **herhangi bir ücretli Chloros+ seviyesi (Copper veya üzeri)**gereklidir. Bu kural**sunucu tarafında** uygulanır — her CLI/SDK isteği, hem aktif bir oturum hem de ücretli bir plan içermelidir:

| HTTP durumu | `error_code` | Anlam | Çözüm |
| --- | --- | --- | --- |
| `401` | `AUTH_REQUIRED` | Bu makinede oturum açılmamış | `chloros-cli login <email> <password>` |
| `403` | `PLAN_UPGRADE_REQUIRED` | Oturum açıldı, ancak plan seviyesi çok düşük (ücretsiz Iron seviyesi) | Herhangi bir ücretli Chloros+ planına yükseltin |

`chloros-cli status`, ücretsiz kademede erişilebilir durumda kalır; böylece mevcut planınızı ve erişimin neden reddedildiğini her zaman görebilirsiniz.

### Plan başına bağlı donanım sınırları

Her plan, aynı anda canlı olarak bağlanabilecek LATTICE kamera ve DAQ ışık sensörlerinin sayısını sınırlar:

| Plan | LATTICE kameralar | DAQ ışık sensörleri |
| --- | --- | --- |
| Iron (ücretsiz / oturum açılmamış) | 4 | 2 |
| Copper / Bronze | 6 | 3 |
| Silver | 10 | 6 |
| Gold | 20 | 12 |

## CLI Oturum Açma

CLI işlemeyi etkinleştirmek için Chloros+ kimlik bilgilerinizi kullanarak oturum açın. Linux&#x27;te (GUI yok), lisansınızı etkinleştirmenin tek yolu budur.

**Sözdizimi:**

```bash
chloros-cli login <email> <password>
```

{% hint style="info" %}
**SDK Kullanıcıları**: Python SDK ayrıca, önbelleğe alınmış kimlik bilgilerini temizlemek için programlı bir `logout()` yöntemi sunar. Ayrıntılar için [SDK Referansı](reference/sdk-reference.md) bölümüne bakın.
{% endhint %}

**Örnek:**

```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% hint style="warning" %}
**Özel Karakterler**: `$`, `!` gibi karakterler veya boşluklar içeren şifrelerin etrafına tek tırnak işareti koyun.
{% endhint %}

**Çıktı:**

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: re-shoot the CLI login output — the banner now prints "Chloros CLI 1.2.0"; capture a successful login with the current output format. -->
### Kimlik Bilgilerinin Depolanması

Önbelleğe alınmış kimlik bilgileri ve yapılandırma, **tüm platformlarda** kullanıcı ana dizininizdeki `.chloros` klasöründe saklanır:

| Platform | Kimlik Bilgileri Önbellek Yolu |
| --- | --- |
| **Windows** | `%USERPROFILE%\.chloros\` |
| **Linux** | `~/.chloros/` |

### Planın Süresinin Dolması ve Çevrimdışı Geçiş Süresi

GUI&#x27;de gösterilen planın süresinin dolması, lisansınızın ne zaman geçersiz hale geleceğini gösterir. Aylık tekrarlayan aboneliklerde süre, ayın sonunda dolar; yıllık aboneliklerde ise aboneliği başlattığınız tarihten bir yıl sonra sona erer.

Chloros, lisansınızı çevrimiçi olarak doğrular; ancak bir geçiş süresi boyunca çevrimdışı çalışma da desteklenir:

* Başarılı sunucu doğrulamaları **5 dakika** süreyle önbelleğe alınır; bu nedenle normal kullanımda lisans sorguları çok az sayıda gerçekleşir.
* İmzalanmış, cihaza bağlı lisans önbelleği daha uzun çevrimdışı süreleri kapsar: **aylık planlar için 30 gün**ve**yıllık planlar için aboneliğinizin son kullanma tarihine kadar (en fazla 365 gün)**.
* Geçici süre sona erdiğinde, cihaz lisans sunucusuna bir kez erişene kadar plan ücretsiz Iron kademesine geçer; bir sonraki başarılı doğrulama işleminde erişim yeniden başlar.

### Cihaz Sınırı

Her Chloros+ planı, farklı sayıda kayıtlı cihaz sunar. Chloros+ hesabıyla oturum açtığınız her cihaz, kayıtlı cihaz sayınıza dahil edilir. MAPIR Cloud hesap sayfanızdan bir cihazın adını değiştirebilir ve cihazı kaldırabilirsiniz.

<table><thead><tr><th width="168.5999755859375" align="right">Chloros+ Planı</th><th align="center">COPPER</th><th align="center">BRONZE</th><th align="center">GÜMÜŞ</th><th align="center">ALTIN</th></tr></thead><tbody><tr><td align="right">Desteklenen Cihazlar</td><td align="center">2</td><td align="center">2</td><td align="center">5</td><td align="center">10</td></tr></tbody></table>Hesabınızın tam cihaz kotası, MAPIR Cloud hesap sayfanızda gösterilir. Bir cihazdan çıkış yapmak, o cihazın yuvasını güvenilir bir şekilde boşaltır ve önceden kayıtlı bir cihaz, hesap cihaz sınırına ulaşmış olsa bile her zaman tekrar giriş yapabilir.
