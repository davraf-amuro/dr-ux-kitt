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

- Dipende da: `dr-fe`. Il tema fornisce i token di `design-tokens.instructions.md` di `dr-fe` (nomi, coppie `on-*`, soglie di contrasto, tema chiaro/scuro con `data-theme`, Regola 8 su animazioni e dipendenze); senza quel contratto le regole del tema non hanno base. Quando la voce del pacchetto è nel catalogo del core, l'installer installa `dr-fe` prima, se manca (vedi sotto lo stato del catalogo).
- Richiesto da: nessuno.
- Applicabilità nel catalogo: `appliesTo: node`.
- Stack richiesto nell'host: Vue 3, Tailwind CSS 4 con `@tailwindcss/vite`, Node per lo script dei contrasti. Font `@fontsource-variable/saira` e `@fontsource-variable/jetbrains-mono`: il prompt propone di installarli se mancano e li installa solo dopo la tua conferma (Regola 8 di `dr-fe`).
- `-Update` non si propaga alle dipendenze: aggiornare solo questo pacchetto lascia `dr-fe` com'è. `/dr-get-latest` aggiorna anche `dr-fe`, che ha una propria voce nel manifest.
- Stato nel catalogo di `dr-guidelines`: la voce `dr-ux-kitt` (e il suo inserimento tra gli opzionali di `vue-fe`) arriva con una modifica separata del core. Fino ad allora `/dr-scaffold` non lo propone e l'installer del pacchetto, che si appoggia alla libreria del core, può non trovarlo nel catalogo.

File di altri pacchetti citati:

| File citato | Chi lo cita | Pacchetto che lo porta |
|-------------|-------------|------------------------|
| `design-tokens.instructions.md` | Istruzione, prompt, script | `dr-fe` (dipendenza dichiarata) |
| `frontend-organization.instructions.md` | Prompt | `dr-fe` (dipendenza dichiarata) |

## 🚀 Come si installa

Prerequisiti: PowerShell 7, git, `gh auth status` autenticato. Esegui dalla **root del repository del frontend**: l'installer usa la cartella corrente come destinazione e non avvisa se sbagli cartella.

Via core:

```powershell
Set-Location <root-del-frontend>
& ([scriptblock]::Create((gh api repos/davraf-amuro/dr-guidelines/contents/dr-guidelines-install.ps1 -H "Accept: application/vnd.github.raw" | Out-String))) -Package dr-ux-kitt
```

Oppure con l'installer del pacchetto:

```powershell
& ([scriptblock]::Create((gh api repos/davraf-amuro/dr-ux-kitt/contents/dr-ux-kitt-install.ps1 -H "Accept: application/vnd.github.raw" | Out-String)))
```

L'installer porta istruzione, prompt e script: **non modifica i sorgenti del frontend**. Il tema si applica dopo, con il prompt `dr-ux-kitt-apply.prompt.md`.

## 📦 Cosa finisce nel progetto host

| Percorso nel progetto host | Contenuto |
|----------------------------|-----------|
| `.github/instructions/kitt-theme.instructions.md` | Copia dell'istruzione del tema |
| `.github/prompts/dr-ux-kitt-apply.prompt.md` | Copia del prompt di applicazione |
| `scripts/kitt-contrast.mjs` | Script di verifica dei contrasti |
| `.ai/dr-guidelines-packages.json` | Voce `dr-ux-kitt` in `installed`; anche `dr-fe` e `dr-guidelines`, se installati come dipendenze |

Dopo il prompt di applicazione cambiano anche `src/assets/styles/tokens.css`, `main.css`, il guscio (`src/App.vue`, `AppSidebar.vue`, `AppHeader.vue`), i componenti condivisi, `index.html` e `public/site.webmanifest`: sempre con diff mostrato e conferma.

Il pacchetto non modifica `CLAUDE.md` né i file di configurazione. Non vengono copiati `README.md`, `LICENSE`, `.github/ISSUE_TEMPLATE/` e l'installer `dr-ux-kitt-install.ps1`. Senza `-Update` un file già presente viene saltato (`[SKIP]`).

## 🔄 Aggiornare

Tutti i pacchetti del progetto: `/dr-get-latest`.

Solo questo pacchetto:

```powershell
& ([scriptblock]::Create((gh api repos/davraf-amuro/dr-ux-kitt/contents/dr-ux-kitt-install.ps1 -H "Accept: application/vnd.github.raw" | Out-String))) -Update
```

`-Update` sovrascrive istruzione, prompt e script, mai i sorgenti del frontend. Per portare nel frontend una correzione del tema, rilancia il prompt di applicazione.

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

*Revisione v1.0 — 2026-10-14 14:00 — claude-opus-5-5*
