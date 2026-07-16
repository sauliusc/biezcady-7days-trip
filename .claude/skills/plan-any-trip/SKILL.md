---
name: plan-any-trip
description: Universalus bet kokio tipo kelionės planavimo skill — miesto arba gamtos kelionėms, automobiliu arba autobusu, bet kuriai šaliai. Sukuria profesionalų vieno failo kelionės puslapį su dienų planais, interaktyviu žemėlapiu ir pagal šalį/kelionės tipą parinkta dizaino tema. Naudoti, kai prašoma suplanuoti bet kokią kelionę.
---

# Universalus kelionės planavimas

Pagrįstas `plan-trip` skill'u (žr. `.claude/skills/plan-trip/SKILL.md` — ten pilnos HTML/Leaflet/Maps techninės detalės). Šis skill'as apibendrina jį bet kokiam kelionės tipui.

## 1 ŽINGSNIS — Klausimai vartotojui (AskUserQuestion)

Jei parametrai nenurodyti užklausoje, **privalomai paklausti**:

### Baziniai parametrai
| Parametras | Pavyzdys |
|---|---|
| `FROM_CITY` | Vilnius |
| `TRIP_LOCATION` | Roma / Bieszczady / Norvegijos fjordai |
| `TRIP_START` / `TRIP_END` | 2026-09-10 – 2026-09-17 |
| `PEOPLE` | 8 |
| `ACCOMMODATION` | jei žinoma — pavadinimas + URL |

### Papildomi privalomi klausimai
1. **Transportas: automobiliu ar autobusu?**
   - *Automobiliu* → planuoti parkavimus (pavadinimai, kainos, kada užsipildo), kuro biudžetą, navigacijos Maps nuorodas prie kiekvieno objekto, kelionių laikus tarp objektų.
   - *Autobusu / viešuoju transportu* → planuoti stoteles, tvarkaraščius, bilietų kainas ir pirkimo nuorodas, pėsčiųjų atstumus nuo stotelių; parkavimo sekcijų nekurti.
2. **Kelionės tipas: miesto ar gamtos?**
   - *Miesto* → muziejai, architektūra, senamiesčiai, restoranai, kavinės, vakarinės veiklos; dienos skirstomos pagal rajonus/kvartalus; „blogo oro" dienos beveik nereikalingos (daug vidaus objektų) — vietoj jų 1 rezervinė diena.
   - *Gamtos* → žygiai su maršrutų variantais, parkavimai prie takų, saugumo įspėjimai (laukiniai gyvūnai, oras, vanduo), **visada 2 blogo oro dienos** su vidaus atrakcijomis ~1–1.5 val. spinduliu.
   - *Mišri* → derinti abu.
3. **Ar reikalingi AllTrails maršrutai?**
   - *Taip* → kiekvienam žygiui rasti AllTrails maršrutą: reitingas, atsiliepimų kiekis, populiarumo badge (🥇🥈✨), nuoroda `AllTrails ↗` dienos antraštėje.
   - *Ne* → naudoti oficialius parkų/turizmo šaltinius, be AllTrails badge.
4. **Dizaino tema** — parinkti automatiškai pagal šalį ir kelionės tipą (žr. žemiau), pasiūlyti vartotojui patvirtinti.

## 2 ŽINGSNIS — Dizaino tema pagal šalį / tipą

Visada tamsi bazė + akcentinė paletė, atspindinti kryptį. CSS kintamieji: `--bg`, `--accent`, `--accent2`, `--text`. Pavyzdinės paletės:

| Kryptis / tipas | `--bg` | `--accent` | Nuotaika |
|---|---|---|---|
| Miškai, Karpatai, Skandinavijos gamta | `#0b1710` (tamsi žalia) | `#c9a84c` (aukso) | miško prieblanda |
| Viduržemio miestai (Italija, Ispanija, Graikija) | `#1a1410` (šilta ruda) | `#d4763b` (terakota) | saulėta terra |
| Šiaurės miestai (Amsterdamas, Kopenhaga, Berlynas) | `#10141a` (tamsi mėlyna) | `#5b9bd5` (šaltas mėlynas) | urban cool |
| Alpės, fjordai, kalnai | `#0e1418` (grafito) | `#7ec8e3` (ledo mėlyna) | ledynų |
| Egzotika, pajūris (Azija, salos) | `#0d1a17` | `#3bc9a8` (turkio) | tropikai |
| Istoriniai miestai (Praha, Krokuva, Viena) | `#171310` | `#c9a84c` + `#8c3a3a` (bordo) | imperinė |

Emoji hero sekcijoje ir dienų kortelėse taip pat pritaikyti temai (🏛️ miestui, ⛰️ kalnams, 🏖️ pajūriui…).

## 3 ŽINGSNIS — Tyrimas

Kaip `plan-trip` 1 fazė, adaptuota tipui:

- **Kiekvienai dienai po 3 restoranus** (pakeliui/rajone), su adresu, telefonu, kuo garsėja.
- **Atrakcijos su oficialiomis svetainėmis** bilietams + kainos + darbo laikas + amžiaus apribojimai.
- **Miesto kelionei papildomai:** city pass'ai / muziejų kortelės (ar apsimoka), bilietų rezervacijos iš anksto (populiariems objektams!), viešojo transporto bilietų sistema.
- **Autobusinei kelionei papildomai:** tarpmiestinių autobusų/traukinių tvarkaraščiai ir bilietų platformos.
- **Biudžetas:** nakvynė, transportas (kuras+parkavimas ARBA bilietai), maistas, atrakcijos, vakaro gėrimai; lentelė asmeniui ir grupei, vietine valiuta + EUR.

## 4 ŽINGSNIS — Puslapis

Naudoti `plan-trip` 2 fazės struktūrą ir techniką (dienų kortelės, timeline, info-chips, Leaflet žemėlapis su legenda, KML eksportas paprašius), su šiais skirtumais:

- **Google Maps taisyklė galioja visada:** nuorodos tik `pavadinimas + miestas + šalis` per `google.com/maps/search/?api=1&query=...`, niekada koordinatės.
- Miesto kelionei žemėlapio legendos kategorijos: 🏨 nakvynė · 🏛️ lankytini objektai · 🍽️ restoranai · ☕ kavinės · 🚇 stotelės · 🛍️ apsipirkimas.
- Gamtos kelionei — kaip `plan-trip` (žygiai, parkavimai, blogas oras…).
- AllTrails badge tik jei vartotojas pasirinko „taip".

## 5 ŽINGSNIS — Deploy

Laikytis repo CLAUDE.md darbo eigos: issue → `claude/<branch>` nuo šviežio `origin/main` → PR (`Closes #N`) → auto-merge → pranešimas su URL, issue ir PR numeriais.
