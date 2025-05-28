# Configurazione Progetti Java con Apache Tomcat in Visual Studio Code

## Indice

- [Descrizione](#descrizione)
- [Estensioni VSCode Necessarie](#estensioni-vscode-necessarie)
- [Configurazione Java](#configurazione-java)
- [Configurazione Tomcat](#configurazione-tomcat)
- [Parametri di Deployment](#parametri-di-deployment)
- [Riferimenti](#riferimenti)

## Descrizione

Guida **step-by-step** per configurare, compilare e distribuire progetti Java su **Apache Tomcat** utilizzando **Visual Studio Code**.



## Estensioni VSCode Necessarie

Installa le seguenti estensioni per abilitare il supporto completo Java e l’integrazione con Tomcat:

- [**Java Extension Pack**](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)  
  (fornisce tutto il necessario per sviluppare in Java)
- [**Community Server Connector**](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-community-server-connector)  
  (per gestire server come Tomcat direttamente da VSCode)
- [**Output Colorizer**](https://marketplace.visualstudio.com/items?itemName=IBM.output-colorizer) (opzionale): migliora la leggibilità dell’output nel terminale.


## Configurazione Java

### Pre-requisiti
Cancellare i file .classpath e .project se presenti nella root del progetto, per evitare conflitti con le nuove configurazioni.
Se VsCode non riconosce il progetto come Java, basta aprire un file `.java` e attendere che l'estensione Java lo riconosca e configuri automaticamente il progetto. 

### Impostazione del JDK

> ⚠️ **Nota:**  
> Il runtime RSP richiede almeno Java 11 per funzionare correttamente.  
> Tuttavia, il progetto Java può essere compilato ed eseguito con qualsiasi versione di Java desiderata:  
> la versione del JDK configurata per RSP **non influisce sul bytecode generato dal progetto**.

Puoi configurare il JDK in diversi modi:
- **Dalla GUI:**  
  Vai su *Project Settings* → **JDK Runtime** e seleziona il JDK desiderato.
  
  ![GUI JDK Runtime](resources/record/add_jdk.gif)
- **Da `settings.json` (consigliato per portabilità):**

  Apri il file *settings.json* del tuo progetto tramite la Command Palette *(Preferences: Open User Settings (JSON))* e aggiungi queste configurazioni:

  ```json
  "java.jdt.ls.java.home": "/percorso/alla/tua/JDK/Contents/Home",
  "java.configuration.runtimes": [
    {
      "name": "JavaSE-{Version}",
      "path": "/percorso/alla/tua/JDK/Contents/Home",
      "default": true
    }
  ],
  "java.project.referencedLibraries": [
    "WebContent/WEB-INF/lib/**/*.jar",
    "/path/di/apache-tomcat-X/lib/**/*.jar"
  ],
  "java.project.sourcePaths": [
    "src",
    "config/local"
  ],
  "java.project.outputPath": "WebContent/WEB-INF/classes",
  "java.dependency.autoRefresh": true,
  "java.autobuild.enabled": true,
  "rsp-ui.rsp.java.home": "/percorso/alla/tua/JDK/Contents/Home"
  ```

- Nota: Se lavori su più progetti che richiedono versioni diverse di Java, evita di impostare il JDK a livello utente nel *settings.json*.
In questi casi, è meglio configurare il JDK a livello di workspace (tramite *Preferences: Open Workspace Settings (JSON)* o tramite l’interfaccia grafica di VSCode) per mantenere coerenza e flessibilità tra i diversi progetti.


## Configurazione Tomcat

![Add Server](resources/record/add_server.gif)

1. **Scarica Tomcat** dal sito ufficiale: [Apache Tomcat](https://tomcat.apache.org/).
2. **Estrai l’archivio** in una cartella a tua scelta.
3. In VSCode, premi **F1** (Windows) o **CMD + SHIFT + P** (Mac) e cerca:  
   `Community Server: Create New Server`
    
    oppure
    
    clicca sull'icona del server nella barra laterale e seleziona `Create New Server`.

4. **Seleziona la cartella di Tomcat** scaricata.
5. *(Opzionale)*: Se richiesto, puoi settare `"vm.install.path"` nelle configurazioni del server Tomcat, serve a specificare il percorso della JVM (Java Home) da utilizzare per avviare quel particolare server.
   *(Tasto destro sul server → Edit Server)*  
   Se non configurato, verrà utilizzato automaticamente il percorso di `rsp-ui.rsp.java.home`.
  6. *(Opzionale)*: Se vuoi evitare che il pannello di output venga aperto automaticamente ogni volta che Tomcat scrive qualcosa, aggiungi questa riga al tuo `settings.json`:

   ```json
   "vscodeAdapters.showChannelOnServerOutput": false
   ```

7. **Aggiungi il deployment**:  
   Clicca su `Add Deployment` nel server e seleziona il file `.war` del progetto o la cartella `WebContent`.

## Configurazione Classpath
Per aggiungere le librerie necessarie al classpath del progetto, aggiornare la variabile `java.project.referencedLibraries` nel file `settings.json` del progetto. Esempio:

```json
"java.project.referencedLibraries": [
  "WebContent/WEB-INF/lib/**/*.jar",
  "/path/di/apache-tomcat-X/lib/**/*.jar"
  "/path/delle/librerie/aggiuntive/**/*.jar"
]
```


## Parametri di Deployment

Durante il deployment, VSCode può chiederti di configurare parametri avanzati:

1. **Nome del deployment:**  
  Il nome che verrà utilizzato nell’URL di accesso all’applicazione, ad esempio **http://.../{Nome}**.  
    *(Lascia vuoto per utilizzare il nome della cartella del deployment.)*

2. **Configurazione avanzata (file di assembly):**  
    > Non è necessario creare questo file se non hai esigenze particolari di configurazione del deployment.
    - Se esiste `.rsp/rsp.assembly.json` nella root del progetto, **lascia il campo vuoto** per auto-rilevamento.
    - Se vuoi usare un file diverso (nome o percorso), specifica qui il path relativo.
    - Se non vuoi configurazioni avanzate, lascia vuoto: sarà usato il comportamento di default.

    Esempio di `.rsp/rsp.assembly.json`:

    ```json
    {
      "mappings": [
        {
          "source-path": "target/classes/",
          "deploy-path": "/WEB-INF/classes/"
        },
        {
          "source-path": "src/main/webapp/",
          "deploy-path": "/"
        }
      ]
    }
    ```

    Questo file permette di definire come verranno assemblati e copiati i file dalla struttura del progetto al deploy finale.


## Riferimenti

- [Documentazione ufficiale Red Hat RSP-UI](https://github.com/redhat-developer/vscode-rsp-ui)
- [Apache Tomcat](https://tomcat.apache.org/)
- [Java Extension Pack](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
- [Community Server Connector](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-community-server-connector)
- [Output Colorizer](https://marketplace.visualstudio.com/items?itemName=IBM.output-colorizer)

---

**Nota:**  
>Questa guida è pensata esclusivamente per un setup rapido e semplificato di applicazioni Java su Tomcat usando VSCode.  
>Per configurazioni più avanzate e personalizzate, si consiglia di fare sempre riferimento alla documentazione ufficiale delle estensioni e degli strumenti utilizzati.