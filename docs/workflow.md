# Workflow Completo v4.7

## Struttura a 3 Gruppi

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

**Tempo totale workflow**: 45-60 min (50-65 min con multi-doc)

---

## Fase 0: Analisi e Integrazione Fonti

**[Esegui SOLO se 2+ documenti sullo stesso argomento]**

**Input**: 2+ documenti PDF/MD (es. melanoma da plastica + dermatologia)

**Obiettivo**: Analizzare scope di ogni fonte, identificare overlap/complementarietà/conflitti, generare piano integrazione per traccia unica.

### Step 0.1: Analisi scope per documento

Per ogni documento:

**Identifica**:
- Nome documento/fonte (es. "Sbobina Plastica chirurgica", "Dispensa Dermatologia")
- Categoria fonte (Chirurgia, Medicina interna, Dermatologia, etc.)
- Argomento principale (deve essere coerente tra documenti)

**Valuta coverage per area** (score 0-3):
- **Eziologia**: 0=assente, 1=cenni, 2=standard, 3=approfondita
- **Clinica**: 0=assente, 1=cenni, 2=standard, 3=approfondita
- **Diagnosi**: 0=assente, 1=cenni, 2=standard, 3=approfondita
- **Terapia**: 0=assente, 1=cenni, 2=standard, 3=approfondita

### Step 0.2: Identificazione overlap e complementarietà

**Overlap** (aspetti coperti da 2+ fonti):
- Per ogni area (Eziologia, Clinica, Diagnosi, Terapia)
- Se 2+ documenti hanno score ≥1 → overlap presente
- Identifica sottotemi specifici trattati da entrambi

**Complementare** (aspetti unici per fonte):
- Identifica sottotemi presenti solo in 1 documento
- Aree con score alto (3) in una fonte e basso (0-1) in altre

**Conflitti** (informazioni discordanti):
- Cerca contraddizioni esplicite (es. dose farmaco, timing intervento)
- Approcci divergenti (chirurgico vs conservativo)
- Terminologia diversa per stesso concetto

### Step 0.3: Strategia integrazione e pesi

**Calcolo pesi per H2 pilastro**:
- Proporzionale a coverage score della fonte per quell'area
- Fonte più specializzata ha priorità in caso di overlap

**Risoluzione conflitti**:
- Priorità fonte più specializzata per quell'area
- Se entrambe equivalenti: integra entrambe prospettive (es. "Margini: 0.5-2cm secondo complessità")
- Segnala discordanze rilevanti in callout RED

---

## Fase 1: Traccia

**Input**:
- Sbobina PDF/MD (singola)
- OPPURE piano_integrazione da Fase 0 (se multi-doc)

**Regole**:
- H2 range: 4-6 pilastri
- Scope macro: copri TUTTO proporzionalmente
- Se `focus=X`: zoom su sezione specifica

### Modalità singola fonte

Analizza documento e crea traccia H2/H3 standard.

### Modalità multi-fonte

Usa piano_integrazione per creare traccia integrata:
- Pilastri H2 basati su coverage combinata
- Note sviluppo con pesi per ogni H2 (es. "70% Dermatologia, 30% Plastica")
- Segnala aspetti complementari da integrare
- Evidenzia conflitti da risolvere

**Output**: Traccia markdown con note pesi

---

## Fase 2: Pagina Notion + Callout

**Input**: Traccia approvata (singola o integrata)

**Processo**:

1. Genera contenuto DIRETTAMENTE in formato Notion (no conversioni)
2. Segui traccia H2/H3 per strutturare il contenuto
3. **Se multi-doc**: usa pesi da piano_integrazione per sviluppo proporzionale
4. Inserisci 5-7 callout automaticamente durante generazione
5. Valida CCI internamente (frasi ≤18 parole)
6. Pubblica su Notion con replace_content

**Formato Notion**:
- H2/H3 con toggle ▶
- Indentazione: 2 spazi (livello 1), 4 spazi (livello 2)
- Callout: `<callout icon="/icons/warning_red.svg" color="red_bg">...</callout>`
- NO TAB (incompatibile con Notion import API)

