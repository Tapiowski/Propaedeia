# Setup del Progetto Claude Web

Questa guida descrive come utilizzare i file `istruzioni.txt` e `orchestrator.txt` all'interno di un progetto **Claude Web** senza eseguire codice localmente.

## Struttura consigliata del progetto Claude

Nel progetto Claude crea due elementi principali:

1. **Custom Instructions** – copia il contenuto di `istruzioni.txt` nel pannello *Instructions* del progetto. In questo modo ogni conversazione eredita automaticamente i vincoli di stile, le regole CCI e i limiti cognitivi.
2. **File Orchestrator** – aggiungi il file `orchestrator.txt` alla sezione *Files* del progetto Claude. Mantieni il nome per facilitarne il riferimento durante il workflow.

Puoi opzionalmente aggiungere altri file della sbobina (PDF, MD) nello stesso progetto: l'orchestrator è progettato per riconoscerli e chiedere quale processare.

## Avvio del workflow

1. Apri una nuova chat nel progetto Claude con le istruzioni già caricate.
2. Incolla o allega la sbobina principale se non è già presente fra i file del progetto.
3. Digita uno dei comandi previsti (es. `workflow completo`, `essenziale`, `pubblica notion`). Claude leggerà il file `orchestrator.txt` e seguirà le fasi indicate senza necessità di codice locale.
4. Utilizza i comandi di controllo (`continua`, `modifica`, `ferma`, `salta`) per muoverti fra le fasi garantendo l'esecuzione step-by-step.

## Gestione degli output

- **Pagina Notion**: Claude costruisce la pagina in memoria (markdown) seguendo la traccia. Il contenuto può essere copiato direttamente da Claude a Notion oppure inviato tramite integrazioni successive.
- **Callout, Pitch, Proprietà, Anki, Diagramma**: vengono generati come blocchi separati durante le fasi 2-4. Mantieni questi output nel thread della conversazione: Claude li utilizzerà per la fase di pubblicazione.
- **Anki Core**: richiedi a Claude di esportare le sole carte core in un file di testo (TSV) per import diretto su Anki. Le carte non core possono restare nel thread o in un file secondario.

## Pubblicazione su Notion

Quando tutte le fasi sono completate puoi lanciare `pubblica notion`. Claude riutilizzerà gli output memorizzati nella conversazione per aggiornare la pagina esistente su Notion (via `notion-update-page`). Se preferisci evitare l'API, copia il markdown finale in Notion e applica manualmente le proprietà.

## Suggerimenti operativi

- Mantieni un'unica chat per workflow in modo che Claude conservi il contesto degli output.
- Se devi ricominciare da una fase specifica, usa `modifica` o ripeti il comando della fase: l'orchestrator vincola la progressione e impedisce salti non autorizzati.
- Per lavorare su più sbobine, duplica la chat o avvia nuove sessioni così da preservare gli stati separati.

Seguendo questi passaggi puoi utilizzare l'intero orchestrator direttamente online in Claude senza bisogno di esecuzione locale.
