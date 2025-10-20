# Propaedeia

Pipeline automatica Claude → Notion & Anki per trasformare sbobine mediche in materiale di studio ottimizzato.

## Overview

Propaedeia processa lezioni/sbobine di medicina generando:
- **Pagine Notion** strutturate (callout, diagrammi Mermaid, CCI)
- **Deck Anki** (max 25 carte CORE con anti-confusori)
- **Proprietà database** estratte automaticamente
- **Link automatici** tra argomenti correlati

**Versione corrente**: v4.7 (Multi-Documento)

## Quick Start

### Utilizzo con Claude Web

1. Copia `istruzioni.txt` nelle Custom Instructions del progetto
2. Carica `orchestrator.txt` tra i file disponibili
3. Allega sbobina(e) PDF/MD
4. Digita un comando:

```bash
workflow completo    # Fasi 0-7 (~45-60 min)
essenziale          # Solo traccia + pagina (~20-30 min)
link [arg1] [arg2]  # Collegamenti tra argomenti (~5-8 min)
```

### Comandi controllo

```bash
continua    # Procedi gruppo successivo
ferma       # Stop workflow
status      # Mostra stato corrente
```

### File core

| File | Ruolo |
|------|-------|
| `orchestrator.txt` | Motore workflow v4.7 (~1180 linee) |
| `istruzioni.txt` | Custom instructions CCI (~154 linee) |

## Funzionalità v4.7

- **Multi-documento**: integrazione automatica 2+ sbobine stesso argomento
- **Workflow 3 gruppi**: A (Contenuto), B (Anki), C (Database) con pause strategiche
- **CCI enforcement**: frasi ≤18 parole, validazione automatica
- **Output minimale**: interfaccia elegante, info essenziali

## Documentazione

Per dettagli completi consulta:

- **[Workflow](docs/workflow.md)**: fasi 0-7 dettagliate
- **[Architettura](docs/architecture.md)**: file, database, CCI, limiti cognitivi
- **[Esempi](docs/examples.md)**: scenari uso (singola/multi-doc)
- **[Changelog](docs/changelog.md)**: versioni e modifiche
- **[Technical](docs/technical.md)**: API Notion, performance, formati

## Setup Claude Web

Consulta [Setup Progetto Claude Web](docs/claude_web_project.md) per configurazione dettagliata.

---

**Piattaforma**: Claude Web (browser), Claude Code (CLI)
**Dipendenze**: API Notion, Mermaid.js
