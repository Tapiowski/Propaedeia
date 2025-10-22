# Architettura Propaedeia

## File Core

| File | Ruolo | Linee |
|------|-------|-------|
| **orchestrator.txt** | Motore workflow v4.7: 3 gruppi (A/B/C), integrazione multi-documento, output minimale | ~1180 |
| **istruzioni.txt** | Custom instructions: CCI, vincoli fonte, limiti cognitivi, validazione | ~154 |
| **Diagrammi.txt** | Specifiche MedGraph: palette B/N + accento #00E0CC, limiti nodi, template | ~62 |
| **Capitalizzazione.txt** | Regole sentence case italiano (maiuscola solo prima parola + nomi propri) | ~19 |

---

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
- Argomenti correlati (relation → Argomenti, per link automatici)

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

---

## Criteri CCI (Chiarezza Clinica Integrata)

1. **Main message first**: ogni H2 apre con 1-2 frasi chiave
2. **Plain language**: frasi ≤18 parole (threshold rigido), paragrafi 2-4 frasi
3. **Numeri con unità**: ogni dato quantitativo ha unità esplicita ("5 giorni", NON "5")
4. **Voce attiva**: operazioni con verbi imperativi/infiniti
5. **Validazione incrementale**: auto-check dopo ogni H2 (se ≥2 fail → autocorreggi)
6. **Quick check finale**: conteggi automatici + checklist (se >20% frasi >18 → STOP)

---

## Limiti Cognitivi Standard

| Elemento | Range | Rationale |
|----------|-------|-----------|
| **Callout** | 5-7 | Working memory capacity |
| **Pilastri H2** | 4-6 | Optimal chunking |
| **Domande ❓** | 5-7 | Spaced retrieval |
| **Chiarimenti** | 3-5 | No overlap domande |
| **Anki CORE** | max 25 | High-yield essentials only |
| **Paragrafi** | 2-4 frasi | Readability |
| **Frasi** | ≤18 parole | Sentence complexity (threshold rigido) |
| **Pitch** | 170-200 parole | Elevator pitch standard (conteggio strict) |
| **Diagramma nodi** | ≤11 | Visual clarity |

---

## Output Generati

### 1. Pagina Notion (markdown Notion-ready)

- H2/H3 con toggle ▶
- Indentazione 2 spazi (livello 1 sotto H2), 4 spazi (livello 2 sotto H3)
- 5-7 callout clinici con icone e colori
- Diagramma Mermaid MedGraph (B/N + accento #00E0CC)
- 5-7 domande cliniche integrate
- Perle del professore (se in fonte)

### 2. anki_deck.txt

- Max 25 carte CORE high-yield
- Formato: 1 carta/linea con `{{c1::risposta}}`
- Anti-confusori applicati sistematicamente

### 3. Proprietà Database

- **Voci**: relazione a DB unificato (2-3 termini × 4 categorie)
- **Pitch**: 170-200 parole
- **Complessità**: Semplice/Media/Complessa (calcolo automatico)
- **Tempo studio stimato**: minuti (calcolo: H2×2.5 + H3×1.5 + Callout×1 + Domande×0.5)

### 4. Summary Finale

- Metriche contenuto (parole, frasi, carte)
- Proprietà & linking
- Performance (tempo, API calls, cache hit rate)
- Checklist qualità CCI

---

## Formato Relazioni Notion (CRITICAL)

✅ **CORRETTO** (quando scrivi):
```json
"Voci": "[\"https://notion.so/page1\", \"https://notion.so/page2\"]"
```

❌ **ERRATO**:
```json
"Voci": ["https://notion.so/page1"]  // Array, non stringa JSON
"Voci": ["{{https://notion.so/page1}}"]  // Doppie graffe (solo in output Notion quando leggi)
```

---

## Indentazione Notion

- **1 livello sotto H2**: 2 spazi iniziali
- **2 livelli sotto H3**: 4 spazi iniziali
- **NO TAB** (incompatibile con Notion import API)

---

## Callout Notion

Formato con indentazione 2 spazi:
```markdown
  <callout icon="/icons/warning_red.svg" color="red_bg">
  <span color="red">Testo avvertenza</span>
  </callout>
```

---

## MedGraph Palette

- Sfondo: #FFFFFF
- Testo/linee: #000000
- **Un solo accento**: #00E0CC (step critico, mai decorativo)
- Contrasto: testo ≥4.5:1, connettori ≥3:1
- Leggibile in scala di grigi (stampa-ready)

---

## Scratchpad System

Tracking stato workflow in `workflow_state`:

```python
workflow_state = {
    "fase_corrente": "",      # fase in esecuzione
    "fasi_completate": [],    # fasi completate

    "fonti": {
        "multi_doc": False,   # True se 2+ documenti
        "documenti": [],      # Lista {nome, categoria, scope_coverage}
        "piano_integrazione": {
            "overlap": {},    # Aspetti coperti da 2+ fonti
            "complementare": {},  # Aspetti unici per fonte
            "conflitti": [],  # Discordanze da risolvere
            "pesi_per_h2": {} # {h2_title: {fonte: peso%}}
        }
    },

    "outputs": {
        "titolo_argomento": "",
        "traccia_h2": [],
        "notion_page_url": "",
        "pitch": "",
        "anki_file": "",
        "voci_estratte": {}
    },

    "validation": {
        "cci_check": {},
        "prerequisiti": []
    }
}
```

**Funzioni**:
- Check prerequisiti automatico prima ogni fase
- Performance metrics (API calls, cache hits, retry)
- Comando `status` per visibilità real-time
