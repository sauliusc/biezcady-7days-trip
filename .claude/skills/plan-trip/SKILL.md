---
name: plan-trip
description: Sukuria profesionalų kelionės planavimo puslapį (vieno failo index.html) su dienų planais, interaktyviu žemėlapiu, restoranais, biudžetu ir blogo oro planais. Naudoti, kai prašoma suplanuoti kelionę ar sukurti kelionės puslapį.
---

# Kelionės planavimo puslapio kūrimas

Sukuria pilną, savarankišką (self-contained) kelionės informacinį puslapį — vienas `index.html` failas su inline CSS + JS, tinkamas GitHub Pages.

## Parametrai (gauti iš vartotojo arba paklausti)

| Parametras | Aprašymas | Pavyzdys |
|---|---|---|
| `FROM_CITY` | Iš kur vykstama | Vilnius |
| `TRIP_LOCATION` | Kelionės kryptis/regionas | Bieszczady, Lenkija |
| `TRIP_START` / `TRIP_END` | Datos | 2026-07-04 – 2026-07-11 |
| `TRANSPORT` | Transporto tipas ir kiekis | 2 automobiliai |
| `PEOPLE` | Žmonių kiekis | 8 |
| `ACCOMMODATION_NAME` / `ACCOMMODATION_URL` | Gyvenamoji vieta (jei žinoma) | Chata Sękata, booking.com nuoroda |

## 1 FAZĖ — Tyrimas (WebSearch / agentai)

Kiekvienai temai naudoti atskirą paiešką ar research agentą:

1. **Gyvenamoji vieta** — adresas, GPS, telefonas, atvykimo laikas, kaina.
2. **Žygiai** — 2–4 žygio dienos. Kiekvienam žygiui:
   - **2–3 maršruto variantai** (ilgas / vidutinis / lengvas) su km, aukščio pralaimu, laiku, takų spalvomis;
   - **AllTrails reitingai** — žvaigždutės, atsiliepimų kiekis, populiarumo vieta (🥇🥈✨ badge);
   - **parkavimo aikštelės** — pavadinimas, GPS, kaina, kada užsipildo (⚠️ įspėjimai).
3. **Restoranai** — **3 variantai kiekvienai dienai** (pakeliui grįžtant), nebrangūs, vietiniai. Pavadinimas, adresas, telefonas, kuo garsėja, reitingas. Restoranai gali kartotis tarp dienų.
4. **SPA / poilsis** — 1–2 SPA dienos su kainomis ir telefonais.
5. **Papildomos atrakcijos** — cerkvės/bažnyčios (UNESCO!), gamtos objektai, keltuvai/gondolos, tyrolkos (nusileidimai lynu), vasaros rogutės/tubingas, raftingas, drezinos, muziejai, saldumynų vietos. Kiekvienai: kaina, darbo laikas, amžiaus apribojimai, **oficiali svetainė** (informacijai ir bilietams).
6. **Blogo oro planai** — **visada 2 pilnos rezervinės dienos** (muziejai, pilys, uždarės atrakcijos, ~1–1.5 val. atstumu).
7. **Biudžetas** — nakvynė, kuras, bilietai, maistas, SPA, parkavimas, vakaro gėrimai. Lentelė vienam asmeniui ir visai grupei.

## 2 FAZĖ — Puslapio struktūra

Vienas `index.html`. Tamsi miško tema:
```css
--bg: #0b1710; --accent: #c9a84c; --green: #4a8c5c;
```

Sekcijos eilės tvarka:
1. Hero (pavadinimas, datos, žmonės, transportas)
2. Savaitės apžvalga (grid kortelės)
3. Dienų kortelės (išskleidžiamos, po vieną kiekvienai dienai)
4. **2 blogo oro dienos** — apipavidalintos kaip normalios dienos, bet **uždarytos pagal nutylėjimą** (`aria-expanded="false"`, `style="max-height:0;overflow:hidden"`), accent spalva `#5a7a9a`, emoji 🌧️
5. Interaktyvus žemėlapis (Leaflet + CARTO dark tiles) **su legenda po juo**
6. Biudžetas
7. Pasiruošimo sąrašas
8. Kontaktai (pagalbos tarnybos, GOPR, gyvenamoji vieta, atrakcijos)

### Dienos kortelė

- Antraštė: emoji, dienos nr., pavadinimas, data, **AllTrails badge** žygio dienoms:
  `⭐ 4.8 / 5 · 116 atsiliepimų · 🥈 #2 pagal lankymą · AllTrails ↗`
