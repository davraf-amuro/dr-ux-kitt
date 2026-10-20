# dr-ux-kitt

Pacchetto del tema grafico K.I.T.T. per frontend Vue 3 + Tailwind v4, ispirato al cruscotto della supercar di Knight Rider: console scura con tasti retroilluminati, LED decorativi, scanner e modulatore di voce animati, tema "notte" e tema "garage diurno", con verifica automatica dei contrasti.

## 🧩 Cosa contiene

| File | Tipo | A cosa serve |
|------|------|--------------|
| `.github/instructions/kitt-theme.instructions.md` | Istruzione | Regole del tema: token propri per ruolo (`--c-kitt-led-*`, `--c-on-kitt`, `--c-console*`, `--kitt-*`), tipografia Saira larga e corsiva con JetBrains Mono per le letture numeriche, guscio a console con tasti, voce attiva riconoscibile senza colore, forma K.I.T.T. dei componenti condivisi, `ScannerBar` e `VoiceModulator`, `theme-color`, verifica. |
| `.github/prompts/dr-ux-kitt-apply.prompt.md` | Prompt | Applica il tema a un frontend esistente. Contiene i sorgenti di riferimento (`tokens.css` dei due temi, `main.css`, guscio, componenti condivisi e di tema). Mostra il diff e chiede conferma per ogni file dell'host: non sovrascrive mai in silenzio. Su Claude Code si usa chiedendo all'agente di seguire il prompt. |
| `scripts/kitt-contrast.mjs` | Script Node | Legge `tokens.css` e verifica i contrasti WCAG 2.x di ogni coppia, in chiaro e in scuro, compresi LED, console e focus ambra sulla console. Exit code `0` conforme, `1` coppie sotto soglia, `2` file illeggibile o token mancanti. Nessuna dipendenza, nessuna rete. |

### Il tema

| Tema | Ispirazione | Fondo | Primario | Allerta |
|---|---|---|---|---|
| Scuro "notte" | Abitacolo di notte | `#050505` | ambra `#ffb21e` | rosso `#ff4d3d` |
| Chiaro "garage diurno" | Officina alla luce del giorno | `#e6e7ea` | bronzo `#8f3f00` | rosso `#b3261e` |

La console (sidebar e testata) è scura in entrambi i temi. Il rosso è riservato a decorazione e allerta, il primario è ambra: azione principale e pericolo restano distinti.

## 🔗 Dipendenze e domini

- Dipende da: `dr-fe`. Il tema fornisce i token di `design-tokens.instructions.md` di `dr-fe` (nomi, coppie `on-*`, soglie di contrasto, tema chiaro/scuro con `data-theme`, Regola 8 su animazioni e dipendenze); senza quel contratto le regole del tema non hanno base. L'installer installa `dr-fe` prima, se manca.
- Richiesto da: nessuno.
- Applicabilità nel catalogo: `appliesTo: node`.
- Dominio del catalogo: nessuno lo elenca; il dominio `web-frontend` elenca solo `dr-fe`. Tipologia che lo propone: `vue-fe`, in `optionalPackages`.
- Stack richiesto nell'host: Vue 3, Tailwind CSS 4 con `@tailwindcss/vite`, Node per lo script dei contrasti. Font `@fontsource-variable/saira` e `@fontsource-variable/jetbrains-mono`: il prompt propone di installarli se mancano e li installa solo dopo la tua conferma (Regola 8 di `dr-fe`).
- `-Update` non si propaga alle dipendenze: aggiornare solo questo pacchetto lascia `dr-fe` com'è. `/dr-get-latest` aggiorna anche `dr-fe`, che ha una propria voce nel manifest.
- Stato nel catalogo di `dr-guidelines`: la voce `dr-ux-kitt` è presente e compare tra gli opzionali di `vue-fe`: `/dr-scaffold` propone il tema e l'installer lo trova.
- Mutuamente esclusivo con `dr-ux-lcars`: un solo tema per host. Le istruzioni dei due temi hanno lo stesso `applyTo` (`**/*.css,**/*.vue`) e si contraddicono se installate insieme; il catalogo li dichiara in `conflictsWith`: se l'altro tema è già nel manifest l'installer avvisa (`[WARN]`) ma non blocca. Per cambiare tema, rimuovi prima l'altro.

File di altri pacchetti citati:

| File citato | Chi lo cita | Pacchetto che lo porta |
|-------------|-------------|------------------------|
| `design-tokens.instructions.md` | Istruzione, prompt, script | `dr-fe` (dipendenza dichiarata) |
| `frontend-organization.instructions.md` | Prompt | `dr-fe` (dipendenza dichiarata) |

## 🚀 Come si installa

Di solito non serve installarlo a mano: `/dr-scaffold` lo propone come pacchetto opzionale della tipologia `vue-fe`.

