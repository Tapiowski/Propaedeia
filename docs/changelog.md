# Changelog Propaedeia

## v4.7 (Multi-Documento) - Corrente

**Data**: 2025-10-20

### Novità principali

- 🔀 **Integrazione multi-documento intelligente**: 2+ sbobine → analisi scope + traccia integrata unica
- 🆕 **Fase 0**: analisi fonti, coverage scoring (0-3), identificazione overlap/complementare/conflitti
- 📊 **Pesi proporzionali**: ogni H2 sviluppato secondo expertise fonti (es. 75% Plastica, 25% Dermato)
- ⚖️ **Risoluzione conflitti**: automatica via priorità fonte specializzata
- 🚀 **Auto-start multi-doc**: rileva 2+ documenti stesso argomento, avvia Fase 0
- 📝 **Output integrato**: callout RED per conflitti risolti, fonti indicate in summary
- ⏱️ **Timing**: +3-5 min per Fase 0 (50-65 min totale con multi-doc)

### Caso d'uso

Melanoma da Plastica (focus terapia ricostruttiva) + Dermatologia (focus clinica/staging) → pagina unica completa integrata con pesi proporzionali

---

## v4.6.2 (Link Separato)

**Data**: 2025-10

### Modifiche

- **RIMOSSO**: Fase 8 Link dal workflow completo (non più opzionale)
- **CREATO**: comando Link separato con 2 modalità standalone
- **MODALITÀ 1** (link auto): trova argomenti correlati via overlap Voci (score ≥2), aggiorna property "Argomenti correlati" (self-relation)
- **MODALITÀ 2** (link compare): crea pagina confronto differenziale con callout, pitch, diagramma decisionale, tabella comparativa
- **WORKFLOW FINALE**: 7 fasi (5+1+1), Gruppi A/B/C senza opzionali
- **TIMING**: workflow completo 45-60 min, link separati su richiesta

---

## v4.6.1 (Property Nome + MedGraph)

**Data**: 2025-10

### Fix critici

- **FIX CRITICAL**: Fase 2 cerca pagina esistente con property "Nome" (non "title")
- **FIX OUTPUT**: paragrafi semplici ed eleganti (rimossi simboli tree)
- **REINTEGRATO**: regole MedGraph complete in Fase 3

---

## v4.6 (Clean Interface)

**Data**: 2025-10

### Riorganizzazione

- **RIORGANIZZATO**: 3 gruppi logici (A: Contenuto, B: Anki, C: Database) + 1 opzionale
- **RINUMERATO**: 8 fasi totali (5+1+1+1 opzionale)
- **SPOSTATO**: Complessità + Tempo da Fase 6 a nuova Fase 5 (fine Gruppo A)
- **SEPARATO**: Status+Tempo (Gruppo A) da Voci (Gruppo C)
- **PULITO**: interfaccia elegante (no CAPS eccetto sigle, no === spam)
- **MINIMIZZATO**: output solo risultato + info breve
- **PAUSE STRATEGICHE**: 3 pause (fine A, fine B, fine C) per controllo utente
- **TIMING**: ottimizzato 45-60 min totale
- **MANTENUTO**: tutto il dettaglio tecnico e validazione da v4.5

---

## v4.5 (Optimized)

**Data**: 2025-09

### Ottimizzazioni

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

---

## v4.4 (Fixed) - Deprecata

**Data**: 2025-09

### Fix critici

- **RIPRISTINATO**: scratchpad system, oneshot automatico fino a Fase 7
- **FIX CRITICO**: Fase 2 genera markdown SEMPLICE (`## Titolo`, non `>## Titolo`)
- **FIX CRITICO**: Fase 2 output COMPLETO in chat (tutta pagina visibile)
- **FIX CRITICO**: Fase 4 genera FILE anki_deck.txt (non output chat)
- **FIX CRITICO**: Fase 7 conversione markdown → Notion + pubblicazione
- **MANTENUTO**: pause obbligatorie dopo Fase 7 e Fase 8
- **MANTENUTO**: indentazione 2 spazi (H2), 4 spazi (H3), NO TAB
- **OTTIMIZZATO**: 780 linee, workflow chiaro e funzionante

---

## v4.3 (Simplified) - Deprecata

**Data**: 2025-08

### Errori

- RIMOSSO scratchpad (ERRORE - era necessario)
- RIMOSSO oneshot (ERRORE - causava pause ad ogni fase)
- Fix indentazione e generazione reale (parziali)

---

## v4.2 (Production-Ready) - Deprecata

**Data**: 2025-08

### Funzionalità

- ✨ **AUTO-START intelligente**: rilevamento file unico → avvio automatico workflow
- ⏸️ **Pause strategiche**: Blocco 1 (fasi 1-7) → PAUSA → Blocco 2 (fasi 8-9)
- 🔢 **Riorganizzazione fasi**: rinumerate 1-9 (da 1a/1b/2a/3a...)
- 📐 **Regole integrate**: Capitalizzazione sentence case, MedGraph template standardizzato
- 🔧 **Fix critici**: indentazione 2 spazi (no TAB), batch processing Step 8c

### Errori

- Formato Notion-ready in Fase 2 (ERRORE - complicato)
- Placeholders per callout/diagramma (ERRORE - non funzionavano)

---

## v4.1 (Database Unificato)

**Data**: 2025-07

### Ottimizzazioni database

- 🗄️ **DB Voci unificato**: property "Categoria" multi_select sostituisce 4 relazioni separate
- 📦 **Batch processing**: Fase 8c ottimizzata (-80% API calls)
- 🚀 **Performance**: Fase 8 da ~8-12 min a ~3-5 min

---

## v4.0 (Scratchpad & Oneshot)

**Data**: 2025-06

### Funzionalità principali

- 💾 **Scratchpad system**: tracking stato workflow, prerequisiti, outputs salvati
- ⚡ **Oneshot mode**: esecuzione automatica senza conferme (default)
- 🔄 **Zero rigenerazione**: str_replace workflow (Fase 2 → 3 → 6 → 7)
- 📊 **Summary finale**: metriche, performance, checklist CCI
- ⏱️ **Performance**: workflow da ~70 min a ~55-60 min

---

## v3.0 (Output Ottimizzato)

**Data**: 2025-05

### Ottimizzazioni output

- 📝 **Anki .txt file**: output 1 carta/linea, zero chat output
- 🎯 **CORE-only**: max 25 carte (da 35-50), TIER 2 rimosso
- 🛡️ **Anti-confusori**: 5 pattern sistematici automatici
- ✅ **CCI enforcement**: validazione incrementale strict
- ♻️ **Retry logic**: API timeout, rate limit, network errors
- 💾 **Cache termini**: dizionario sessione (-40% chiamate duplicate)

---

## v2.5 (Prima Integrazione Notion)

**Data**: 2025-03

### Integrazione Notion

- 🔗 **API Notion**: pubblicazione automatica database Argomenti
- 🏷️ **Proprietà DB**: Eziologia, Clinica, Diagnosi, Terapia (4 relazioni separate)
- ⏱️ **Tempo workflow**: ~70 min (workflow manuale con conferme)

---

## v1.0 (MVP)

**Data**: 2025-01

### Funzionalità base

- 📄 **Generazione base**: Pagina Notion markdown
- 📚 **Anki basic**: ~50 carte miste (CORE + NON-CORE)
- ⚙️ **Workflow manuale**: conferme utente obbligatorie tra fasi

---

**Piattaforma**: Claude Web (browser), Claude Code (CLI)
**Dipendenze**: API Notion, Mermaid.js
