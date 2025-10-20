# Propaedeia

Pipeline automatica Claude → Notion & Anki per trasformare sbobine mediche in materiale di studio ottimizzato.

## Overview

Propaedeia è un sistema completo per processare lezioni/sbobine di medicina e generare:
- **Pagine Notion** strutturate con callout clinici, diagrammi Mermaid, domande integrate
- **Deck Anki** (max 25 carte CORE) con anti-confusori automatici per spaced repetition
- **Proprietà database** estratte (Eziologia, Clinica, Diagnosi, Terapia)
- **Link automatici** tra argomenti correlati

Il workflow applica rigorosamente i **Criteri CCI** (Chiarezza Clinica Integrata): frasi ≤18 parole, main message first, numeri con unità, voce attiva.

## Architettura File

| File | Ruolo | Linee |
|------|-------|-------|
| **orchestrator.txt** | Motore workflow v4.4: scratchpad, oneshot, markdown semplice → Notion, MedGraph | ~780 |
| **istruzioni.txt** | Custom instructions: CCI, vincoli fonte, limiti cognitivi, validazione | ~154 |

## Workflow Completo (v4.4)

### BLOCCO 1: AUTOMATICO (~35-45 min)

| Fase | Nome | Output | Tempo |
|------|------|--------|-------|
| **1** | Traccia | Struttura H2/H3 in chat | 5-10 min |
| **2** | Pagina Markdown | **Markdown SEMPLICE completo in chat** (salvato in memoria) | 15-25 min |
| **3** | Elevator Pitch | Sintesi 170-200 parole in chat | 2-3 min |
| **4** | Anki CORE | **FILE anki_deck.txt generato** (max 25 carte) | 5-8 min |
| **5** | Callout | Lista 3-5 callout in chat, inseriti in pagina salvata | 2-3 min |
| **6** | Diagramma | Codice Mermaid in chat, inserito in pagina salvata | 2-3 min |
| **7** | Conversione + Pubblicazione | Markdown → Notion + update pagina + pitch | 1-2 min |

**Esecuzione automatica** senza pause fino a Fase 7

**[PAUSA OBBLIGATORIA]** Digita "continua" per Fase 8 o "ferma"

### BLOCCO 2: Proprietà (~3-5 min)

| Fase | Nome | Output | Tempo |
|------|------|--------|-------|
| **8** | Proprietà + DB Voci | Estrazione termini, batch processing, calcolo Complessità/Tempo | 3-5 min |

**[PAUSA OBBLIGATORIA]** Digita "continua" per Fase 9 (opzionale) o "ferma"

### BLOCCO 3: Link (~3-5 min, opzionale)

| Fase | Nome | Output | Tempo |
|------|------|--------|-------|
| **9** | Link Automatici | Ricerca argomenti correlati (score ≥2), creazione pagine collegamento | 3-5 min |

**Tempo totale**: 55-65 min

## Funzionalità Chiave (v4.4)

### 1. **Scratchpad System + Oneshot Automatico**
- Tracking stato workflow in memoria (fase corrente, outputs salvati, validation)
- Esecuzione automatica BLOCCO 1 (fasi 1-7) senza interruzioni
- Pause obbligatorie SOLO dopo Fase 7 e Fase 8
- Comando "status" per visibilità real-time
- Auto-avanzamento tra fasi fino a pubblicazione

### 2. **Markdown Semplice → Conversione Notion**
- Fase 2: genera pagina in markdown STANDARD (`## Titolo`, non `>## Titolo`)
- Output COMPLETO in chat (tutta la pagina visibile per validazione)
- CCI check REALI dopo ogni H2 (non simulati)
- Pagina salvata in memoria come markdown semplice
- Fase 7: conversione automatica markdown → formato Notion prima pubblicazione

### 3. **File Anki Generato (non output chat)**
- Fase 4: genera FILE `anki_deck.txt` nel progetto
- Max 25 carte CORE con singola c1
- Anti-confusori automatici (5 pattern)
- Comunicazione chat: solo "File anki_deck.txt generato (N carte)"

