# Chloros+ Pieslēgšanās

## Chloros un Chloros (pārlūkprogramma) Pieslēgšanās

Lietotāja <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> sānu joslas izvēlne ļauj jums pieteikties savā Chloros+ kontā un atbloķēt papildu funkcijas.

Pēc pieteikšanās tiks parādīta jūsu konta informācija:

<figure><img src=".gitbook/assets/user_account.JPG" alt="" width="375"><figcaption></figcaption></figure>## CLI Pieslēgšanās

Piesakieties ar savām Chloros+ piekļuves datiem, lai aktivizētu CLI apstrādi. Linux (bez grafiskās lietotāja saskarnes) versijā tas ir vienīgais veids, kā aktivizēt savu licenci.

**Sintakse:**

```bash
chloros-cli login <email> <password>
```

{% hint style="info" %}
**SDK lietotāji**: Python SDK piedāvā arī programmatisku `logout()` metodi, lai dzēstu kešatmiņā saglabātās autentifikācijas datus. Sīkāku informāciju skatiet [Python SDK dokumentācijā](api-python-sdk.md#logout).
{% endhint %}

**Piemērs:**

```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% hint style="warning" %}
**Īpašie simboli**: Parolēm, kurās ir simboli, piemēram, `$`, `!` vai atstarpes, izmantojiet vienkāršās pēdiņas.
{% endhint %}

**Rezultāts:**

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>### Pieslēgšanās datu uzglabāšana

Cache atmiņā saglabātie pieslēgšanās dati tiek uzglabāti platformai specifiskā vietā:

| Platforma | Pieslēgšanās datu cache ceļš |
| --- | --- |
| **Windows** | `%APPDATA%\Chloros\cache\` |
| **Linux** | `~/.cache/chloros/` |

### Plāna derīguma termiņš

Plāna derīguma termiņš GUI rāda, kad jūsu licence vairs nebūs derīga. Atkārtotiem ikmēneša abonementiem derīguma termiņš beidzas mēneša beigās. Gada abonementiem tas ir gads pēc abonementa sākuma. Licences pārbaudei ir nepieciešams ikmēneša interneta savienojums, ar 30 dienu papildlaiku.

### Ierīču limits

Katrs Chloros+ plāns piedāvā atšķirīgu reģistrēto ierīču skaitu. Katra ierīce, kurā jūs piesakāties ar Chloros+ kontu, tiks ieskaitīta jūsu reģistrēto ierīču skaitā. Jūs varat pārdēvēt un noņemt ierīci savā MAPIR Cloud konta lapā.

<table><thead><tr><th width="168.5999755859375" align="right">Chloros+ plāns</th><th align="center">COPPER</th><th align="center">BRONZE</th><th align="center">SILVER</th><th align="center">ZELTS</th></tr></thead><tbody><tr><td align="right">Atbalstītās ierīces</td><td align="center">2</td><td align="center">2</td><td align="center">5</td><td align="center">10</td></tr></tbody></table>
