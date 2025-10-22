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
| **orchestrator.txt** | Motore workflow v4.7: 3 gruppi (A/B/C), integrazione multi-documento, output minimale | ~1180 |
| **istruzioni.txt** | Custom instructions: CCI, vincoli fonte, limiti cognitivi, validazione | ~154 |

## Workflow Completo (v4.7)

### Gruppo A - Contenuto Notion (~35-50 min)

Esecuzione automatica fasi 0-5

| Fase | Nome | Output | Tempo |
|------|------|--------|-------|
| **0** | Analisi fonti | Piano integrazione (SOLO se multi-doc) | 3-5 min |
| **1** | Traccia | Struttura H2/H3 in chat (integrata se multi-doc) | 5-10 min |
| **2** | Pagina + Callout | Generazione diretta su Notion (5-7 callout automatici, CCI interno) | 15-25 min |
| **3** | Diagramma | Inserito su Notion (append blocks) | 2-3 min |
| **4** | Pitch + Status | Property Pitch con grassetto + Status "Attivo" | 2-3 min |
| **5** | Complessità + Tempo | Calcolo automatico e update properties | 1-2 min |

**Output finale**: Contenuto studiabile completo su Notion (integrato da fonti multiple se applicabile)

**[Pausa A]** - Digita 'continua' per Anki o 'ferma'

---

### Gruppo B - Anki (~5-8 min)

Esecuzione dopo "continua"

| Fase | Nome | Output | Tempo |
|------|------|--------|-------|
| **6** | Anki deck | FILE anki_deck.txt (max 25 carte CORE) | 5-8 min |

**Output finale**: File anki_deck.txt generato

**[Pausa B]** - Digita 'continua' per proprietà DB o 'ferma'

---

### Gruppo C - Database (~3-5 min)

Esecuzione dopo "continua"

| Fase | Nome | Output | Tempo |
|------|------|--------|-------|
| **7** | Proprietà Voci | Estrazione termini, batch processing DB, relazioni Voci→Argomenti | 3-5 min |

**Output finale**: Database aggiornato, workflow completato

**Tempo totale workflow**: 45-60 min

## Funzionalità Chiave (v4.7)

### 1. **Integrazione Multi-Documento Intelligente** (NUOVO v4.7)
- **Auto-rilevamento**: 2+ sbobine sullo stesso argomento → avvio automatico Fase 0
- **Analisi scope**: coverage per area (Eziologia, Clinica, Diagnosi, Terapia) con score 0-3
- **Identificazione**:
  - **Overlap**: aspetti coperti da 2+ fonti
  - **Complementare**: aspetti unici per fonte (es. tecniche chirurgiche da Plastica)
  - **Conflitti**: discordanze risolte automaticamente (priorità a fonte specializzata)
- **Pesi proporzionali**: ogni H2 sviluppato secondo expertise fonti
  - Esempio: "Terapia chirurgica" → 75% Plastica, 25% Dermatologia
- **Traccia integrata**: unica struttura H2/H3 che include TUTTO da entrambe fonti
- **Output**: contenuto Notion completo con informazioni pesate e conflitti risolti
- **Caso d'uso**: melanoma da Plastica (focus ricostruttivo) + Dermatologia (focus clinico/staging)

### 2. **Workflow a 3 Gruppi con Pause Strategiche**
- **Gruppo A**: Contenuto studiabile completo (Traccia→Pagina→Diagramma→Pitch→Tempo)
- **Gruppo B**: Anki deck isolato (esecuzione separata dopo conferma)
- **Gruppo C**: Database linking (Voci→Argomenti via relazioni)
- **Opzionale**: Link automatici proposti dopo Gruppo C
- Pause dopo ogni gruppo per controllo utente

### 2. **Interfaccia Elegante e Minimale**
- Output pulito: no CAPS (eccetto sigle CCI), no === spam
- Info essenziale: solo risultato + metriche brevi
- Esempio output: `Pagina pubblicata: [URL] | [N] parole | CCI: PASS | Callout: [N]`
- Comunicazione chiara con simboli tree: `├─` e `└─`

