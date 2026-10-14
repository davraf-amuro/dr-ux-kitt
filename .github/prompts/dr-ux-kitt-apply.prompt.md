---
agent: 'agent'
description: 'Applica il tema K.I.T.T. (cruscotto Knight Rider, tema notte e garage diurno) a un frontend Vue 3 + Tailwind v4'
tools: ['search/codebase', 'edit/editFiles', 'runCommands']
---

# Prompt: applica il tema K.I.T.T.

Applica il tema K.I.T.T. al frontend Vue del progetto corrente: token "notte" e "garage diurno", tipografia, guscio a console e componenti condivisi e di tema.

Le regole del tema stanno in `.github/instructions/kitt-theme.instructions.md`; il contratto dei token e la Regola 8 (animazioni, dipendenze, font) in `.github/instructions/design-tokens.instructions.md` (pacchetto `dr-fe`). Leggile entrambe per intero prima di scrivere: questo prompt dice **cosa** applicare e **in che ordine**, non ripete le regole.

## Regola d'oro

**Mai sovrascrivere in silenzio un file dell'host.** Per ogni file che esiste già: mostra il diff proposto, spiega cosa cambia, chiedi conferma. Nei componenti esistenti si conservano `<script>`, props, slot, eventi e logica dell'host: cambiano solo template (classi) e `<style>`. I sorgenti qui sotto sono **riferimenti**, non file da incollare a occhi chiusi.

## Fase 1 — Prerequisiti (fermati se uno manca)

