---
applyTo: "**/*.css,**/*.vue"
---

# Tema K.I.T.T. — Cruscotto, ruoli dei colori e componenti

Scopo: regole del tema K.I.T.T. (cruscotto ispirato a Knight Rider) per i frontend Vue 3 + Tailwind v4. La struttura (console con tasti, tipografia, scanner, componenti) è una sola e ha due temi: "notte" (scuro) e "garage diurno" (chiaro), che cambiano solo i valori dei token. La console resta scura in entrambi.

Presuppone `design-tokens.instructions.md` di `dr-fe` (dipendenza del pacchetto): nomi dei token semantici, coppie `on-*`, soglie di contrasto, tema chiaro/scuro e **Regola 8** (ripiego `prefers-reduced-motion` per ogni animazione, nessuna dipendenza oltre ai font, stato mai solo colore) stanno lì e non si ripetono qui. Per applicare il tema a un progetto si usa il prompt `dr-ux-kitt-apply.prompt.md`.

---

## Regola 1 — Token propri del tema

Definiti in `tokens.css` accanto ai token semantici di dr-fe. LED e console sono **uguali in tema chiaro e scuro**.

| Token | Ruolo |
|---|---|
| `--c-kitt-led-alert` | LED di allerta (rosso): scanner, filetto dell'eroe, `HazardStripe`, spia di errore |
| `--c-kitt-led-primary` | LED principale (ambra): tasti, modulatore, anello di focus sulla console, spia attiva |
| `--c-kitt-led-ok` | LED di esito positivo (verde) |
| `--c-kitt-led-caution` | LED di avviso (giallo) |
| `--c-on-kitt` | Unico colore di testo e icone sopra un LED (`#000000`) |
| `--c-console` | Fondo della console (sidebar, testata) |
| `--c-console-raised` | Fondo dei tasti della console, rialzato |
| `--c-console-line` | Linee e separatori della console (decorativi) |
| `--c-console-ink` | Testo sulla console |
| `--kitt-bar` | Spessore delle barre e dei segmenti (`0.875rem`) |
| `--kitt-gap` | Fessura tra segmenti (`3px`) |
| `--kitt-radius` | Raggio degli spigoli (`4px`) |
| `--stencil-tracking` | Spaziatura delle etichette maiuscole (`0.12em`, token di dr-fe, valore del tema) |

- Nomi di **ruolo**, mai di colore (Regola 7 di `design-tokens`): un componente che scrive `--c-kitt-red` è legato a una palette. Scelta D5: la prova originale usava `-red`, `-amber`, `-green`, `-yellow`; qui i valori sono gli stessi con nomi di ruolo.
- Sopra un LED si scrive solo con `--c-on-kitt`, contrasto ≥ 4.5:1 con ogni LED. Nessun testo informativo direttamente su `--c-kitt-led-*` senza `--c-on-kitt`.
- LED e console sono decorazione e cornice: azioni, errori ed esiti usano i token semantici di dr-fe. Il rosso resta riservato a decorazione e allerta; il primario del tema è l'ambra (chiaro: bronzo), così l'azione principale non si confonde col pericolo.
- Esposti a Tailwind con `@theme inline` (`bg-console`, `text-console-ink`, `bg-kitt-led-primary`, `text-on-kitt`).
- Ogni voce della console imposta `--key-led` (il LED della voce) a un `--c-kitt-led-*`: nessun esadecimale nei componenti.

---

## Regola 2 — Tipografia

- Font: Saira variabile (`@fontsource-variable/saira`, assi `wdth` e corsivo: import `wdth.css` e `wdth-italic.css`) e JetBrains Mono per le letture numeriche (`@fontsource-variable/jetbrains-mono`). I font sono le uniche dipendenze npm del tema e si installano **solo su conferma esplicita** (Regola 8 di `design-tokens`).
- Titoli `h1`–`h3`: `font-stretch: 125%`, corsivo, peso 700, maiuscolo.
- Utility `.stencil` (sopratitoli, etichette, tasti): `font-stretch: 112.5%`, maiuscolo, `letter-spacing: var(--stencil-tracking)`.
- Utility `.font-console` (letture numeriche, orari, contatori): JetBrains Mono, cifre tabulari.
- Il maiuscolo e il corsivo restano su `h1`–`h3` e `.stencil`: il testo corrente è a larghezza normale (`font-stretch: 100%`), senza maiuscolo né corsivo forzati.

---

## Regola 3 — Guscio a console

**Desktop (≥ 48rem)**: griglia `15rem | 1fr`. La colonna sinistra è la console: fondo `--c-console`, tasti rettangolari in `--c-console-raised` separati da `--kitt-gap`, uno per voce, con il proprio LED in `--key-led` (barra luminosa sul bordo sinistro del tasto) e testo `--c-console-ink`.

**Testata**: striscia `--c-console` con `ScannerBar`, marchio dell'app, contenuto di stato dell'host e tasto del tema (rettangolare, alto almeno `--hit-target-min`).

**Mobile (< 48rem)**: la console diventa una barra fissa in basso, una riga di tasti con lettering ristretto (`font-stretch` minore, testo corto); il contenuto principale lascia spazio in basso.