### 4. **CCI Enforcement**
- Validazione incrementale dopo ogni H2 generato
- Conteggi strict: max 18 parole/frase (threshold rigido, non media)
- Auto-check finale: se ≥2 fail → autocorreggi (max 1 iterazione)
- Pitch: validazione automatica 170-200 parole

### 5. **Anti-confusori Anki**
Pattern sistematici per evitare interferenza tra carte:
- **Età/popolazione**: "nel *neonato*" vs "nell'*adulto* >65 anni"
- **Temporalità**: "fase *acuta* (<72h)" vs "fase *cronica* (>3 mesi)"
- **Gravità/tipo**: "asma *intermittente*" vs "asma *persistente grave*"
- **Localizzazione**: "ictus *emisfero dominante*"
- **Contesto clinico**: "in *assenza di insufficienza renale*"

### 6. **Retry Logic & Cache**
- **Retry**: API timeout (3x), rate limit 429 (3x), server error 500+ (2x), network (3x)
- **Rate limiting**: 350ms tra chiamate consecutive, batch operations
- **Backoff**: esponenziale (1s, 2s, 4s)

### 7. **Database Unificato Voci**
- Property "Categoria" multi_select: ["Eziologia", "Clinica", "Diagnosi", "Terapia"]
- Relazione bidirezionale con Argomenti
- Batch processing Fase 8: search → create → update

## Output Generati