1. `.ai/dr-guidelines-packages.json` elenca `dr-fe` e `dr-ux-kitt`. Se manca `dr-fe` → fermati: senza contratto dei token il tema non ha regole.
2. `package.json` contiene `vue` (3.x) e `tailwindcss` 4.x (con `@tailwindcss/vite`). Altrimenti → fermati e dichiara che il tema non si applica a questo stack.
3. Individua i file. Percorsi attesi (struttura di `frontend-organization.instructions.md`):
   - `src/assets/styles/tokens.css`, `src/assets/styles/main.css`
   - `src/App.vue`, `AppSidebar.vue` e `AppHeader.vue` (nella cartella del guscio dell'host)
   - `src/components/shared/BasePanel.vue`, `BaseButton.vue`, `BaseBadge.vue`, `HazardStripe.vue`, `ProgressMeter.vue` (se esistono)
   - `index.html`, `public/site.webmanifest`
   Percorso diverso o componente con altro nome → chiedi all'utente quale file corrisponde; non indovinare.

## Fase 2 — Applicazione

Un passo alla volta, con conferma per ogni file esistente.

1. **`tokens.css`** — sostituisci con i token di "Riferimento — `tokens.css`". Token propri dell'host non presenti qui (es. `--panel-bevel` di un tema precedente): elencali e chiedi se rimuoverli; se un componente li usa ancora, segnalalo.
2. **Font** — se mancano `@fontsource-variable/saira` o `@fontsource-variable/jetbrains-mono` in `package.json`, proponi `npm install @fontsource-variable/saira @fontsource-variable/jetbrains-mono` e **attendi conferma esplicita**: sono le uniche dipendenze consentite al tema (Regola 8 di `design-tokens`). Senza conferma, usa i font di sistema e dichiaralo.
3. **`main.css`** — integra "Riferimento — `main.css`": import dei font, `@custom-variant dark`, alias `@theme inline`, `h1`–`h3`, `.font-console`, `.stencil`, focus unico e focus ambra sulla console, `prefers-reduced-motion`. Mantieni gli import e le regole proprie dell'host che non confliggono.
4. **Componenti di tema** — crea `ScannerBar.vue` e `VoiceModulator.vue` in `src/components/shared/` dai riferimenti (file nuovi: nessun diff da confermare, ma elenca cosa crei).
5. **Guscio** — `App.vue`, `AppSidebar.vue`, `AppHeader.vue` dai riferimenti. Le voci di navigazione, il marchio e il contenuto di stato restano quelli dell'host: dai riferimenti prendi struttura, classi e stile. `VoiceModulator` si monta dove l'host vuole un indicatore di attività (per esempio nella testata); chiedi dove.
6. **Condivisi** — `BasePanel`, `BaseButton`, `BaseBadge`, `HazardStripe`, `ProgressMeter` dai riferimenti, API dell'host invariata. Poi cerca `<input`, `<select`, `<textarea` e i link d'azione: proponi l'elenco dei punti da portare agli spigoli `--kitt-radius` e applica dopo conferma.
7. **`theme-color`** — in `index.html` due `<meta name="theme-color">` con `--c-bg` chiaro e scuro (tabella in fondo); in `site.webmanifest`, se esiste, `theme_color` e `background_color` uguali a `--c-bg` chiaro.

## Fase 3 — Verifica

1. Formatta i file toccati con il formatter dell'host (es. `prettier --write`): i riferimenti seguono una configurazione comune, non quella del progetto.
2. `node scripts/kitt-contrast.mjs` (percorso diverso di `tokens.css` → passalo come argomento). Exit code diverso da 0 → mostra le coppie `FAIL` e fermati: non si committa un tema sotto soglia. Annota i rapporti in testa a `tokens.css` con la data.
3. Comandi di verifica dell'host dichiarati dalla sua istruzione di dominio (lint senza `--fix`, format, type-check, test, build). Tutti verdi.
4. Chiedi all'utente un controllo visivo a 375px e 1280px, tema chiaro e scuro, anche con `prefers-reduced-motion: reduce`: scanner fermo e acceso al centro, modulatore statico, voce attiva riconoscibile in scala di grigi, anello di focus ambra visibile sui tasti della console.

## Output

- File modificati e creati, con una riga di motivo ciascuno
- Esito dello script contrasti (numero di coppie, eventuali `FAIL`)
- Esito dei comandi di verifica
- Font installati (con la conferma ottenuta) o non installati
- Token o file dell'host lasciati intatti e perché

---

## Riferimento — `tokens.css`

```css
/*
 * Tema K.I.T.T. (cruscotto Knight Rider): "garage diurno" (chiaro) e "notte" (scuro), console
 * scura in entrambi. Struttura e ruoli: kitt-theme.instructions.md.
 * Contrasti (WCAG 2.x) verificati con scripts/kitt-contrast.mjs il <data>: testo >= 4.5:1,
 * bordi dei controlli e focus >= 3:1, in chiaro e in scuro, LED e console compresi.
 * --c-caution non si usa mai come colore del testo: solo fondo, con --c-on-caution sopra.
 * Sopra i LED --c-kitt-led-* si scrive solo con --c-on-kitt.
 */

:root {
  color-scheme: light;

  /* Tema chiaro "garage diurno". */
  --c-bg: #e6e7ea;
  --c-surface: #f7f7f8;
  --c-panel-line: #c9cbd0;
  --c-control-border: #6b6d74;
  --c-ink: #111214;
  --c-ink-muted: #46484e;
  --c-federal: #8f3f00;
  --c-on-federal: #ffffff;
  --c-alert: #b3261e;
  --c-on-alert: #ffffff;
  --c-caution: #ffb21e;
  --c-on-caution: #111214;
  --c-ok: #1d6a32;
  --c-on-ok: #ffffff;
  --c-neutral: #5a5c63;

  /* Console: scura in entrambi i temi. */
  --c-console: #0b0b0d;
  --c-console-raised: #19191c;
  --c-console-line: #2a2a2e;
  --c-console-ink: #f2f2f0;

  /* LED decorativi per ruolo, uguali nei due temi; il testo sopra è sempre --c-on-kitt. */
  --c-kitt-led-alert: #ff2a1a;
  --c-kitt-led-primary: #ffb21e;
  --c-kitt-led-ok: #3ddc6a;
  --c-kitt-led-caution: #ffe14d;
  --c-on-kitt: #000000;

  --focus-ring-width: 3px;
  --focus-ring-offset: 2px;
  --hit-target-min: 44px;
  --stencil-tracking: 0.12em;

  /* Geometria K.I.T.T.: barre, fessure tra segmenti, spigoli. */
  --kitt-bar: 0.875rem;
  --kitt-gap: 3px;
  --kitt-radius: 4px;
}

/* Tema scuro "notte": scelta esplicita (useTheme) oppure preferenza di sistema. */
:root[data-theme='dark'] {
  color-scheme: dark;

  --c-bg: #050505;
  --c-surface: #121214;
  --c-panel-line: #2a2a2e;
  --c-control-border: #7a7a82;
  --c-ink: #f2f2f0;
  --c-ink-muted: #b4b4b8;
  --c-federal: #ffb21e;
  --c-on-federal: #000000;
  --c-alert: #ff4d3d;
  --c-on-alert: #000000;
  --c-caution: #ffe14d;
  --c-on-caution: #000000;
  --c-ok: #3ddc6a;
  --c-on-ok: #000000;
  --c-neutral: #9a9aa2;
}

@media (prefers-color-scheme: dark) {
  :root:not([data-theme]) {
    color-scheme: dark;

    --c-bg: #050505;
    --c-surface: #121214;
    --c-panel-line: #2a2a2e;
    --c-control-border: #7a7a82;
    --c-ink: #f2f2f0;
    --c-ink-muted: #b4b4b8;
    --c-federal: #ffb21e;
    --c-on-federal: #000000;
    --c-alert: #ff4d3d;
    --c-on-alert: #000000;
    --c-caution: #ffe14d;
    --c-on-caution: #000000;
    --c-ok: #3ddc6a;
    --c-on-ok: #000000;
    --c-neutral: #9a9aa2;
  }
}
```

Sostituisci `<data>` con la data della verifica di Fase 3, passo 2.

## Riferimento — `main.css`

```css
@import '@fontsource-variable/saira/wdth.css';
@import '@fontsource-variable/saira/wdth-italic.css';
@import '@fontsource-variable/jetbrains-mono';
@import 'tailwindcss';
@import './tokens.css';

/* La variante dark segue l'attributo impostato da useTheme, non solo la preferenza di sistema. */
@custom-variant dark (&:where([data-theme='dark'], [data-theme='dark'] *));

/* Token esposti a Tailwind: bg-surface, text-ink, bg-console, bg-kitt-led-primary, text-on-kitt… */
@theme inline {
  --color-bg: var(--c-bg);
  --color-surface: var(--c-surface);
  --color-panel-line: var(--c-panel-line);
  --color-control-border: var(--c-control-border);
  --color-ink: var(--c-ink);
  --color-ink-muted: var(--c-ink-muted);
  --color-federal: var(--c-federal);
  --color-on-federal: var(--c-on-federal);
  --color-alert: var(--c-alert);
  --color-on-alert: var(--c-on-alert);
  --color-caution: var(--c-caution);
  --color-on-caution: var(--c-on-caution);
  --color-ok: var(--c-ok);
  --color-on-ok: var(--c-on-ok);
  --color-neutral: var(--c-neutral);
  --color-console: var(--c-console);
  --color-console-raised: var(--c-console-raised);
  --color-console-line: var(--c-console-line);
  --color-console-ink: var(--c-console-ink);
  --color-kitt-led-alert: var(--c-kitt-led-alert);
  --color-kitt-led-primary: var(--c-kitt-led-primary);
  --color-kitt-led-ok: var(--c-kitt-led-ok);
  --color-kitt-led-caution: var(--c-kitt-led-caution);
  --color-on-kitt: var(--c-on-kitt);

  --font-sans: 'Saira Variable', system-ui, sans-serif;
  --font-display: 'Saira Variable', system-ui, sans-serif;
  --font-mono: 'JetBrains Mono Variable', ui-monospace, 'Cascadia Mono', Consolas, monospace;
}

@layer base {
  html {
    background-color: var(--c-bg);
    color: var(--c-ink);
    font-family: var(--font-sans);
    font-stretch: 100%;
  }

  body {
    min-height: 100vh;
    line-height: 1.5;
  }

  /* Anello di focus unico per tutti gli elementi interattivi. */
  :focus-visible {
    outline: var(--focus-ring-width) solid var(--c-federal);
    outline-offset: var(--focus-ring-offset);
  }

  /* Sul fondo console (scuro in entrambi i temi) il focus è ambra. */
  .on-console :focus-visible,
  .on-console:focus-visible {
    outline-color: var(--c-kitt-led-primary);
  }

  /* Lettering K.I.T.T.: Saira larga, corsiva, maiuscola, solo sui titoli. */
  h1,
  h2,
  h3 {
    font-family: var(--font-display);
    font-stretch: 125%;
    font-style: italic;
    font-weight: 700;
    line-height: 1.1;
    text-transform: uppercase;
  }
}

@layer utilities {
  /* Letture numeriche: JetBrains Mono a cifre tabulari. */
  .font-console {
    font-family: var(--font-mono);
    font-variant-numeric: tabular-nums;
  }

  /* Sopratitoli, etichette e tasti: maiuscolo spaziato, largo. */
  .stencil {
    font-stretch: 112.5%;
    text-transform: uppercase;
    letter-spacing: var(--stencil-tracking);
  }
}

/* Collegamento "Vai al contenuto": fuori schermo finché non riceve il focus da tastiera. */
.skip-link {
  position: fixed;
  top: 0.5rem;
  left: 0.5rem;
  z-index: 60;
  transform: translateY(-200%);
}

.skip-link:focus {
  transform: none;
}

/*
 * Azzeramento globale (Regola 5 di design-tokens). Le animazioni decorative hanno in più il
 * proprio ripiego nel componente (Regola 8): stato fermo e significativo, non un fotogramma a caso.
 */
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

Se l'host usa già un altro font monospaziato, tienilo in `--font-mono`.

## Riferimento — `ScannerBar.vue`

Decorativo (`aria-hidden`). La maschera ritaglia i segmenti; il bagliore sta sul contenitore, perché `mask` taglierebbe un `drop-shadow` sullo stesso elemento. La maschera usa solo il canale alfa: il valore `--c-on-kitt` serve soltanto a non scrivere un colore letterale.

```vue
<template>
  <div class="scanner-glow" aria-hidden="true">
    <div class="scanner">
      <span class="scanner-light" />
    </div>
  </div>
</template>

<style scoped>
.scanner-glow {
  filter: drop-shadow(0 0 6px var(--c-kitt-led-alert));
}

.scanner {
  position: relative;
  height: var(--kitt-bar);
  overflow: hidden;
  background: color-mix(in srgb, var(--c-kitt-led-alert) 22%, var(--c-console));
  border-radius: var(--kitt-radius);
  mask-image: repeating-linear-gradient(
    90deg,
    var(--c-on-kitt) 0 8px,
    transparent 8px calc(8px + var(--kitt-gap))
  );
}

.scanner-light {
  position: absolute;
  inset-block: 0;
  left: 0;
  width: 28%;
  background: linear-gradient(
    90deg,
    transparent,
    var(--c-kitt-led-alert) 50%,
    transparent
  );
  animation: kitt-scan 1.8s ease-in-out infinite alternate;
}

/* Corsa completa: 100% / 28% - 100% = 257% della larghezza della luce. */
@keyframes kitt-scan {
  from {
    transform: translateX(0);
  }

  to {
    transform: translateX(257%);
  }
}

/* Movimento ridotto: la luce resta ferma, accesa al centro. */
@media (prefers-reduced-motion: reduce) {
  .scanner-light {
    animation: none;
    transform: translateX(128.5%);
  }
}
</style>
```

## Riferimento — `VoiceModulator.vue`

Decorativo (`aria-hidden`). Tre colonne di barre LED; il bagliore sta sul contenitore, la maschera sulle colonne.

```vue
<script setup lang="ts">
const COLUMNS = [0, 1, 2] as const
</script>

<template>
  <div class="modulator" aria-hidden="true">
    <span v-for="column in COLUMNS" :key="column" class="column" :class="`column-${column}`">
      <span class="fill" />
    </span>
  </div>
</template>

<style scoped>
.modulator {
  display: inline-flex;
  align-items: stretch;
  gap: var(--kitt-gap);
  height: 1.75rem;
  filter: drop-shadow(0 0 5px var(--c-kitt-led-primary));
}

.column {
  display: block;
  width: 0.5rem;
  overflow: hidden;
  background: color-mix(in srgb, var(--c-kitt-led-primary) 20%, var(--c-console));
  mask-image: repeating-linear-gradient(
    0deg,
    var(--c-on-kitt) 0 3px,
    transparent 3px calc(3px + var(--kitt-gap))
  );
}

.fill {
  display: block;
  height: 100%;
  background: var(--c-kitt-led-primary);
  transform-origin: bottom;
  transform: scaleY(0.5);
  animation: kitt-voice 0.9s ease-in-out infinite alternate;
}

.column-1 .fill {
  animation-duration: 0.6s;
  animation-delay: -0.3s;
}

.column-2 .fill {
  animation-duration: 1.1s;
  animation-delay: -0.6s;
}

@keyframes kitt-voice {
  from {
    transform: scaleY(0.15);
  }

  to {
    transform: scaleY(1);
  }
}

/* Movimento ridotto: statico, colonne a metà altezza. */
@media (prefers-reduced-motion: reduce) {
  .fill {
    animation: none;
    transform: scaleY(0.5);
  }
}
</style>
```

## Riferimento — `App.vue` (solo il guscio)

Conserva provider, transizioni e logica dell'host; i percorsi `@/components/shell/` degli import sono di esempio (usa la cartella del guscio dell'host); il riferimento mostra griglia, link "Vai al contenuto" e spazio per la console mobile.

```vue
<script setup lang="ts">
import { RouterView } from 'vue-router'

import AppHeader from '@/components/shell/AppHeader.vue'
import AppSidebar from '@/components/shell/AppSidebar.vue'

const MAIN_CONTENT_ID = 'main-content'
</script>

<template>
  <a
    :href="`#${MAIN_CONTENT_ID}`"
    class="skip-link bg-federal px-4 py-2 font-semibold text-on-federal"
  >
    Vai al contenuto
  </a>
  <div class="min-h-screen md:grid md:grid-cols-[15rem_1fr]">
    <AppSidebar />
    <div class="flex min-w-0 flex-col">
      <AppHeader>
        <template #brand>
          <!-- Marchio dell'host -->
        </template>
        <!-- Contenuto di stato dell'host (banner, avvisi) -->
      </AppHeader>
      <main
        :id="MAIN_CONTENT_ID"
        tabindex="-1"
        class="app-main flex-1 px-4 pt-4 pb-24 md:px-6 md:pb-6"
      >
        <RouterView />
      </main>
    </div>
  </div>
</template>

<style scoped>
.app-main:focus {
  outline: none;
}
</style>
```

## Riferimento — `AppSidebar.vue`

Le voci (`links`) restano quelle dell'host, con la loro sorgente (route, meta, costanti). Il LED di ogni tasto si assegna per indice tramite `--key-led`: nessun colore letterale nel componente. La spia "ON" è testo generato da CSS e nascosto agli assistivi (il link attivo dichiara già `aria-current`).

```vue
<script setup lang="ts">
import { type Component } from 'vue'
import { RouterLink, type RouteLocationRaw } from 'vue-router'

interface NavLink {
  to: RouteLocationRaw
  label: string
  icon: Component
}

// Voci dell'host: mantieni la sorgente esistente, qui serve solo la forma.
const links: NavLink[] = []

/** LED dei tasti, per indice: oltre la quarta voce si ricomincia. */
const LEDS = [
  '--c-kitt-led-primary',
  '--c-kitt-led-ok',
  '--c-kitt-led-caution',
  '--c-kitt-led-alert',
] as const
const ledOf = (index: number) => `var(${LEDS[index % LEDS.length]})`
</script>

<template>
  <nav
    aria-label="Navigazione principale"
    class="on-console fixed inset-x-0 bottom-0 z-30 bg-console p-1 text-console-ink md:sticky md:top-0 md:flex md:h-screen md:flex-col md:gap-(--kitt-gap) md:p-2"
  >
    <ul class="grid auto-cols-fr grid-flow-col gap-(--kitt-gap) md:flex md:flex-col">
      <li v-for="(link, index) in links" :key="link.label">
        <RouterLink
          :to="link.to"
          class="key stencil flex min-h-14 flex-col items-center justify-center gap-1 px-2 text-xs font-semibold max-md:[font-stretch:75%] md:min-h-12 md:flex-row md:justify-start md:gap-3 md:px-4 md:text-base"
          :style="{ '--key-led': ledOf(index) }"
        >
          <component :is="link.icon" :size="20" :stroke-width="1.75" aria-hidden="true" />
          <span>{{ link.label }}</span>
        </RouterLink>
      </li>
    </ul>
  </nav>
</template>

<style scoped>
/* Tasto retroilluminato: il LED della voce è la barra sul bordo (sotto, su mobile). */
.key {
  background: var(--c-console-raised);
  color: var(--c-console-ink);
  border-radius: var(--kitt-radius);
  box-shadow: inset 0 -3px 0 var(--key-led);
  transition:
    box-shadow 0.15s ease,
    filter 0.15s ease;
}

@media (min-width: 48rem) {
  .key {
    box-shadow: inset 3px 0 0 var(--key-led);
  }
}

.key:hover {
  filter: brightness(1.25);
  box-shadow: 0 0 10px var(--key-led);
}

/*
 * Voce attiva: fondo pieno nel LED, testo nero, peso maggiore, spia "ON" e bagliore.
 * Non è il solo colore: restano fondo pieno, peso e testo "ON", leggibili anche in grigi.
 */
.key[aria-current='page'] {
  background: var(--key-led);
  color: var(--c-on-kitt);
  font-weight: 800;
  box-shadow: 0 0 12px var(--key-led);
}

.key[aria-current='page']::after {
  content: 'ON' / '';
  margin-left: auto;
  padding: 0 0.375rem;
  font-size: 0.6875rem;
  font-weight: 800;
  letter-spacing: 0.1em;
  border: 1px solid currentColor;
  border-radius: var(--kitt-radius);
}

/* Su mobile il tasto è in colonna: la spia va sotto l'etichetta, senza margine automatico. */
@media (max-width: 47.99rem) {
  .key[aria-current='page']::after {
    margin-left: 0;
  }
}

/* Movimento ridotto: nessuna transizione, il tasto cambia stato di colpo. */
@media (prefers-reduced-motion: reduce) {
  .key {
    transition: none;
  }
}
</style>
```

## Riferimento — `AppHeader.vue`

Il pulsante del tema usa il composable dell'host (`useTheme` di `composables/shared/`, oppure lo store `ui` se l'host lo incapsula lì). Le icone sono quelle già usate dall'host. `VoiceModulator` si aggiunge solo dove l'utente lo chiede.

```vue
<script setup lang="ts">
import ScannerBar from '@/components/shared/ScannerBar.vue'
import { useTheme } from '@/composables/shared/useTheme'

const { isDark, toggleTheme } = useTheme()
</script>

<template>
  <header class="on-console bg-console text-console-ink">
    <div class="flex items-center gap-4 px-4 py-3 md:px-8">
      <div class="shrink-0">
        <slot name="brand" />
      </div>
      <div class="hidden w-40 shrink-0 md:block">
        <ScannerBar />
      </div>
      <div class="min-w-0 flex-1">
        <slot />
      </div>
      <button
        type="button"
        class="stencil inline-flex min-h-11 min-w-11 shrink-0 items-center justify-center rounded-(--kitt-radius) bg-console-raised px-3 text-console-ink hover:brightness-125 motion-reduce:transition-none"
        :aria-pressed="isDark"
        aria-label="Tema scuro"
        :title="isDark ? 'Passa al tema chiaro' : 'Passa al tema scuro'"
        @click="toggleTheme()"
      >
        <!-- Icona sole/luna dell'host, 20px, aria-hidden -->
      </button>
    </div>
    <!-- Su mobile lo scanner occupa il bordo inferiore della testata -->
    <div class="px-4 pb-3 md:hidden">
      <ScannerBar />
    </div>
  </header>
</template>
```

## Riferimento — `BasePanel.vue`

```vue
<script setup lang="ts">
withDefaults(
  defineProps<{
    /** Elemento radice: section per i pannelli con titolo, div per i contenitori. */
    as?: 'section' | 'div' | 'article'
    /** Cella "eroe" della vista: filetto a segmenti LED rossi in testa. */
    hero?: boolean
  }>(),
  { as: 'div', hero: false },
)
</script>

<template>
  <!-- Strumento incassato: fondo scuro del pannello, bordo sottile, filetto LED nella variante eroe -->
  <component :is="as" class="kitt-panel bg-surface" :class="{ 'kitt-panel--hero': hero }">
    <div v-if="hero" class="hero-rule" aria-hidden="true" />
    <div class="panel-body p-4">
      <slot />
    </div>
  </component>
</template>

<style scoped>
.kitt-panel {
  overflow: hidden;
  border: 1px solid var(--c-panel-line);
  border-radius: var(--kitt-radius);
  box-shadow: inset 0 1px 0 var(--c-panel-line);
}

.hero-rule {
  height: 0.375rem;
  background: repeating-linear-gradient(
    90deg,
    var(--c-kitt-led-alert) 0 12px,
    transparent 12px calc(12px + var(--kitt-gap))
  );
}

.panel-body {
  height: 100%;
}
</style>
```

## Riferimento — `BaseButton.vue` e `BaseBadge.vue`

Varianti e props restano quelle dell'host. Classi della radice di `BaseButton` (il ripiego del movimento ridotto è `motion-reduce:transition-none`):

```text
BaseButton: stencil inline-flex min-h-11 items-center justify-center gap-2 rounded-(--kitt-radius) border px-5 font-semibold
            transition-shadow hover:shadow-[0_0_10px_var(--c-federal)] motion-reduce:transition-none
            disabled:cursor-not-allowed disabled:opacity-60
            primary   → bg-federal text-on-federal border-federal
            secondary → bg-surface text-federal border-control-border
            danger    → bg-alert text-on-alert border-alert hover:shadow-[0_0_10px_var(--c-alert)]
            ghost     → bg-transparent text-federal border-transparent
```

`BaseBadge`: la spia LED è il contenuto di riserva dello slot `icon`, quindi l'API non cambia. Lo stato resta sempre anche a parole (l'etichetta del badge).

```vue
<template>
  <span
    class="inline-flex items-center gap-1.5 rounded-(--kitt-radius) border px-2.5 py-0.5 text-sm font-semibold whitespace-nowrap"
  >
    <slot name="icon">
      <!-- Spia LED: prende il colore del testo del badge (currentColor), nessun colore letterale -->
      <span class="size-2 shrink-0 rounded-full bg-current" aria-hidden="true" />
    </slot>
    <slot />
  </span>
</template>
```

```text
BaseBadge varianti (classi sulla radice):
            ok → border-ok text-ok · alert → border-alert text-alert · neutral → border-neutral text-neutral
            federal → border-federal text-federal · caution → border-caution bg-caution text-on-caution
```

## Riferimento — `HazardStripe.vue` (solo stile)

```css
/* Segmenti LED rossi separati da fessure del colore di fondo. Solo decorativa (aria-hidden). */
.hazard-stripe {
  width: 100%;
  height: var(--kitt-bar);
  background: repeating-linear-gradient(
    90deg,
    var(--c-kitt-led-alert) 0 20px,
    var(--c-bg) 20px calc(20px + var(--kitt-gap))
  );
}
```

## Riferimento — `ProgressMeter.vue`

Barra a segmenti LED. Il valore è anche in testo e in `role="progressbar"`; la modalità indeterminata ha `aria-busy` e una scritta. Props di esempio: adatta ai nomi dell'host, l'API dell'host resta invariata.

```vue
<script setup lang="ts">
import { computed } from 'vue'

const props = withDefaults(
  defineProps<{
    /** Valore 0-100; `null` = indeterminato. */
    value?: number | null
    label: string
  }>(),
  { value: null },
)

const percent = computed(() =>
  props.value === null ? null : Math.min(100, Math.max(0, Math.round(props.value))),
)
</script>

<template>
  <div class="meter-wrap">
    <div
      class="meter-glow"
      role="progressbar"
      :aria-label="label"
      :aria-valuemin="0"
      :aria-valuemax="100"
      :aria-valuenow="percent ?? undefined"
      :aria-busy="percent === null"
    >
      <div class="meter">
        <span
          v-if="percent !== null"
          class="meter-fill"
          :style="{ width: `${percent}%` }"
        />
        <span v-else class="meter-light" />
      </div>
    </div>
    <span class="font-console text-sm text-ink-muted">
      {{ percent === null ? 'In corso…' : `${percent}%` }}
    </span>
  </div>
</template>

<style scoped>
.meter-wrap {
  display: flex;
  align-items: center;
  gap: 0.75rem;
}

/* Bagliore sul contenitore: la maschera taglierebbe un drop-shadow sull'elemento mascherato. */
.meter-glow {
  flex: 1;
  filter: drop-shadow(0 0 4px var(--c-kitt-led-primary));
}

.meter {
  position: relative;
  height: var(--kitt-bar);
  overflow: hidden;
  background: color-mix(in srgb, var(--c-kitt-led-primary) 20%, var(--c-console));
  border-radius: var(--kitt-radius);
  mask-image: repeating-linear-gradient(
    90deg,
    var(--c-on-kitt) 0 8px,
    transparent 8px calc(8px + var(--kitt-gap))
  );
}

.meter-fill {
  position: absolute;
  inset-block: 0;
  left: 0;
  background: var(--c-kitt-led-primary);
}

.meter-light {
  position: absolute;
  inset-block: 0;
  left: 0;
  width: 25%;
  background: linear-gradient(90deg, transparent, var(--c-kitt-led-primary) 50%, transparent);
  animation: kitt-meter 1.8s ease-in-out infinite alternate;
}

/* Corsa completa: 100% / 25% - 100% = 300% della larghezza della luce. */
@keyframes kitt-meter {
  from {
    transform: translateX(0);
  }

  to {
    transform: translateX(300%);
  }
}

/* Movimento ridotto: indeterminato = barra ferma, piena a metà, con la scritta "In corso…". */
@media (prefers-reduced-motion: reduce) {
  .meter-light {
    animation: none;
    left: 0;
    width: 50%;
    background: var(--c-kitt-led-primary);
    transform: none;
  }
}
</style>
```

---

## Temi e `theme-color`

| Tema | `theme-color` | Nome |
|---|---|---|
| Chiaro | `#e6e7ea` | garage diurno |
| Scuro | `#050505` | notte |

---

*Prompt v1.0 - Applica tema K.I.T.T. - 2026-10-14 — claude-opus-5-5 — token dalla prova su dr-mailroom-fe, branch feat/kitt-knight-rider; sorgenti dei componenti riscritti e verificati in un progetto di prova (dr-fe#3)*
