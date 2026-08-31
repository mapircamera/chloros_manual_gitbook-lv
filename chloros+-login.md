# Chloros+ Pieslēgšanās

## Pieslēgšanās caur grafisko lietotāja saskarni

Lietotāja <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> sānu joslas izvēlnē varat pieslēgties savam Chloros+ kontam un atbloķēt papildu funkcijas.

**Katram datoram ir nepieciešams pieteikties tikai vienu reizi.** Grafiskajai lietotāja saskarnei, CLI un Python SDK ir kopīga sesijas kešatmiņa — pieteikšanās caur darbvirsmas GUI aktivizē arī CLI un SDK tajā datorā (un otrādi, izmantojot `chloros-cli login`).

Pēc pieteikšanās tiks parādīta jūsu konta informācija:

<figure><img src=".gitbook/assets/user_account.JPG" alt="" width="375"><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: re-shoot the logged-in user account panel in Chloros 1.2.0 — plan name display and the registered-device list UI may have changed; must show plan name, expiration, and device list. -->
## Pakalpojuma līmeņi

| Pakalpojums | `plan_id` | Tips |
| --- | --- | --- |
| Iron | `0` | Bezmaksas |
| Copper | `1` | Maksas (Chloros+) |
| Bronze | `2` | Maksas (Chloros+) |
| Sudrabs | `3` | Maksas (Chloros+) |
| Zelta | `4` | Maksas (Chloros+) |

Skatīt [plānus un cenas](https://cloud.mapir.camera/pricing), lai uzzinātu, ko ietver katrs maksas līmenis.

### Lai piekļūtu CLI / SDK, ir nepieciešams maksas līmenis

Lai piekļūtu CLI un Python, kā arī SDK, ir nepieciešams **jebkurš maksas Chloros+ līmenis (Copper vai augstāks)**. Tas tiek piemērots**servera pusē** — katram CLI/SDK pieprasījumam ir jābūt gan aktīvai sesijai, gan maksas plānam:

| HTTP statuss | `error_code` | Nozīme | Risinājums |
| --- | --- | --- | --- |
| `401` | `AUTH_REQUIRED` | Nav pieteicies šajā ierīcē | `chloros-cli login <email> <password>` |
| `403` | `PLAN_UPGRADE_REQUIRED` | Esat pieteicies, bet plāna līmenis ir pārāk zems (bezmaksas „Iron” līmenis) | Pārejiet uz jebkuru maksas Chloros+ plānu |

`chloros-cli status` joprojām ir pieejams bezmaksas līmenī, tādēļ jūs vienmēr varat redzēt savu pašreizējo plānu un iemeslu, kāpēc piekļuve ir atteikta.

### Pievienotās aparatūras ierobežojumi katram plānam

Katram plānam ir noteikts maksimālais skaits, cik daudz LATTICE kameru un DAQ gaismas sensoru vienlaikus var pievienot tiešsaistē:

| Plāns | LATTICE kameras | DAQ gaismas sensori |
| --- | --- | --- |
| Iron (bezmaksas / nav pieteicies) | 4 | 2 |
| Copper / Bronze | 6 | 3 |
| Silver | 10 | 6 |
| Gold | 20 | 12 |

## CLI Pieslēgšanās

Piesakieties ar savām Chloros+ piekļuves datiem, lai aktivizētu CLI apstrādi. Linux (bez grafiskās lietotāja saskarnes) gadījumā šis ir vienīgais veids, kā aktivizēt savu licenci.

**Sintakse:**

```bash
chloros-cli login <email> <password>
```

{% hint style="info" %}
**SDK lietotāji**: Python SDK piedāvā arī programmatisku `logout()` metodi, lai dzēstu kešatmiņā saglabātās autentifikācijas datus. Sīkāku informāciju skatiet [SDK atsauces dokumentā](reference/sdk-reference.md).
{% endhint %}

**Piemērs:**

```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% hint style="warning" %}
**Īpašie simboli**: Parolēm, kurās ir simboli, piemēram, `$`, `!`, vai atstarpes, izmantojiet vienkāršās pēdiņas.
{% endhint %}

**Rezultāts:**

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: re-shoot the CLI login output — the banner now prints "Chloros CLI 1.2.0"; capture a successful login with the current output format. -->
### Piekļuves datu uzglabāšana

Cache atmiņā saglabātie piekļuves dati un konfigurācija **visās platformās** tiek uzglabāti jūsu lietotāja mājas direktorija mapē `.chloros`:

| Platforma | Piekļuves datu cache ceļš |
| --- | --- |
| **Windows** | `%USERPROFILE%\.chloros\` |
| **Linux** | `~/.chloros/` |

### Plāna derīguma termiņš un bezsaistes pārejas periods

GUI parādītais plāna derīguma termiņš norāda, kad jūsu licence vairs nebūs derīga. Atkārtotiem ikmēneša abonementiem derīguma termiņš beidzas mēneša beigās; gada abonementiem — gadu pēc abonementa sākšanas.

Chloros pārbauda jūsu licenci tiešsaistē, taču darbs bezsaistē ir atbalstīts pagarinājuma perioda ietvaros:

* Veiksmīgas servera pārbaudes tiek saglabātas kešatmiņā **5 minūtes**, tādēļ normālas lietošanas gadījumā tiek veikti ļoti nedaudzi licences pieprasījumi.
* Parakstīta, ar konkrētu ierīci saistīta licences cache nodrošina ilgāku darbību bezsaistē: **30 dienas ikmēneša plāniem**un**līdz jūsu abonementa derīguma termiņa beigām (ne vairāk kā 365 dienas) gada plāniem**.
* Kad pārejas periods beidzas, plāns pāriet uz bezmaksas „Iron” līmeni, līdz ierīce vismaz vienu reizi var sazināties ar licenču serveri; piekļuve tiek atjaunota pēc nākamās veiksmīgās pārbaudes.

### Ierīču skaita ierobežojums

Katrs Chloros+ plāns piedāvā atšķirīgu reģistrēto ierīču skaitu. Katra ierīce, kurā jūs piesakāties ar Chloros+ kontu, tiek iekļauta jūsu reģistrēto ierīču skaitā. Jūs varat pārdēvēt un dzēst ierīci savā MAPIR Cloud konta lapā.

<table><thead><tr><th width="168.5999755859375" align="right">Chloros+ plāns</th><th align="center">COPPER</th><th align="center">BRONZE</th><th align="center">SILVER</th><th align="center">GOLD</th></tr></thead><tbody><tr><td align="right">Atbalstītās ierīces</td><td align="center">2</td><td align="center">2</td><td align="center">5</td><td align="center">10</td></tr></tbody></table>Jūsu konta precīza ierīču kvota ir norādīta jūsu MAPIR Cloud konta lapā. Izietot no ierīces, tās vieta tiek droši atbrīvota, un jau reģistrēta ierīce vienmēr var atkal pieslēgties, pat ja kontam ir sasniegts ierīču limits.
