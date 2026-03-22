# Desteklenen Diller

Chloros, **dünya çapında 38 dilde** tam arayüz desteği sunarak, dünyanın her yerindeki kullanıcıların erişimine açık hale getirir. Tüm arayüzlerde (Masaüstü, Tarayıcı, CLI ve Python SDK) dilleri anında değiştirebilirsiniz.

Chloros aşağıdaki dilleri destekler:

| # | Dil | Yerel Adı | CLI Kodu |
|---|----------|-------------|----------|
| 1 | 🇺🇸 İngilizce | English | `en` |
| 2 | 🇪🇸 İspanyolca | Español | `es` |
| 3 | 🇵🇹 Portekizce | Português | `pt` |
| 4 | 🇫🇷 Fransızca | Français | `fr` |
| 5 | 🇩🇪 Almanca | Deutsch | `de` |
| 6 | 🇮🇹 İtalyanca | Italiano | `it` |
| 7 | 🇯🇵 Japonca | 日本語 | `ja` |
| 8 | 🇰🇷 Korece | 한국어 | `ko` |
| 9 | 🇨🇳 Çince (Basitleştirilmiş) | 简体中文 | `zh` |
| 10 | 🇹🇼 Çince (Geleneksel) | 繁體中文 | `zh-TW` |
| 11 | 🇷🇺 Rusça | Русский | `ru` |
| 12 | 🇳🇱 Felemenkçe | Nederlands | `nl` |
| 13 | 🇸🇦 Arapça | العربية | `ar` |
| 14 | 🇵🇱 Lehçe | Polski | `pl` |
| 15 | 🇹🇷 Türkçe | Türkçe | `tr` |
| 16 | 🇮🇳 Hintçe | हिंदी | `hi` |
| 17 | 🇮🇩 Endonezyaca | Bahasa Indonesia | `id` |
| 18 | 🇻🇳 Vietnamca | Tiếng Việt | `vi` |
| 19 | 🇹🇭 Tayca | ไทย | `th` |
| 20 | 🇸🇪 İsveççe | Svenska | `sv` |
| 21 | 🇩🇰 Danca | Dansk | `da` |
| 22 | 🇳🇴 Norveççe | Norsk | `no` |
| 23 | 🇫🇮 Fince | Suomi | `fi` |
| 24 | 🇬🇷 Yunanca | Ελληνικά | `el` |
| 25 | 🇨🇿 Çekçe | Čeština | `cs` |
| 26 | 🇭🇺 Macarca | Magyar | `hu` |
| 27 | 🇷🇴 Romence | Română | `ro` |
| 28 | 🇺🇦 Ukraynaca | Українська | `uk` |
| 29 | 🇧🇷 Brezilya Portekizcesi | Português Brasileiro | `pt-BR` |
| 30 | 🇭🇰 Kantonca | 粵語 | `zh-HK` |
| 31 | 🇲🇾 Malayca | Bahasa Melayu | `ms` |
| 32 | 🇸🇰 Slovakça | Slovenčina | `sk` |
| 33 | 🇧🇬 Bulgarca | Български | `bg` |
| 34 | 🇭🇷 Hırvatça | Hrvatski | `hr` |
| 35 | 🇱🇹 Litvanyaca | Lietuvių | `lt` |
| 36 | 🇱🇻 Letonca | Latviešu | `lv` |
| 37 | 🇪🇪 Estonca | Eesti | `et` |
| 38 | 🇸🇮 Slovence | Slovenščina | `sl` |

## Dil Nasıl Değiştirilir

### Chloros Masaüstü/Tarayıcı&#x27;da

1. Uygulama ayarlarını açın
2. Dil seçimi menüsüne gidin
3. Listeden tercih ettiğiniz dili seçin
4. Arayüz anında güncellenecektir

### Chloros CLI

`language` komutunu kullanarak CLI arayüz dilini görüntüleyin veya değiştirin:

```bash
# View current language
chloros-cli language

# Change to Spanish
chloros-cli language es

# Change to Chinese (Simplified)
chloros-cli language zh

# Change to Brazilian Portuguese
chloros-cli language pt-BR

# List all available languages
chloros-cli language --list
```

Daha fazla ayrıntı için [CLI belgelerine](CLI.md) bakın.

### Chloros Python SDK

Mesajları ve çıktıları tercih ettiğiniz dilde almak için SDK&#x27;i başlatırken dil parametresini ayarlayın.

## Kapsam

38 dilin tamamı aşağıdakilerde tam olarak desteklenmektedir:

* **Chloros Masaüstü** - Tam GUI çevirisi
* **Chloros Tarayıcı** - Tüm dillerde web arayüzü
* **Chloros CLI** - Komut satırı arayüzü ve çıktı mesajları
* **Chloros Python SDK** - API mesajları ve belgeler

Dil desteği, dünya çapındaki kullanıcıların engel olmadan kendi ana dillerinde verimli bir şekilde çalışabilmelerini sağlar.
