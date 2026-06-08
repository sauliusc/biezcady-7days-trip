# Bieszczady Trip Page — Claude darbo eigos instrukcijos

## Projektas

Statinis kelionės informacinis puslapis (`index.html`). Jokių build įrankių — viskas viename faile.
GitHub Pages adresas: `https://sauliusc.github.io/biezcady-7days-trip`

---

## Darbo eiga — PRIVALOMA vykdyti kiekvieną kartą

Kiekvieną kartą gavus instrukciją dėl pakeitimų, laikykis šios sekos:

### 1. Sukurti GitHub Issue

Prieš pradedant bet kokius darbus, sukurti issue su:
- **Pavadinimu**: trumpas pakeitimo aprašymas
- **Aprašymu**: kas bus pakeista, kokia informacija bus pridėta, planuojami darbai (checklist `- [ ]` formatu)
- Naudoti `mcp__github__issue_write` įrankį

### 2. Sukurti feature branch

Branch pavadinimo formatas: `claude/<trumpas-aprašymas>`
Pavyzdys: `claude/update-day6-kolejka`

### 3. Atlikti pakeitimus

- Redaguoti `index.html` tiesiogiai
- Commitinti į feature branch (ne į `main`)
- Commit žinutė turi aiškiai aprašyti pakeitimus

### 4. Sukurti Pull Request

Naudoti `mcp__github__create_pull_request`:
- **title**: trumpas pakeitimo pavadinimas
- **body**: nurodyti `Closes #<issue-numeris>`, aprašyti pakeitimus
- **head**: feature branch
- **base**: `main`

### 5. Auto-merge

GitHub Actions automatiškai sumergins PR į `main` (žr. `.github/workflows/auto-merge.yml`).

### 6. Užtikrinti deploy

**SVARBU:** Auto-merge ir deploy timing gali nesutapti — deploy kartais paleidžiamas PRIEŠ PR merge'ą ir pakeitimai nepatenkaa į puslapį. Po kiekvieno PR merge'o privaloma:

```bash
git fetch origin main && git checkout main && git pull origin main --rebase
git commit --allow-empty -m "chore: trigger deploy"
git push origin main
```

Šis tuščias commit garantuoja, kad deploy paleidžiamas su naujausiais pakeitimais.

### 7. Pranešti vartotojui

Baigus darbą, visada parodyti pranešimą:

```
✅ Pakeitimai matomi:
https://sauliusc.github.io/biezcady-7days-trip

📋 Issue: #<numeris>
🔀 PR: #<numeris> (sumergintas)
```

---

## Failo struktūra

```
biezcady-7days-trip/
├── index.html          ← vienintelis puslapis (HTML + CSS + JS inline)
├── CLAUDE.md           ← šios instrukcijos
└── .github/
    └── workflows/
        ├── deploy.yml       ← GitHub Pages deploy
        └── auto-merge.yml   ← automatinis PR merge
```

## Svarbios taisyklės

- **Niekada** nekeiski `main` branch tiesiogiai — tik per PR
- Kiekvienas pakeitimas = atskiras issue + atskiras PR
- `index.html` yra self-contained — nereikia jokių papildomų failų
- Puslapio kalba: **lietuvių**
- Turinys: Bieszczady 8 dienų kelionės planas, liepos 4–11, 8 žmonės