### 3. **Generazione Diretta su Notion**
- Fase 2: genera pagina DIRETTAMENTE su Notion in memoria
- Validazione CCI interna dopo ogni H2 (no simulazione, no output chat)
- Callout 5-7 inseriti automaticamente durante generazione
- Elimina duplicazione lavoro e lunghezza chat

### 4. **Pitch con Grassetto Markdown**
- Fase 4: pitch property come text semplice
- UNA frase in grassetto: `**testo decisivo**` (Markdown)
- Notion mantiene grassetto ** ** nella property
- Fix: formato Notion-flavored Markdown ufficiale

### 5. **File Anki Generato**
- Fase 6: genera FILE `anki_deck.txt` nel progetto
- Max 25 carte CORE con singola c1
- Anti-confusori automatici (5 pattern sistematici)
- Output chat minimale: `File generato: anki_deck.txt ([N] carte CORE)`

### 6. **CCI Enforcement**
- Validazione incrementale dopo ogni H2 generato
- Conteggi strict: max 18 parole/frase (threshold rigido, non media)
- Pitch: validazione automatica 170-200 parole
- Auto-correzione interna se necessario

### 7. **Anti-confusori Anki**
Pattern sistematici per evitare interferenza tra carte:
- **Età/popolazione**: "nel *neonato*" vs "nell'*adulto* >65 anni"
- **Temporalità**: "fase *acuta* (<72h)" vs "fase *cronica* (>3 mesi)"
- **Gravità/tipo**: "asma *intermittente*" vs "asma *persistente grave*"
- **Localizzazione**: "ictus *emisfero dominante*"
- **Contesto clinico**: "in *assenza di insufficienza renale*"

### 7. **Retry Logic & Cache**
- **Retry**: API timeout (3x), rate limit 429 (3x), server error 500+ (2x), network (3x)
- **Rate limiting**: 350ms tra chiamate consecutive, batch operations
- **Backoff**: esponenziale (1s, 2s, 4s)

### 8. **Database Unificato Voci**
- Property "Categoria" multi_select: ["Eziologia", "Clinica", "Diagnosi", "Terapia"]
- Relazione bidirezionale con Argomenti
- Batch processing Fase 7: search → create → update

## Comandi Separati

### Link (NON parte del workflow)

Invocazione solo su richiesta esplicita utente.

**Modalità 1 - Link Automatico**:
```
link auto [argomento]
```
- Trova argomenti correlati via overlap Voci (score ≥2)
- Aggiorna property "Argomenti correlati" (self-relation su DB Argomenti)
- Update bidirezionale automatico
- Esempio: Carcinoma spinocellulare ↔ Carcinoma basocellulare (overlap: UV, istopatologia)

**Modalità 2 - Link Compare**:
```
link compare [arg1] [arg2] ...
```
- Crea pagina confronto differenziale nel DB Argomenti
- Contenuto: callout differenze, pitch comparativo, diagramma decisionale, tabella comparativa
- Property: Argomento primario = false, Argomenti correlati = [url_A, url_B, ...]
- Collegamento bidirezionale con argomenti originali

## Output Generati