- Maršruto variantai (A/B/C) su waypoint'ais ir takų spalvomis
- Laiko juosta (timeline) su valandomis
- `info-chip` eilutės: parkavimas, ➕ pakeliui esančios atrakcijos, 🍽️ restoranai
- Išskleidimas: po atidarymo animacijos `maxHeight='none'` (transitionend), uždarant `maxHeight='0px'` — kitaip ilgas turinys nukerpamas!

### Google Maps nuorodos — SVARBIAUSIA TAISYKLĖ

**Niekada nenaudoti koordinačių** (`maps.google.com/?q=lat,lon` rodo į mišką!).
Visada naudoti pavadinimu paremtą paiešką **su miestu IR šalimi**:

```
https://www.google.com/maps/search/?api=1&query=Pizzeria+Roma%2C+Baligr%C3%B3d%2C+Polska
```

- Restoranai: `Pavadinimas, Miestas, Šalis` (be šalies „Pizzeria Roma" atidaro Romą!)
- Parkavimai: oficialus pavadinimas, pvz. `Parking BdPN Wołosate, Polska`
- Kur pavadinimas nepatikimas — pridėti gatvės adresą.
- Maps nuorodą dėti prie **kiekvieno objekto, į kurį reikia važiuoti**: laiko juostoje, chip'uose ir žemėlapio popup'uose. Formatas: `📍 Pavadinimas ↗`

### Interaktyvus žemėlapis (Leaflet)

Markeriai — objektai su `mapsUrl` lauku; popup'e rodoma Maps nuoroda:

```js
var points = [
  { ll:[49.34,22.28], emoji:'🏠', color:'#c9a84c', title:'Bazė',
    mapsUrl:'https://www.google.com/maps/search/?api=1&query=...',
    body:'<b>Adresas</b><br>📞 tel<br><a href="https://oficiali.svetaine" target="_blank" style="color:#c9a84c">svetainė ↗</a>' },
];
points.forEach(function(p) {
  var mapsLink = p.mapsUrl ? '<br><a href="' + p.mapsUrl + '" target="_blank" style="color:#c9a84c;font-size:0.8em">📍 Google Maps ↗</a>' : '';
  L.marker(p.ll, { icon: icon(p.emoji, p.color) })
   .bindPopup('<div style="font-family:Inter,sans-serif;min-width:160px"><b style="color:#c9a84c">' + p.title + '</b><br><span style="font-size:0.85em;color:#888">' + p.body + mapsLink + '</span></div>', { maxWidth: 240 })
   .addTo(map);
});
```

Spalvų/emoji konvencija ir legenda po žemėlapiu (18px spalvoti apskritimai):
- 🏠 bazė `#c9a84c` · ⛰️ žygiai `#5db060` · 🅿️ parkavimas `#4a6b4c` · 🍽️ restoranai `#c97a4a` (VISI viena ikona!) · ⛪ cerkvės/kultūra `#9c6fc9` · 🚂🚡🛷🚠 veiklos `#3a8c8c` · 🦬 gamta `#5db060` · 🌧️ blogas oras `#5a7a9a`

### KML eksportas

Paprašius — sugeneruoti `bieszczady-trip.kml` (ar analogišką) su visais žemėlapio taškais, sugrupuotais į `<Folder>` (bazė, parkavimas, žygiai, SPA, kultūra, veiklos, blogas oras, restoranai), importui į Google My Maps. Koordinatės KML formatu: `lon,lat,0`.

## 3 FAZĖ — Deploy darbo eiga

Laikytis repo CLAUDE.md: **issue → `claude/<branch>` nuo šviežio `origin/main` → pakeitimai → PR (`Closes #N`) → auto-merge → deploy ~2–3 min**.

Pastabos iš patirties:
- Prieš naują branch **visada** `git fetch origin main` ir šakotis nuo `origin/main` (ne nuo seno branch'o — squash-merge po PR padaro seną branch'ą „dirty").
- Sudėtingiems pakeitimams su lenkiškais rašmenimis naudoti Python `str.replace()`, ne Edit tool (koduotės problemos).
- Deploy trigger'ina `auto-merge.yml` per `createWorkflowDispatch` — tuščių commitų nereikia.
- Baigus visada parodyti: puslapio URL, issue nr., PR nr.

## Kalba ir turinys

- Puslapio kalba — **lietuvių** (arba vartotojo nurodyta).
- Kainos vietine valiuta (PLN ir pan.) su ~EUR ekvivalentu biudžete.
- Saugumo įspėjimai žygiams (🐺 laukiniai gyvūnai, oro sąlygos, vandens atsargos).
- Kiekvienai atrakcijai — oficiali svetainė bilietams + Maps nuoroda + telefonas.