A mano. Prerequisiti: PowerShell 7 e git (l'installer clona il repo con `git clone --depth 1`). `gh` autenticato (`gh auth status`) serve solo alle forme `gh api` qui sotto: i repo sono Public dal 2026-09-21. Su un repo Private anche git deve poterlo leggere (`gh auth setup-git`). Esegui dalla **root del repository del frontend**: l'installer usa la cartella corrente come destinazione e non avvisa se sbagli cartella.

Prima di scrivere, l'installer annuncia le dipendenze mancanti nell'ordine in cui le installa. In un progetto vuoto:

```text
  [dep]  dr-guidelines (richiesta da dr-ux-kitt, core implicito)
  [dep]  dr-fe (richiesta da dr-ux-kitt)
```

Via core:

```powershell
Set-Location <root-del-frontend>
& ([scriptblock]::Create((gh api repos/davraf-amuro/dr-guidelines/contents/dr-guidelines-install.ps1 -H "Accept: application/vnd.github.raw" | Out-String))) -Package dr-ux-kitt
```

Oppure con l'installer del pacchetto:

```powershell
& ([scriptblock]::Create((gh api repos/davraf-amuro/dr-ux-kitt/contents/dr-ux-kitt-install.ps1 -H "Accept: application/vnd.github.raw" | Out-String)))
```

Nota: la forma breve `irm https://raw.githubusercontent.com/... | iex` è un'alternativa valida, perché i repo sono Public. Su un repo Private risponderebbe 404: lì si usa `gh api`.

L'installer porta istruzione, prompt e script: **non modifica i sorgenti del frontend**. Il tema si applica dopo, con il prompt `dr-ux-kitt-apply.prompt.md`.

## 📦 Cosa finisce nel progetto host

| Percorso nel progetto host | Contenuto |
|----------------------------|-----------|
| `.github/instructions/kitt-theme.instructions.md` | Copia dell'istruzione del tema |
| `.github/prompts/dr-ux-kitt-apply.prompt.md` | Copia del prompt di applicazione |
| `scripts/kitt-contrast.mjs` | Script di verifica dei contrasti |
| `.ai/dr-guidelines-packages.json` | Voce `dr-ux-kitt` in `installed`; anche `dr-fe` e `dr-guidelines`, se installati come dipendenze |

Dopo il prompt di applicazione cambiano anche `src/assets/styles/tokens.css`, `main.css`, il guscio (`src/App.vue`, `AppSidebar.vue`, `AppHeader.vue`), i componenti condivisi, `index.html` e `public/site.webmanifest`: sempre con diff mostrato e conferma.

Il pacchetto non modifica `CLAUDE.md` né i file di configurazione (`.editorconfig`, `.gitignore`, `.gitattributes`, `.claude/settings.json`, `.mcp.json`). Li scrive il core, solo se viene installato insieme perché mancava dal manifest; `.mcp.json` solo con `-WithMcp` dell'installer del core.

Non vengono copiati `README.md`, `LICENSE`, `.github/ISSUE_TEMPLATE/` e l'installer `dr-ux-kitt-install.ps1`.

Senza `-Update` un file già presente viene saltato (`[SKIP]`).

## 🔄 Aggiornare

Tutti i pacchetti del progetto: `/dr-get-latest`.

Solo questo pacchetto:

```powershell
& ([scriptblock]::Create((gh api repos/davraf-amuro/dr-ux-kitt/contents/dr-ux-kitt-install.ps1 -H "Accept: application/vnd.github.raw" | Out-String))) -Update
```

`-Update` sovrascrive istruzione e prompt. Lo script dei contrasti si sovrascrive solo se non l'hai modificato; altrimenti la versione nuova va in `scripts/kitt-contrast.mjs.new` e l'originale resta com'è. Mai i sorgenti del frontend. Per portare nel frontend una correzione del tema, rilancia il prompt di applicazione.

Un file rimosso da questo repo resta nel progetto host: l'installer copia, non cancella. Fa eccezione un file elencato in `obsoleteArtifacts` del catalogo: `-Update` lo rimuove.

## 🐞 Segnalare un problema o una miglioria

Non correggere la copia nel progetto host: si perde al primo `-Update`.

Dal progetto host usa `/dr-file-feedback <descrizione>` (su Copilot il prompt `.github/prompts/dr-file-feedback.prompt.md` del core). La issue si apre in questo repo, dopo la tua conferma esplicita di titolo e corpo.

| Modello del repo | Quando usarlo |
|------------------|---------------|
| `.github/ISSUE_TEMPLATE/miglioria.md` | Richiesta evolutiva: regola nuova, precisazione, estensione del pacchetto |
| `.github/ISSUE_TEMPLATE/problema.md` | Malfunzionamento: regola sbagliata, ambigua o che porta l'agente fuori strada |

## ⚖️ Marchi

K.I.T.T. e Knight Rider sono marchi dei rispettivi titolari (NBCUniversal). Questo pacchetto è un omaggio non ufficiale: non è affiliato, approvato o sponsorizzato da loro, non usa materiale grafico originale e il nome "K.I.T.T." indica solo l'ispirazione estetica.

---

*Revisione v1.2 — 2026-10-20 21:00 — claude-opus-5-5*