1. **Pagina Notion** (generata direttamente):
   - H2/H3 con toggle ▶
   - Indentazione 2 spazi (livello 1 sotto H2), 4 spazi (livello 2 sotto H3)
   - **5-7 callout clinici** con icone e colori (inseriti automaticamente)
   - Diagramma Mermaid MedGraph (B/N + accento #00E0CC)
   - 5-7 domande cliniche integrate
   - Perle del professore (se in fonte)

2. **anki_deck.txt**:
   - Max 25 carte CORE high-yield
   - Formato: 1 carta/linea con {{c1::risposta}}
   - Anti-confusori applicati sistematicamente

3. **Proprietà Database**:
   - **Voci**: relazione a DB unificato (2-3 termini × 4 categorie)
   - **Pitch**: 170-200 parole (rich_text con UNA frase in grassetto)
   - **Complessità**: Semplice/Media/Complessa (calcolo automatico)
   - **Tempo studio stimato**: minuti (calcolo: H2×2.5 + H3×1.5 + Callout×1 + Domande×0.5)

4. **Summary Finale**:
   - Metriche contenuto (parole, frasi, carte)
   - Proprietà & linking
   - Performance (tempo, API calls, cache hit rate)
   - Checklist qualità CCI

## Comandi Disponibili

```bash
workflow completo  # 7 fasi (~50-60 min) con 3 pause obbligatorie
essenziale        # Solo fasi 1-2 (traccia + pagina Notion con callout)
continua          # Procedi a blocco successivo (dopo pause)
ferma             # Stop workflow
status            # Mostra stato scratchpad
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

## Esempi Utilizzo

### Scenario 1: Sbobina singola

**Input**: Sbobina PDF "Insufficienza Cardiaca" allegata come unico file

```markdown
[Fase 1] ✓ Traccia completata (5 H2)
[ONESHOT] Auto-avanzamento: Fase 2

[Fase 2] [H2.1] Frasi: 18, >18: 2 (11.1%)
[Fase 2] [H2.1] Autocorretto
[Fase 2] [H2.2] Frasi: 15, >18: 0 (0.0%)
[Fase 2] [H2.3] Frasi: 20, >18: 1 (5.0%)
[Fase 2] [H2.4] Frasi: 17, >18: 0 (0.0%)
[Fase 2] [H2.5] Frasi: 22, >18: 1 (4.5%)
[ONESHOT] Auto-avanzamento: Fase 3

Pagina pubblicata: https://notion.so/...
Parole: 1850 | CCI: PASS (frasi >18: 4.3%) | Callout: 6 (RED:2 | BLUE:2 | GREEN:2)

[Fase 3] Diagramma inserito: flowchart (10 nodi)
[ONESHOT] Auto-avanzamento: Fase 4

[Fase 4] Pitch inserito: 185 parole (PASS)

═══════════════════════════════════════════
    BLOCCO 1 COMPLETATO
═══════════════════════════════════════════

CONTENUTO STUDIABILE:
- Pagina Notion: 1850 parole (CCI: PASS)
- Callout: 6 (RED:2 | BLUE:2 | GREEN:2)
- Diagramma: flowchart (10 nodi)
- Pitch: 185 parole

PROSSIMO STEP:
Fase 5: File Anki (~5-8 min)

Digita 'continua' per Anki o 'ferma' per terminare
```

**Utente**: `continua`

```markdown
[Fase 5] File anki_deck.txt generato (23 carte CORE)

═══════════════════════════════════════════
    BLOCCO 2 COMPLETATO
═══════════════════════════════════════════

ANKI PRONTO:
- File: anki_deck.txt
- Carte: 23 CORE
- Anti-confusori: applicati

PROSSIMO STEP:
Fase 6: Proprietà DB (~3-5 min)

Digita 'continua' per proprietà o 'ferma' per terminare
```

**Utente**: `continua`

```markdown
[Fase 6] Termini processati: 12/12 (cached: 3, created: 5, found: 4)

═══════════════════════════════════════════
    BLOCCO 3 COMPLETATO
═══════════════════════════════════════════

PROPERTIES DB:
- Voci: 12 termini (Ez:3 | Cl:3 | Dg:3 | Tx:3)
- Complessità: Media
- Tempo studio: 25 min

PROSSIMO STEP (opzionale):
Fase 7: Link automatici (~3-5 min)

Digita 'continua' per link o 'ferma' per terminare
```

**Utente**: `continua`

```markdown
[Fase 7] ✓ Trovati 3 collegamenti (score ≥2)

Collegamenti creati: 3

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

---

### Scenario 2: Sbobine multiple (multi-documento)

**Input**: 2 documenti sullo stesso argomento
- `melanoma_plastica.pdf` (focus: terapia chirurgica e ricostruzione)
- `melanoma_dermatologia.pdf` (focus: clinica, diagnosi, staging)

```markdown
2 sbobine rilevate sullo stesso argomento. Avvio integrazione fonti.

[Fase 0] Analisi scope documenti...

Analisi fonti completata (2 documenti):

Plastica chirurgica - Focus Terapia (score 3), Clinica (score 2)
Dermatologia - Focus Clinica (score 3), Diagnosi (score 3), Terapia (score 2)

Overlap identificato: 4 aree comuni (eziologia UV, caratteristiche cliniche, biopsia, escissione)
Complementare: 8 aspetti unici (Plastica: tecniche ricostruttive, innesti, lembi | Dermato: staging TNM, dermatoscopia, immunoterapia, follow-up, target therapy)
Conflitti rilevati: 1 (margini escissione: risoluzione automatica via priorità chirurgica)

Piano integrazione generato con pesi proporzionali.

Fase 0 completata. Procedo alla Fase 1: Traccia integrata.

[Fase 1] Traccia integrata in corso...

# Melanoma cutaneo

## Eziologia e fattori di rischio
-> Sviluppo: 70% Dermatologia, 30% Plastica
-> Focus: esposizione UV (overlap), genetica BRAF, fototipi, nevi

## Quadro clinico ed evoluzione
-> Sviluppo: 60% Dermatologia, 40% Plastica
-> Focus: caratteristiche ABCDE, varianti istologiche, staging TNM

### Varianti anatomo-cliniche
-> SSM, nodulare, lentigo maligna, acrale

## Diagnosi
-> Sviluppo: 75% Dermatologia, 25% Plastica
-> Focus: dermatoscopia, biopsia escissionale, imaging staging

## Terapia chirurgica e ricostruttiva
-> Sviluppo: 75% Plastica, 25% Dermatologia
-> Focus: margini escissione (1-2cm, fonte chirurgica), tecniche ricostruttive, linfonodo sentinella
-> CONFLITTO RISOLTO: Margini (Plastica 1-2cm vs Dermato 0.5-1cm) → range chirurgico

### Tecniche ricostruttive
-> Innesti, lembi locali, chiusura diretta

## Terapie adiuvanti e follow-up
-> Sviluppo: 60% Dermatologia, 40% Plastica
-> Focus: immunoterapia, target therapy BRAF/MEK, follow-up dermatoscopico

Traccia integrata completata (2 fonti integrate). Procedo alla Fase 2.

[Fase 2] Generazione pagina con integrazione proporzionale...

Pagina pubblicata: https://notion.so/Melanoma-cutaneo-...
2450 parole | CCI: PASS (frasi >18: 3.8%) | Callout: 7 (red:3 | blue:2 | green:2)

[Callout RED inserito per conflitto risolto]:
"Margini escissione: range **1-2 cm** secondo linee guida chirurgiche (discordanza con range dermatologico 0.5-1cm)"

[Fase 3] Diagramma inserito: flowchart decisionale (9 nodi)

[Fase 4] Pitch inserito: 192 parole | Status: Attivo

[Fase 5] Complessità: Complessa | Tempo stimato: 35 min

Gruppo A completato - Contenuto studiabile

Pagina Notion pubblicata: https://notion.so/Melanoma-cutaneo-...

Contenuto generato: 2450 parole (CCI: PASS, frasi >18: 3.8%)
Callout inseriti: 7 totali (red: 3, blue: 2, green: 2)
Diagramma: flowchart con 9 nodi
Pitch: 192 parole, Status: Attivo
Tempo stimato: 35 min (complessità: Complessa)

FONTI INTEGRATE:
- Plastica chirurgica: 45% contenuto (specializzata terapia/ricostruzione)
- Dermatologia: 55% contenuto (specializzata clinica/diagnosi/staging)

Digita 'continua' per generare Anki o 'ferma' per terminare.
```

**Utente**: `continua`

```markdown
[Fase 6] Generazione Anki da contenuto integrato...

File generato: anki_deck.txt (25 carte CORE)

[Carte integrate da entrambe fonti]:
- Eziologia/clinica: 8 carte (prevalenza Dermato)
- Diagnosi: 5 carte (prevalenza Dermato)
- Terapia chirurgica: 7 carte (prevalenza Plastica)
- Terapie adiuvanti: 5 carte (prevalenza Dermato)

Gruppo B completato - Anki pronto

File generato: anki_deck.txt
Carte CORE: 25/25 con anti-confusori applicati

Digita 'continua' per aggiornare proprietà database o 'ferma' per terminare.
```

**Utente**: `continua`

```markdown
[Fase 7] Estrazione termini da contenuto integrato...

Proprietà Voci collegate: 11 termini totali
Eziologia: 3 (UV, BRAF, nevi displastici)
Clinica: 2 (ABCDE, Breslow)
Diagnosi: 3 (dermatoscopia, biopsia escissionale, linfonodo sentinella)
Terapia: 3 (margini escissione, innesto, immunoterapia)

Gruppo C completato - Database aggiornato

Workflow completato.
```

---

## Performance & Metriche

### Timing Medio

| Workflow | Tempo Totale | Fasi Incluse |
|----------|-------------|--------------|
| **Completo** (singola fonte, tutti i blocchi) | 45-60 min | 1-7 |
| **Completo** (multi-doc, tutti i blocchi) | 50-65 min | 0-7 (include Fase 0) |
| **Completo** (solo Blocco A) | 35-50 min | 0-5 (con Fase 0 se multi-doc) |
| **Essenziale** | 20-30 min | 1-2 (solo traccia + pagina) |

### Ottimizzazioni v4.5

| Fase | Tempo v4.4 | Tempo v4.5 | Miglioramento |
|------|-----------|-----------|---------------|
| **Fase 2** (Pagina + Callout) | 18-28 min (separati) | 15-25 min | **-15%** (generazione combinata) |
| **Totale Blocco 1** | 35-45 min | 30-40 min | **-13%** (workflow semplificato 7 fasi) |
| **Output chat** | ~2000 parole | ~100 parole | **-95%** (validazione CCI interna) |
| **Fase 6** (Batch DB) | 3-5 min | 3-5 min | Mantenuto (già ottimizzato v4.1) |

### API Calls

| Fase | Chiamate Tipiche | Note |
|------|------------------|------|
| Fase 2 (Pagina + Callout) | 2 | search + replace_content (pubblicazione diretta) |
| Fase 3 (Diagramma) | 1 | append_block |
| Fase 4 (Pitch) | 1 | update_properties (rich_text) |
| Fase 6 (Proprietà) | 12-20 | batch search (cached: -40%), batch create, update |
| Fase 7 (Link) | 5-10 | search candidati + match score + create collegamenti |

**Cache hit rate medio**: 20-30% (termini comuni come "TC", "Corticosteroidi", "Biopsia")

## Database Notion

### Argomenti

- **ID**: `1b5282519c2c80a68c37ce5e4bd56f22`
- **Data Source**: `collection://1b528251-9c2c-8065-a61e-000bfdfab7c7`

**Properties**:
- Nome (title)
- Pitch (rich_text) ← formattazione grassetto preservata
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
| **Callout** | **5-7** | Working memory capacity aumentata |
| **Pilastri H2** | 4-6 | Optimal chunking |
| **Domande ❓** | 5-7 | Spaced retrieval |
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

### v4.7 (Multi-Documento) - Corrente
- 🔀 **Integrazione multi-documento intelligente**: 2+ sbobine → analisi scope + traccia integrata unica
- 🆕 **Fase 0**: analisi fonti, coverage scoring (0-3), identificazione overlap/complementare/conflitti
- 📊 **Pesi proporzionali**: ogni H2 sviluppato secondo expertise fonti (es. 75% Plastica, 25% Dermato)
- ⚖️ **Risoluzione conflitti**: automatica via priorità fonte specializzata
- 🚀 **Auto-start multi-doc**: rileva 2+ documenti stesso argomento, avvia Fase 0
- 📝 **Output integrato**: callout RED per conflitti risolti, fonti indicate in summary
- ⏱️ **Timing**: +3-5 min per Fase 0 (50-65 min totale con multi-doc)
- 🎯 **Caso d'uso**: melanoma da Plastica + Dermatologia → pagina unica completa

### v4.6.2 (Link Separato)
- **RIMOSSO**: Fase 8 Link dal workflow completo (non più opzionale)
- **CREATO**: comando Link separato con 2 modalità standalone
- **MODALITÀ 1** (link auto): trova argomenti correlati via overlap Voci (score ≥2), aggiorna property "Argomenti correlati" (self-relation)
- **MODALITÀ 2** (link compare): crea pagina confronto differenziale con callout, pitch, diagramma decisionale, tabella comparativa
- **WORKFLOW FINALE**: 7 fasi (5+1+1), Gruppi A/B/C senza opzionali
- **TIMING**: workflow completo 45-60 min, link separati su richiesta

### v4.6.1 (Property Nome + MedGraph)
- **FIX CRITICAL**: Fase 2 cerca pagina esistente con property "Nome" (non "title")
- **FIX OUTPUT**: paragrafi semplici ed eleganti (rimossi simboli tree)
- **REINTEGRATO**: regole MedGraph complete in Fase 3

### v4.6 (Clean Interface)
- **RIORGANIZZATO**: 3 gruppi logici (A: Contenuto, B: Anki, C: Database) + 1 opzionale
- **RINUMERATO**: 8 fasi totali (5+1+1+1 opzionale)
- **SPOSTATO**: Complessità + Tempo da Fase 6 a nuova Fase 5 (fine Gruppo A)
- **SEPARATO**: Status+Tempo (Gruppo A) da Voci (Gruppo C)
- **PULITO**: interfaccia elegante (no CAPS eccetto sigle, no === spam)
- **MINIMIZZATO**: output solo risultato + info breve
- **PAUSE STRATEGICHE**: 3 pause (fine A, fine B, fine C) per controllo utente
- **TIMING**: ottimizzato 45-60 min totale
- **MANTENUTO**: tutto il dettaglio tecnico e validazione da v4.5

### v4.5 (Optimized)
- **SEMPLIFICATO**: 7 fasi (da 9) - workflow più lineare
- **OTTIMIZZATO**: generazione diretta su Notion (no output lungo in chat)
- **AGGIUNTO**: validazione CCI interna in memoria (no simulazione)
- **COMBINATO**: Fase 2 = pagina + callout 5-7 in un solo step
- **AGGIUNTO**: pitch rich_text con grassetto formattato (fix critico)
- **AGGIUNTO**: diagramma append blocks direttamente su Notion (Fase 3)
- **AGGIORNATO**: callout 5-7 (da 3-5) in orchestrator + istruzioni
- **RIDOTTO**: tempo totale ~50-60 min (da ~55-65 min v4.4)
- **RIDOTTO**: output chat ~95% (da ~2000 a ~100 parole)
- **MANTENUTO**: 3 pause obbligatorie (dopo Fase 4, 5, 6)
- **MANTENUTO**: scratchpad system + oneshot automatico

### v4.4 (Fixed) - Deprecata
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

**Ultimo aggiornamento**: v4.5 Optimized (2025)
**Piattaforma**: Claude Web Projects (browser)
**Dipendenze**: API Notion (custom integration), Mermaid.js (diagrammi)
