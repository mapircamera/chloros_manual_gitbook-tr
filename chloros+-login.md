# Chloros+ Oturum Açma

## GUI Oturum Açma

Kullanıcının <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> kenar çubuğu menüsü, Chloros+ hesabınıza oturum açmanıza ve ek özelliklerin kilidini açmanıza olanak tanır.

**Her makine için yalnızca bir kez oturum açmanız yeterlidir.** GUI, CLI ve Python SDK aynı önbelleğe alınmış oturumu paylaşır — masaüstü GUI üzerinden oturum açmak, o cihazdaki CLI ve SDK&#x27;yi de etkinleştirir (ve `chloros-cli login` aracılığıyla bunun tersi de geçerlidir).

Oturum açtığınızda hesap bilgileriniz gösterilecektir:

<figure><img src=".gitbook/assets/user_account.JPG" alt="" width="375"><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: re-shoot the logged-in user account panel in Chloros 1.3.0 — plan name display and the registered-device list UI may have changed; must show plan name, expiration, and device list. -->

## Plan Seviyeleri

| Plan | `plan_id` | Tür |
| --- | --- | --- |
| Iron | `0` | Ücretsiz |
| Copper | `1` | Ücretli (Chloros

+) |
| Bronze | `2` | Ücretli (Chloros

+) |
| Silver | `3` | Ücretli (Chloros

+) |
| Altın | `4` | Ücretli (Chloros

+) |

Her bir ücretli kademenin neleri içerdiğini öğrenmek için [planlar ve fiyatlandırma](https://cloud.mapir.camera/pricing) sayfasına bakın.

### CLI

/ SDK

erişimi için ücretli bir kademe gereklidir

CLI

ve Python

SDK

erişimi için **herhangi bir ücretli Chloros

+ kademesi (Copper veya üstü)**gereklidir. Bu kural**sunucu tarafında** uygulanır — her CLI

/SDK

isteği hem aktif bir oturum hem de ücretli bir plan içermelidir:

| HTTP

durumu | `error_code` | Anlamı | Çözüm |
| --- | --- | --- | --- |
| `401` | `AUTH_REQUIRED` | Bu makinede oturum açılmamış | `chloros-cli login <email> <password>` |
| `403` | `PLAN_UPGRADE_REQUIRED` | Oturum açılmış, ancak plan seviyesi çok düşük (ücretsiz Iron seviyesi) | Herhangi bir ücretli Chloros

+ planına yükseltin |

`chloros-cli status`, ücretsiz seviyede erişilebilir durumda kalır; böylece mevcut planınızı ve erişimin neden reddedildiğini her zaman görebilirsiniz.

### Plan başına bağlı donanım sınırları

Her plan, aynı anda canlı olarak bağlanabilecek LATTICE kamera ve DAQ ışık sensörlerinin sayısını sınırlar:

| Plan | LATTICE kameralar | DAQ ışık sensörleri |
| --- | --- | --- |
| Iron (ücretsiz / oturum açılmamış) | 4 | 2 |
| Copper / Bronze | 6 | 3 |
| Silver | 10 | 6 |
| Gold | 20 | 12 |

## CLI

Oturum Açma

CLI

işlemeyi etkinleştirmek için Chloros

+ kimlik bilgilerinizle oturum açın. Linux

&#x27;ta (GUI yok), lisansınızı etkinleştirmenin tek yolu budur.

**Sözdizimi:**

```bash
chloros-cli login <email> <password>
```

{% hint style="info" %}
**SDK

Kullanıcıları**: Python

SDK

&#x27;si, önbelleğe alınmış kimlik bilgilerini temizlemek için programlı bir `logout()` yöntemi de sunar. Ayrıntılar için [SDK

Referansı](reference/sdk-reference.md) bölümüne bakın.
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
<!-- SCREENSHOT-UPDATE: re-shoot the CLI login output — the banner now prints "Chloros CLI 1.3.0"; capture a successful login with the current output format. -->

### Kimlik Bilgilerinin Depolanması

Önbelleğe alınmış kimlik bilgileri ve yapılandırma, **tüm platformlarda** kullanıcı ana dizininizdeki `.chloros` klasöründe saklanır:

| Platform | Kimlik Bilgileri Önbellek Yolu |
| --- | --- |
| **Windows** | `%USERPROFILE%\.chloros\` |
| **Linux** | `~/.chloros/` |

Mevcut sürümün üzerine daha yeni bir sürüm yüklediğinizde, Windows

&#x27;ta (yükleyici) ve Linux

&#x27;ta (`.deb`, `chloros-cli update` dahil) oturum açma bilgileriniz korunur. Chloros

&#x27;u kaldırmak — veya Linux

paketini silmek — önbellekteki oturum açma bilgilerini siler; bu nedenle, yeni bir kurulumdan sonra tekrar oturum açmanız gerekir.

### Planın Süresinin Dolması ve Çevrimdışı Geçiş Süresi

GUI&#x27;de gösterilen planın süresi dolumu, lisansınızın ne zaman geçersiz hale geleceğini gösterir. Aylık tekrarlayan aboneliklerde son kullanma tarihi ayın sonudur; yıllık aboneliklerde ise aboneliği başlattığınız tarihten bir yıl sonradır.

Chloros

, lisansınızı çevrimiçi olarak doğrular, ancak çevrimdışı çalışma bir ek süre içinde desteklenir:

* Başarılı sunucu doğrulamaları **5 dakika** süreyle önbelleğe alınır, bu nedenle normal kullanımda çok az sayıda lisans çağrısı yapılır.
* İmzalanmış, makineye bağlı lisans önbelleği, daha uzun çevrimdışı süreleri kapsar: **aylık planlar için 30 gün**ve**yıllık planlar için aboneliğinizin son kullanma tarihine kadar (en fazla 365 gün)**.
* Geçiş süresi sona erdiğinde, makine lisans sunucusuna bir kez erişene kadar plan ücretsiz Iron kademesine geçer; erişim, bir sonraki başarılı kontrolde yeniden başlar.

### Cihaz Sınırı

Her Chloros

+ planı, farklı sayıda kayıtlı cihaz sunar. Chloros

+ hesabınızla oturum açtığınız her cihaz, kayıtlı cihaz sayınıza dahil edilir. MAPIR

Cloud hesap sayfanızdan bir cihazın adını değiştirebilir ve cihazı kaldırabilirsiniz.

<table><thead><tr><th width="168.5999755859375" align="right">Chloros Planı+</th><th align="center">COPPER</th><th align="center">BRONZE</th><th align="center">SILVER</th><th align="center">GOLD</th></tr></thead><tbody><tr><td align="right">Desteklenen Cihazlar</td><td align="center">2</td><td align="center">2</td><td align="center">5</td><td align="center">10</td></tr></tbody></table>

Hesabınızın tam cihaz kotası, MAPIR Cloud hesap sayfanızda gösterilir. Bir cihazdan çıkış yapmak, o cihazın yerini güvenilir bir şekilde boşaltır ve zaten kayıtlı olan bir cihaz, hesap cihaz sınırına ulaşmış olsa bile her zaman tekrar giriş yapabilir.