**Callout tipi**:
- ⚠️ RED: warning clinici, controindicazioni, conflitti risolti
- 💡 BLUE: concetti chiave, meccanismi
- ⭐ GREEN: perle, tips pratici

**Se multi-doc**: segnala conflitti risolti in callout RED

**Output**: Pagina Notion pubblicata con URL

---

## Fase 3: Diagramma

**Input**: Pagina Notion esistente

**Processo**:
1. Genera diagramma Mermaid (flowchart/timeline/graph)
2. Rispetta limiti MedGraph: ≤11 nodi, palette B/N + accento #00E0CC
3. Inserisci via append_blocks su Notion

**Output**: Diagramma pubblicato su Notion

---

## Fase 4: Pitch + Status

**Input**: Contenuto generato

**Processo**:
1. Genera pitch 170-200 parole
2. Formato: text semplice con UNA frase grassetto Markdown `**testo**`
3. Update property "Pitch" + "Status argomento" = "Attivo"

**Output**: Proprietà aggiornate

---

## Fase 5: Complessità + Tempo

**Input**: Struttura pagina

**Calcolo automatico**:

**Complessità**:
- Semplice: ≤4 H2, concetti base
- Media: 5 H2, standard
- Complessa: ≥6 H2, avanzata

**Tempo studio** (minuti):
```
tempo = (H2 × 2.5) + (H3 × 1.5) + (Callout × 1) + (Domande × 0.5)
```

**Output**: Proprietà "Complessità" e "Tempo studio stimato" aggiornate

---

## Fase 6: Anki Deck

**Input**: Contenuto Notion pubblicato

**Processo**:
1. Genera max 25 carte CORE high-yield
2. Formato: 1 carta/linea con `{{c1::risposta}}`
3. Applica anti-confusori sistematici (5 pattern)
4. Salva in file `anki_deck.txt`

**Anti-confusori**:
- Età/popolazione: "nel *neonato*" vs "nell'*adulto* >65 anni"
- Temporalità: "fase *acuta* (<72h)" vs "fase *cronica* (>3 mesi)"
- Gravità/tipo: "asma *intermittente*" vs "asma *persistente grave*"
- Localizzazione: "ictus *emisfero dominante*"
- Contesto clinico: "in *assenza di insufficienza renale*"

**Output**: File anki_deck.txt generato

---

## Fase 7: Proprietà Voci

**Input**: Contenuto Notion

**Processo**:
1. Estrai 2-3 termini per categoria (Eziologia, Clinica, Diagnosi, Terapia)
2. Batch processing: search → create → update (-80% API calls)
3. Cache termini comuni (hit rate 20-30%)
4. Update property "Voci" (relazione bidirezionale)

**Database Voci**:
- Property "Categoria" multi_select: ["Eziologia", "Clinica", "Diagnosi", "Terapia"]
- Relazione bidirezionale con Argomenti

**Output**: Database aggiornato, workflow completato

---

## Comandi Disponibili

### Workflow

```bash
workflow completo    # Fasi 0-7 (~45-60 min) [ONESHOT]
essenziale          # Fasi 1-2 (~20-30 min) [ONESHOT]
```

### Link (separato dal workflow)

```bash
link [arg1] [arg2]  # Collegamenti clinici tra 2 argomenti (~5-8 min)
pubblica notion     # Solo Fase 7 (se hai già tutti gli output)
```

### Controllo

```bash
continua            # Procedi gruppo successivo
ferma               # Stop workflow
status              # Mostra stato scratchpad corrente
```

### Parametri Opzionali

Inserire PRIMA del comando:

```bash
n=30              # Override limite Anki CORE (default: max 25)
focus=diagnosi    # Restringe scope a sezione specifica
mode=comparativo  # Forza modalità confronto A vs B
```

**Esempio**:
```bash
n=30
focus=diagnosi differenziale
workflow completo
```