1. **Pagina Notion** (markdown Notion-ready):
   - H2/H3 con toggle ▶
   - Indentazione 2 spazi (livello 1 sotto H2), 4 spazi (livello 2 sotto H3)
   - 3-5 callout clinici con icone e colori
   - Diagramma Mermaid MedGraph (B/N + accento #00E0CC)
   - 5-7 domande cliniche integrate
   - Perle del professore (se in fonte)

2. **anki_deck.txt**:
   - Max 25 carte CORE high-yield
   - Formato: 1 carta/linea con {{c1::risposta}}
   - Anti-confusori applicati sistematicamente

3. **Proprietà Database**:
   - **Voci**: relazione a DB unificato (2-3 termini × 4 categorie)
   - **Pitch**: 170-200 parole
   - **Complessità**: Semplice/Media/Complessa (calcolo automatico)
   - **Tempo studio stimato**: minuti (calcolo: H2×2.5 + H3×1.5 + Callout×1 + Domande×0.5)

4. **Summary Finale**:
   - Metriche contenuto (parole, frasi, carte)
   - Proprietà & linking
   - Performance (tempo, API calls, cache hit rate)
   - Checklist qualità CCI

## Comandi Disponibili

```bash
workflow completo  # 9 fasi (~55-65 min) con pause obbligatorie
essenziale        # Solo fasi 1-3 (traccia, pagina, pitch)
continua          # Procedi a blocco successivo (dopo pause)
ferma             # Stop workflow
```

### Parametri Opzionali

Inserire PRIMA del comando:

```bash
n=30              # Override limite Anki CORE (default: max 25)
focus=diagnosi    # Restringe scope a sezione specifica
mode=comparativo  # Forza modalità confronto A vs B
skip_link=true    # Salta Fase 9 link automatico
```

**Esempio**:
```bash
n=30
focus=diagnosi differenziale
workflow completo
```

## Esempio Utilizzo

**Scenario**: Sbobina PDF "Insufficienza Cardiaca" allegata come unico file

```markdown
[AUTO-START] Workflow completo rilevato - Inizio elaborazione

[Fase 1] ✓ Traccia completata (5 H2)
[ONESHOT] Auto-avanzamento: Fase 2

[Fase 2] Generazione pagina: H2 3/5 completati
[Auto-check CCI H2.3] ✓ PASS
[Fase 2] ✓ Pagina completata (1850 parole)
[ONESHOT] Auto-avanzamento: Fase 3

[Fase 3] ✓ Callout inseriti: 4 (⚠️2 | 💡1 | ⭐1)
[ONESHOT] Auto-avanzamento: Fase 4

[Fase 4] ✓ Pitch completato (185 parole)
[ONESHOT] Auto-avanzamento: Fase 5

[Fase 5] ✓ Deck Anki generato: anki_deck.txt (23 carte CORE)
[ONESHOT] Auto-avanzamento: Fase 6

[Fase 6] ✓ Diagramma inserito: flowchart (10 nodi)
[ONESHOT] Auto-avanzamento: Fase 7

[Fase 7] ✓ Pagina pubblicata: https://notion.so/...

═══════════════════════════════════════════
    BLOCCO 1 COMPLETATO - PAUSA STRATEGICA
═══════════════════════════════════════════

✅ CONTENUTO PUBBLICATO:
├─ Pagina Notion: live e studiabile
├─ Pitch: inserito
└─ Anki: anki_deck.txt (23 carte)

⏸️  PROSSIMI STEP (opzionali):
├─ Fase 8: Proprietà cliniche + DB Voci (~3-5 min)
└─ Fase 9: Link automatici (~3-5 min)

Vuoi continuare? [continua | ferma | status]
```

**Utente**: `continua`

```markdown
[Fase 8] Termini processati: 12/12 (cached: 3, created: 5, found: 4)
[Fase 8] ✓ Proprietà aggiornate

[Fase 9] ✓ Trovati 3 collegamenti (score ≥2)
[Fase 9] ✓ Pagine link create: 3

[WORKFLOW COMPLETATO]

═══════════════════════════════════════════
    WORKFLOW COMPLETO - RIEPILOGO FINALE
═══════════════════════════════════════════

📊 METRICHE CONTENUTO
├─ Traccia H2: 5 pilastri
├─ Pagina Notion: 1850 parole totali
│  ├─ Frasi totali: 92
│  ├─ Frasi >18 parole: 5 (5.4%)
│  └─ Media parole/frase: 14.2
├─ Callout: 4 (⚠️2 | ⭐1 | 💡1)
├─ Domande: 6
├─ Elevator Pitch: 185 parole
└─ Anki CORE: 23 carte

🔗 PROPRIETÀ & LINKING
├─ Voci totali: 12 (Eziologia:3 | Clinica:3 | Diagnosi:3 | Terapia:3)
├─ Complessità: Media
├─ Tempo studio: 25 min
└─ Link automatici: 3 argomenti correlati

⏱️  PERFORMANCE
├─ Tempo totale: 58 min
├─ API calls: 34 (cached: 8, retry: 2)
└─ Termini cache hit rate: 25%

✅ CHECKLIST QUALITÀ CCI
├─ ✓ Main message in ogni H2
├─ ✓ Frasi ≤18 parole (94.6%)
├─ ✓ Numeri con unità
├─ ✓ Pitch 170-200 parole
├─ ✓ Anki ≤25 CORE, solo c1
└─ ✓ Pre-flight validation PASS

🔗 LINK NOTION
└─ https://notion.so/Insufficienza-cardiaca-...

═══════════════════════════════════════════
```

## Performance & Metriche

### Timing Medio

| Workflow | Tempo Totale | Fasi Incluse |
|----------|-------------|--------------|
| **Completo** (Blocco 1+2) | 55-65 min | 1-9 |
| **Completo** (solo Blocco 1) | 30-35 min | 1-7 |
| **Essenziale** | 25-30 min | 1,2,4,8 (no callout, anki, diagramma, link) |

### Ottimizzazioni v4.0

| Fase | Tempo v2.5 | Tempo v4.0 | Miglioramento |
|------|-----------|-----------|---------------|
| **Fase 3** (Callout) | 3-5 min | 1-2 min | **-60%** (str_replace vs lista) |
| **Fase 6** (Diagramma) | 3-5 min | 1-2 min | **-60%** (str_replace vs output chat) |
| **Fase 7** (Pubblicazione) | 3-5 min | 1 min | **-67%** (zero rigenerazione) |
| **Fase 8c** (Batch DB) | N/A | 3-5 min | **-80% API calls** (batch vs individuale) |

### API Calls

| Fase | Chiamate Tipiche | Note |
|------|------------------|------|
| Fase 7 (Upload) | 2-3 | search + update_content + update_properties |
| Fase 8 (Proprietà) | 12-20 | batch search (cached: -40%), batch create, update |
| Fase 9 (Link) | 5-10 | search candidati + match score + create collegamenti |

**Cache hit rate medio**: 20-30% (termini comuni come "TC", "Corticosteroidi", "Biopsia")

## Database Notion

### Argomenti

- **ID**: `1b5282519c2c80a68c37ce5e4bd56f22`
- **Data Source**: `collection://1b528251-9c2c-8065-a61e-000bfdfab7c7`

**Properties**:
- Nome (title)
- Pitch (text)
- Voci (relation → Voci, bidirezionale)
- Complessità (select): Semplice | Media | Complessa
- Tempo studio stimato (number)
- Tipo (select)
- Argomento primario (checkbox)
- Status argomento (select)
- Argomenti correlati (relation → Argomenti, per link automatici Fase 9)

### Voci (Unificato)

- **ID**: `290282519c2c801ea214d30b803c78f8`
- **Data Source**: `collection://29028251-9c2c-8024-bd71-000bcc303592`

**Properties**:
- Name (title)
- Categoria (multi_select): ["Eziologia", "Clinica", "Diagnosi", "Terapia"]
- Argomento (relation → Argomenti, bidirezionale)
- Note (text)
- Gruppo (self-relation)
- Sottogruppo (self-relation)

**Schema SQLite**:
```sql
"Categoria" TEXT  -- JSON array: ["Eziologia"], ["Clinica"], etc
"Argomento" TEXT  -- JSON array di URL: ["https://notion.so/..."]
```

## Limiti Cognitivi Standard

| Elemento | Range | Rationale |
|----------|-------|-----------|
| **Callout** | 3-5 | Working memory capacity |
| **Pilastri H2** | 4-6 | Optimal chunking |
| **Domande ❓** | 5-7 | Spaced retrieval |
| **Chiarimenti** | 3-5 | No overlap domande |
| **Anki CORE** | max 25 | High-yield essentials only |
| **Paragrafi** | 2-4 frasi | Readability |
| **Frasi** | ≤18 parole | Sentence complexity (threshold rigido) |
| **Pitch** | 170-200 parole | Elevator pitch standard (conteggio strict) |
| **Diagramma nodi** | ≤11 | Visual clarity |

## Criteri CCI (Chiarezza Clinica Integrata)

1. **Main message first**: ogni H2 apre con 1-2 frasi chiave
2. **Plain language**: frasi ≤18 parole (threshold rigido), paragrafi 2-4 frasi
3. **Numeri con unità**: ogni dato quantitativo ha unità esplicita ("5 giorni", NON "5")
4. **Voce attiva**: operazioni con verbi imperativi/infiniti
5. **Validazione incrementale**: auto-check dopo ogni H2 (se ≥2 fail → autocorreggi)
6. **Quick check finale**: conteggi automatici + checklist (se >20% frasi >18 → STOP)

## Changelog Versioni

### v4.4 (Fixed) - Corrente
- **RIPRISTINATO**: scratchpad system, oneshot automatico fino a Fase 7
- **FIX CRITICO**: Fase 2 genera markdown SEMPLICE (`## Titolo`, non `>## Titolo`)
- **FIX CRITICO**: Fase 2 output COMPLETO in chat (tutta pagina visibile)
- **FIX CRITICO**: Fase 4 genera FILE anki_deck.txt (non output chat)
- **FIX CRITICO**: Fase 7 conversione markdown → Notion + pubblicazione
- **MANTENUTO**: pause obbligatorie dopo Fase 7 e Fase 8
- **MANTENUTO**: indentazione 2 spazi (H2), 4 spazi (H3), NO TAB
- **OTTIMIZZATO**: 780 linee, workflow chiaro e funzionante

### v4.3 (Simplified) - Deprecata
- RIMOSSO scratchpad (ERRORE - era necessario)
- RIMOSSO oneshot (ERRORE - causava pause ad ogni fase)
- Fix indentazione e generazione reale (parziali)

### v4.2 (Production-Ready) - Deprecata
- AUTO-START intelligente
- Scratchpad + oneshot
- Formato Notion-ready in Fase 2 (ERRORE - complicato)
- Placeholders per callout/diagramma (ERRORE - non funzionavano)

### v4.1 (Database Unificato)
- 🗄️ **DB Voci unificato**: property "Categoria" multi_select sostituisce 4 relazioni separate
- 📦 **Batch processing**: Fase 8c ottimizzata (-80% API calls)
- 🚀 **Performance**: Fase 8 da ~8-12 min a ~3-5 min

### v4.0 (Scratchpad & Oneshot)
- 💾 **Scratchpad system**: tracking stato workflow, prerequisiti, outputs salvati
- ⚡ **Oneshot mode**: esecuzione automatica senza conferme (default)
- 🔄 **Zero rigenerazione**: str_replace workflow (Fase 2 → 3 → 6 → 7)
- 📊 **Summary finale**: metriche, performance, checklist CCI
- ⏱️ **Performance**: workflow da ~70 min a ~55-60 min

### v3.0 (Output Ottimizzato)
- 📝 **Anki .txt file**: output 1 carta/linea, zero chat output
- 🎯 **CORE-only**: max 25 carte (da 35-50), TIER 2 rimosso
- 🛡️ **Anti-confusori**: 5 pattern sistematici automatici
- ✅ **CCI enforcement**: validazione incrementale strict
- ♻️ **Retry logic**: API timeout, rate limit, network errors
- 💾 **Cache termini**: dizionario sessione (-40% chiamate duplicate)

### v2.5 (Prima Integrazione Notion)
- 🔗 **API Notion**: pubblicazione automatica database Argomenti
- 🏷️ **Proprietà DB**: Eziologia, Clinica, Diagnosi, Terapia (4 relazioni separate)
- ⏱️ **Tempo workflow**: ~70 min (workflow manuale con conferme)

### v1.0 (MVP)
- 📄 **Generazione base**: Pagina Notion markdown
- 📚 **Anki basic**: ~50 carte miste (CORE + NON-CORE)
- ⚙️ **Workflow manuale**: conferme utente obbligatorie tra fasi

---

## Note Tecniche

### Formato Relazioni Notion (CRITICAL)

✅ **CORRETTO** (quando scrivi):
```json
"Voci": "[\"https://notion.so/page1\", \"https://notion.so/page2\"]"
```

❌ **ERRATO**:
```json
"Voci": ["https://notion.so/page1"]  // Array, non stringa JSON
"Voci": ["{{https://notion.so/page1}}"]  // Doppie graffe (solo in output Notion quando leggi)
```

### Indentazione Notion

- **1 livello sotto H2**: 2 spazi iniziali
- **2 livelli sotto H3**: 4 spazi iniziali
- **NO TAB** (incompatibile con Notion import API)

### Callout Notion

Formato con indentazione 2 spazi:
```markdown
  <callout icon="/icons/warning_red.svg" color="red_bg">
  <span color="red">Testo avvertenza</span>
  </callout>
```

### MedGraph Palette

- Sfondo: #FFFFFF
- Testo/linee: #000000
- **Un solo accento**: #00E0CC (step critico, mai decorativo)
- Contrasto: testo ≥4.5:1, connettori ≥3:1
- Leggibile in scala di grigi (stampa-ready)

---

**Ultimo aggiornamento**: v4.4 Fixed (2025)
**Piattaforma**: Claude Web Projects (browser)
**Dipendenze**: API Notion (custom integration), Mermaid.js (diagrammi)
