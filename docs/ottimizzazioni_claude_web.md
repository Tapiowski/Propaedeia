# Valutazione attuale e ottimizzazioni proposte

## Stato attuale del progetto
- **Collocazione**: l'intero workflow risiede come progetto Claude Web. Gli unici artefatti locali sono `istruzioni.txt` (Custom Instructions) e `orchestrator.txt` (file consultato da Claude per seguire il flusso).
- **Esecuzione**: tutte le fasi (traccia, callout, pitch, proprietà, Anki, diagramma, pubblicazione Notion) vengono svolte nella chat, senza orchestratore eseguibile né storage locale.
- **Output**: la pagina viene costruita in memoria e poi copiata su Notion; le carte Anki devono essere esportate manualmente; l'utente desidera vedere in chat solo le carte Anki (core) come file di testo pronto all'import.

## Criticità osservate
1. **Controllo dello stato solo testuale** – l'orchestrator guida Claude fase per fase, ma non c'è un meccanismo che impedisca di saltare passaggi o di perdere output intermedi in caso di errore.
2. **Gestione manuale della pagina Notion** – l'utente teme la perdita di formattazione quando la pagina viene ricostruita in memoria e poi importata su Notion.
3. **Esportazione Anki core** – la richiesta è avere un file testo direttamente importabile con sole carte core, mantenendo le non-core opzionali e fuori dal file principale.

## Ottimizzazioni suggerite
### 1. Rafforzare l'orchestrator per il controllo “per step”
- Inserire all'interno di `orchestrator.txt` una sezione "Stato" che Claude aggiorna a ogni fase (es. tabella Markdown con colonne *Fase*, *Done*, *Output salvato*).
- Aggiungere prompt mirati che ricordino a Claude di confermare il completamento prima di procedere: «*Non passare alla fase successiva finché l'utente non conferma `continua`*».
- Suggerire l'uso della funzione **Scratchpad** di Claude (se disponibile) per mantenere in memoria la checklist delle fasi completate.

### 2. Preservare la formattazione Notion durante l'import
- Introdurre nel file orchestrator una macro «*Ricontrolla formattazione*» che chiede a Claude di validare i callout (icone/colori), i toggle (▶) e l'indentazione prima di generare il markdown finale.
- Quando si prepara il payload per Notion, far produrre due versioni: una in puro markdown per copia/incolla e una in JSON pseudo-strutturato con elenco dei blocchi, utile per eventuali automazioni future.
- Invitare Claude a eseguire un check finale: «*Se rilevi differenze tra la versione precedente e quella nuova, elencale prima dell'import su Notion*».

### 3. Automazione della consegna Anki core
- Aggiungere un passaggio esplicito nella fase 4 del workflow: «*Genera un file TSV denominato `anki_core_<slug>.txt` con colonne `Front`/`Back` e allegalo alla conversazione.*».
- Istruire Claude a collocare le carte non-core in una sezione separata della chat, marcandole come opzionali e fuori dal TSV principale.
- Inserire nelle istruzioni un reminder che le non-core non devono essere esportate se non su richiesta esplicita dell'utente.

### 4. Operatività Notion “in memoria”
- Suggerire di mantenere il documento markdown completo in un blocco dedicato della chat (es. "Pagina Notion pronta all'import"). Claude potrà poi usarlo per eventuali modifiche senza rigenerare tutto.
- Consigliare il salvataggio periodico della versione markdown in un file di backup interno al progetto Claude, così da evitare la perdita di contenuto in caso di reset della sessione.

### 5. Monitoraggio dei comandi e logging
- Proporre l'aggiunta di un "registro comandi" nel prompt d'orchestrazione: ogni volta che l'utente impartisce `continua`, `modifica`, `salta`, Claude aggiorna un elenco cronologico. Questo permette di ricostruire rapidamente il flusso in caso di imprevisti.
- Consigliare di includere, a fine workflow, un riepilogo automatico con tutte le fasi completate e i link ai blocchi rilevanti nella chat (callout, pitch, Anki, diagramma).

## Prossimi passi suggeriti
1. Aggiornare `istruzioni.txt` per includere i promemoria e i controlli aggiuntivi sopra elencati.
2. Revisionare `orchestrator.txt` introducendo la tabella di stato e i template per i file Notion/Anki.
3. Testare il workflow in Claude Web su una sbobina reale verificando che:
   - Ogni fase richieda conferma esplicita dell'utente.
   - Il file TSV venga generato e scaricato correttamente.
   - Il markdown finale mantenga la formattazione (callout, toggle, mermaid) una volta incollato in Notion.

Seguendo queste ottimizzazioni il workflow rimane interamente online ma guadagna robustezza, tracciabilità e un export Anki immediatamente utilizzabile.
