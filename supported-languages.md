# Atbalstītās valodas

Chloros nodrošina pilnīgu saskarnes atbalstu **38 valodās visā pasaulē**, tādējādi padarot to pieejamu lietotājiem visā pasaulē. Valodu var ātri mainīt gan darbvirsmas lietotāja saskarnē, gan CLI.

Chloros atbalsta šādas valodas:

| # | Valoda | Nativais nosaukums | CLI kods |
|---|----------|-------------|----------|
| 1 | 🇺🇸 Angļu | English | `en` |
| 2 | 🇪🇸 Spāņu | Español | `es` |
| 3 | 🇵🇹 Portugāļu | Português | `pt` |
| 4 | 🇫🇷 Franču | Français | `fr` |
| 5 | 🇩🇪 Vācu | Deutsch | `de` |
| 6 | 🇮🇹 Itāļu | Italiano | `it` |
| 7 | 🇯🇵 Japāņu | 日本語 | `ja` |
| 8 | 🇰🇷 Korejiešu | 한국어 | `ko` |
| 9 | 🇨🇳 Ķīniešu (vienkāršotā) | 简体中文 | `zh` |
| 10 | 🇹🇼 Ķīniešu (tradicionālā) | 繁體中文 | `zh-TW` |
| 11 | 🇷🇺 Krievu | Русский | `ru` |
| 12 | 🇳🇱 Holandiešu | Nederlands | `nl` |
| 13 | 🇸🇦 Arābu | العربية | `ar` |
| 14 | 🇵🇱 poļu | Polski | `pl` |
| 15 | 🇹🇷 turku | Türkçe | `tr` |
| 16 | 🇮🇳 hindi | हिंदी | `hi` |
| 17 | 🇮🇩 Indonēziešu | Bahasa Indonesia | `id` |
| 18 | 🇻🇳 Vjetnamiešu | Tiếng Việt | `vi` |
| 19 | 🇹🇭 Taizemes valoda | ไทย | `th` |
| 20 | 🇸🇪 Zviedru valoda | Svenska | `sv` |
| 21 | 🇩🇰 Dāņu | Dansk | `da` |
| 22 | 🇳🇴 Norvēģu | Norsk | `no` |
| 23 | 🇫🇮 Somu | Suomi | `fi` |
| 24 | 🇬🇷 Grieķu | Ελληνικά | `el` |
| 25 | 🇨🇿 Čehu | Čeština | `cs` |
| 26 | 🇭🇺 Ungāru | Magyar | `hu` |
| 27 | 🇷🇴 Rumāņu | Română | `ro` |
| 28 | 🇺🇦 Ukraiņu | Українська | `uk` |
| 29 | 🇧🇷 Brazīlijas portugāļu | Português Brasileiro | `pt-BR` |
| 30 | 🇭🇰 Kantoniešu | 粵語 | `zh-HK` |
| 31 | 🇲🇾 Malajiešu | Bahasa Melayu | `ms` |
| 32 | 🇸🇰 Slovāņu | Slovenčina | `sk` |
| 33 | 🇧🇬 Bulgāru valoda | Български | `bg` |
| 34 | 🇭🇷 Horvātu valoda | Hrvatski | `hr` |
| 35 | 🇱🇹 Lietuviešu | Lietuvių | `lt` |
| 36 | 🇱🇻 Latviešu | Latviešu | `lv` |
| 37 | 🇪🇪 Igaunijas | Eesti | `et` |
| 38 | 🇸🇮 Slovēņu | Slovenščina | `sl` |

## Kā mainīt valodu

### Chloros darbvirsmā

1. Atveriet lietojumprogrammas iestatījumus
2. Pārejiet uz valodas izvēles izvēlni
3. No saraksta izvēlieties vēlamo valodu
4. Lietojumprogrammas saskarne tiks atjaunināta uzreiz

### Chloros un CLI

Lai apskatītu vai mainītu CLI saskarnes valodu, izmantojiet komandu `language`:

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

Sīkāku informāciju skatiet [CLI dokumentācijā](CLI.md).

## Atbalstītās valodas

Visas 38 valodas tiek pilnībā atbalstītas:

* **Chloros Desktop** — pilnīgs grafiskās lietotāja saskarnes tulkojums
* **Chloros CLI** — komandrindas saskarne un izvades ziņojumi

Python SDK API un tā [atsauces dokumentācija](reference/sdk-reference.md) ir pieejama angļu valodā.

Valodu atbalsts nodrošina, ka lietotāji visā pasaulē var efektīvi strādāt savā dzimtajā valodā bez šķēršļiem.
