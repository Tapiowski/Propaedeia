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

### Utilizzo con Claude Code

1. Claude Code legge automaticamente `CLAUDE.md` per le istruzioni complete
2. Allega sbobina(e) PDF/MD
3. Digita un comando:

### Utilizzo con Claude Web

1. Copia sezioni A-B-C di `CLAUDE.md` nelle Custom Instructions del progetto
2. Carica sezioni D-E di `CLAUDE.md` tra i file disponibili
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
| `CLAUDE.md` | Istruzioni complete unificate per Claude (~480 linee) |

## Funzionalità v4.7

- **Multi-documento**: integrazione automatica 2+ sbobine stesso argomento
- **Workflow 3 gruppi**: A (Contenuto), B (Anki), C (Database) con pause strategiche
- **CCI enforcement**: frasi ≤18 parole, validazione automatica
- **Output minimale**: interfaccia elegante, info essenziali

## Documentazione

- **[CLAUDE.md](CLAUDE.md)**: Istruzioni complete, workflow, esempi, API specs
- **[Changelog](docs/changelog.md)**: Storico versioni e modifiche
- **[Technical](docs/technical.md)**: Metriche performance e timing
- **[Setup Claude Web](docs/claude_web_project.md)**: Configurazione progetto browser

---

**Piattaforma**: Claude Web (browser), Claude Code (CLI)
**Dipendenze**: API Notion, Mermaid.js