**Voce attiva**: fondo pieno nel LED della voce (`--key-led`), testo `--c-on-kitt`, peso 800, spia "ON" testuale e bagliore. Il link attivo ha `aria-current="page"`. Lo stato non è mai affidato al solo colore o al solo bagliore: restano la spia "ON", il peso e il fondo pieno, riconoscibili anche in scala di grigi (Regola 8 di `design-tokens`).

**Focus**: sul fondo console l'anello di focus è `--c-kitt-led-primary` (ambra) in entrambi i temi, ≥ 3:1 su `--c-console` e `--c-console-raised`. Sul resto dell'app vale l'anello unico di dr-fe con `--c-federal`.

> **Insidia nota.** La visibilità della barra e degli elementi solo desktop si imposta con classi Tailwind (`hidden md:flex`, `md:hidden`), **non** con `display` nello `<style scoped>`: lo stile scoped vince su `hidden` e l'elemento comparirebbe su mobile.

---

## Regola 4 — Componenti condivisi e di tema

Props, slot ed eventi **invariati** rispetto alla versione dell'host: il tema cambia template e stile, non l'API. I componenti di guscio (`AppSidebar`, `AppHeader`) restano dove li tiene l'host. `ScannerBar` e `VoiceModulator`, specifici del tema ma senza dominio, vivono in `components/shared/`.

| Componente | Forma K.I.T.T. |
|---|---|
| `BasePanel` | "Strumento incassato": fondo `--c-surface`, bordo `--c-panel-line`, `--kitt-radius`. Variante `hero`: filetto a segmenti in `--c-kitt-led-alert` in testa |
| `BaseButton`, link d'azione | Tasto rettangolare, `.stencil`, `--kitt-radius`, altezza minima `--hit-target-min`, bagliore all'hover. Colori dai token semantici (`federal`, `alert`), non dai LED |
| `BaseBadge` | Spia LED come contenuto di riserva dello slot `icon` (API invariata); stato sempre anche a parole |
| `HazardStripe` | Segmenti `--c-kitt-led-alert` separati da fessure `--kitt-gap`. Solo decorativa (`aria-hidden`): il pericolo è sempre detto anche a parole |
| `ProgressMeter` | Barra a segmenti LED con `mask`; indeterminata a scanner. Il valore è sempre anche in testo (`role="progressbar"`, `aria-valuenow`) |
| `ScannerBar` | Scanner rosso decorativo (effetto Larson): segmenti con `mask`, luce animata in `translateX` alternata 1.8 s. `aria-hidden` |
| `VoiceModulator` | Tre colonne di barre LED, `scaleY` animato. `aria-hidden` |
| Input, select | Spigoli `--kitt-radius`, bordo `--c-control-border`, fondo `--c-surface` |

**`mask` e bagliore.** `mask` taglia il `drop-shadow` applicato sullo stesso elemento: il bagliore (`filter: drop-shadow`) va sul **contenitore** dell'elemento mascherato, mai sull'elemento con la maschera.

**Movimento ridotto.** Ogni animazione ha il ripiego in `@media (prefers-reduced-motion: reduce)` (Regola 8 di `design-tokens`): `ScannerBar` ferma e accesa al centro, `VoiceModulator` statico con barre a metà altezza, `ProgressMeter` indeterminato come barra piena a metà senza movimento. Nei componenti il ripiego è esplicito, non basta l'azzeramento globale di `main.css`.

---

## Regola 5 — `theme-color` del browser

- `index.html`: due `<meta name="theme-color">`, uno per tema, con il valore di `--c-bg`: `media="(prefers-color-scheme: light)"` e `media="(prefers-color-scheme: dark)"`.
- `site.webmanifest` (se presente): `theme_color` e `background_color` uguali a `--c-bg` del tema chiaro.

---

## Regola 6 — Verifica

- Dopo ogni modifica di un colore: `node scripts/kitt-contrast.mjs` (installato dal pacchetto). Exit code diverso da 0 → contrasto insufficiente, il tema non si committa.
- I rapporti misurati si annotano in testa a `tokens.css` (Regola 4 di `design-tokens`).
- Lint, type-check, test e build restano quelli del progetto host. Controllo visivo a 375px e 1280px, tema chiaro e scuro, e con `prefers-reduced-motion: reduce`.

---

## ✅ Checklist pre-commit

- [ ] Nessun nome di colore (`red`, `amber`, …) nei componenti: solo `led-alert`, `led-primary`, `led-ok`, `led-caution`?
- [ ] Testo su LED solo con `--c-on-kitt`?
- [ ] Ogni animazione con ripiego `prefers-reduced-motion: reduce` che la ferma in uno stato significativo?
- [ ] `drop-shadow` sul contenitore dell'elemento con `mask`, mai sullo stesso elemento?
- [ ] Voce attiva con `aria-current`, spia "ON", peso e fondo pieno, non solo colore o bagliore?
- [ ] Barra mobile nascosta/mostrata con classi Tailwind, non con lo style scoped?
- [ ] API dei componenti condivisi invariata? Font installati solo dopo conferma?
- [ ] `theme-color` in `index.html` e `site.webmanifest` allineati a `--c-bg`?
- [ ] `node scripts/kitt-contrast.mjs` con exit code 0?

---

*Istruzione v1.0 - Tema K.I.T.T. - 2026-10-14 — claude-opus-5-5 — struttura estratta dalla prova su dr-mailroom-fe, branch feat/kitt-knight-rider (dr-fe#3)*
