# Esempi Utilizzo Propaedeia

## Scenario 1: Sbobina Singola

**Input**: Sbobina PDF "Insufficienza Cardiaca" allegata come unico file

```markdown
Sbobina rilevata. Avvio automatico workflow completo.

[Fase 1] ✓ Traccia completata (5 H2)

[Fase 2] Generazione pagina Notion...

Pagina pubblicata: https://notion.so/Insufficienza-cardiaca-...
Parole: 1850 | CCI: PASS (frasi >18: 4.3%) | Callout: 6 (red:2 | blue:2 | green:2)

[Fase 3] Diagramma inserito: flowchart (10 nodi)

[Fase 4] Pitch inserito: 185 parole | Status: Attivo

[Fase 5] Complessità: Media | Tempo stimato: 25 min

═══════════════════════════════════════════
    GRUPPO A COMPLETATO
═══════════════════════════════════════════

CONTENUTO STUDIABILE:
├─ Pagina Notion: 1850 parole (CCI: PASS)
├─ Callout: 6 (red:2 | blue:2 | green:2)
├─ Diagramma: flowchart (10 nodi)
├─ Pitch: 185 parole, Status: Attivo
└─ Tempo stimato: 25 min (complessità: Media)

Digita 'continua' per Anki o 'ferma' per terminare.
```

**Utente**: `continua`

```markdown
[Fase 6] File generato: anki_deck.txt (23 carte CORE)

═══════════════════════════════════════════
    GRUPPO B COMPLETATO
═══════════════════════════════════════════

ANKI PRONTO:
├─ File: anki_deck.txt
├─ Carte: 23/25 CORE
└─ Anti-confusori: applicati

Digita 'continua' per proprietà database o 'ferma' per terminare.
```

**Utente**: `continua`

```markdown
[Fase 7] Termini processati: 12/12 (cached: 3, created: 5, found: 4)

═══════════════════════════════════════════
    GRUPPO C COMPLETATO
═══════════════════════════════════════════

DATABASE AGGIORNATO:
├─ Voci: 12 termini totali
│  ├─ Eziologia: 3
│  ├─ Clinica: 3
│  ├─ Diagnosi: 3
│  └─ Terapia: 3
├─ Complessità: Media
└─ Tempo studio: 25 min

Workflow completato.

═══════════════════════════════════════════
    WORKFLOW COMPLETO - RIEPILOGO FINALE
═══════════════════════════════════════════

📊 METRICHE CONTENUTO
├─ Traccia H2: 5 pilastri
├─ Pagina Notion: 1850 parole totali
│  ├─ Frasi totali: 92
│  ├─ Frasi >18 parole: 5 (5.4%)
│  └─ Media parole/frase: 14.2
├─ Callout: 6 (red:2 | blue:2 | green:2)
├─ Domande: 6
├─ Elevator Pitch: 185 parole
└─ Anki CORE: 23 carte

🔗 PROPRIETÀ & LINKING
├─ Voci totali: 12 (Eziologia:3 | Clinica:3 | Diagnosi:3 | Terapia:3)
├─ Complessità: Media
├─ Tempo studio: 25 min
└─ Database: aggiornato

⏱️  PERFORMANCE
├─ Tempo totale: 48 min
├─ API calls: 28 (cached: 3, retry: 0)
└─ Cache hit rate: 25%

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

## Scenario 2: Sbobine Multiple (Multi-Documento)

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

═══════════════════════════════════════════
    GRUPPO A COMPLETATO
═══════════════════════════════════════════

CONTENUTO STUDIABILE:
├─ Pagina Notion: 2450 parole (CCI: PASS)
├─ Callout: 7 (red:3 | blue:2 | green:2)
├─ Diagramma: flowchart (9 nodi)
├─ Pitch: 192 parole, Status: Attivo
└─ Tempo stimato: 35 min (complessità: Complessa)

FONTI INTEGRATE:
├─ Plastica chirurgica: 45% contenuto (specializzata terapia/ricostruzione)
└─ Dermatologia: 55% contenuto (specializzata clinica/diagnosi/staging)

Digita 'continua' per Anki o 'ferma' per terminare.
```

**Utente**: `continua`

```markdown
[Fase 6] Generazione Anki da contenuto integrato...

File generato: anki_deck.txt (25 carte CORE)

Carte integrate da entrambe fonti:
├─ Eziologia/clinica: 8 carte (prevalenza Dermato)
├─ Diagnosi: 5 carte (prevalenza Dermato)
├─ Terapia chirurgica: 7 carte (prevalenza Plastica)
└─ Terapie adiuvanti: 5 carte (prevalenza Dermato)

═══════════════════════════════════════════
    GRUPPO B COMPLETATO
═══════════════════════════════════════════

ANKI PRONTO:
├─ File: anki_deck.txt
├─ Carte: 25/25 CORE
└─ Anti-confusori: applicati

Digita 'continua' per proprietà database o 'ferma' per terminare.
```

**Utente**: `continua`

```markdown
[Fase 7] Estrazione termini da contenuto integrato...

Proprietà Voci collegate: 11 termini totali
├─ Eziologia: 3 (UV, BRAF, nevi displastici)
├─ Clinica: 2 (ABCDE, Breslow)
├─ Diagnosi: 3 (dermatoscopia, biopsia escissionale, linfonodo sentinella)
└─ Terapia: 3 (margini escissione, innesto, immunoterapia)

═══════════════════════════════════════════
    GRUPPO C COMPLETATO
═══════════════════════════════════════════

DATABASE AGGIORNATO:
├─ Voci: 11 termini totali
├─ Complessità: Complessa
└─ Tempo studio: 35 min

Workflow completato.

═══════════════════════════════════════════
    WORKFLOW MULTI-DOC - RIEPILOGO FINALE
═══════════════════════════════════════════

📊 METRICHE CONTENUTO
├─ Fonti integrate: 2 (Plastica 45%, Dermatologia 55%)
├─ Traccia H2: 5 pilastri integrati
├─ Pagina Notion: 2450 parole totali
│  ├─ Frasi totali: 145
│  ├─ Frasi >18 parole: 5 (3.8%)
│  └─ Media parole/frase: 16.9
├─ Callout: 7 (red:3 | blue:2 | green:2)
├─ Domande: 8
├─ Elevator Pitch: 192 parole
└─ Anki CORE: 25 carte

🔀 INTEGRAZIONE FONTI
├─ Overlap: 4 aree comuni
├─ Complementare: 8 aspetti unici
├─ Conflitti risolti: 1 (margini escissione)
└─ Pesi applicati: proporzionali a coverage score

🔗 PROPRIETÀ & LINKING
├─ Voci totali: 11
├─ Complessità: Complessa
└─ Tempo studio: 35 min

⏱️  PERFORMANCE
├─ Tempo totale: 58 min (include Fase 0: 4 min)
├─ API calls: 32 (cached: 4, retry: 0)
└─ Cache hit rate: 20%

✅ CHECKLIST QUALITÀ CCI
├─ ✓ Main message in ogni H2
├─ ✓ Frasi ≤18 parole (96.2%)
├─ ✓ Numeri con unità
├─ ✓ Pitch 170-200 parole
├─ ✓ Anki ≤25 CORE, solo c1
├─ ✓ Conflitti risolti (segnalati in callout RED)
└─ ✓ Integrazione proporzionale applicata

🔗 LINK NOTION
└─ https://notion.so/Melanoma-cutaneo-...

═══════════════════════════════════════════
```

---

## Scenario 3: Workflow Essenziale

**Input**: Sbobina breve per overview rapida

```bash
essenziale
```

```markdown
[Fase 1] ✓ Traccia completata (4 H2)

[Fase 2] Generazione pagina Notion...

Pagina pubblicata: https://notion.so/...
Parole: 980 | CCI: PASS (frasi >18: 3.1%) | Callout: 5

═══════════════════════════════════════════
    WORKFLOW ESSENZIALE COMPLETATO
═══════════════════════════════════════════

CONTENUTO ESSENZIALE:
├─ Pagina Notion: 980 parole
├─ Callout: 5
└─ Tempo: 22 min

Workflow terminato.
```

---

## Parametri Avanzati

### Override limite Anki

```bash
n=30
workflow completo
```

Output: anki_deck.txt con 30 carte invece di 25

### Focus su sezione specifica

```bash
focus=diagnosi differenziale
workflow completo
```

Output: traccia e contenuto zoom su diagnosi differenziale

### Modalità comparativa

```bash
mode=comparativo
workflow completo
```

Output: struttura confronto A vs B
