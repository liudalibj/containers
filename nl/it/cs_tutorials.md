---

copyright:
  years: 2014, 2019
lastupdated: "2019-03-21"

keywords: kubernetes, iks

subcollection: containers

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}
{:download: .download}


# Esercitazione: Creazione dei cluster Kubernetes
{: #cs_cluster_tutorial}

Con questa esercitazione, puoi distribuire e gestire un cluster Kubernetes in {{site.data.keyword.containerlong}}. Impara come automatizzare la distribuzione, l'operatività, il ridimensionamento e il monitoraggio delle applicazioni inserite in un contenitore in un cluster.
{:shortdesc}

In questa serie di esercitazioni, puoi vedere come un'agenzia di PR fittizia utilizza le funzionalità Kubernetes
per distribuire un'applicazione in un contenitore in {{site.data.keyword.Bluemix_notm}}. Utilizzo di {{site.data.keyword.toneanalyzerfull}}, l'agenzia di PR analizza i propri comunicati stampa e riceve i feedback.


## Obiettivi
{: #tutorials_objectives}

In questa prima esercitazione, agisci come l'amministratore di rete della società di PR. Configuri un cluster Kubernetes personalizzato utilizzato per distribuire e testare una versione Hello World dell'applicazione in {{site.data.keyword.containerlong_notm}}.
{:shortdesc}

-   Crea un cluster con 1 pool di nodi di lavoro che abbia 1 nodo di lavoro.
-   Installa le CLI per eseguire i [comandi Kubernetes ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://kubernetes.io/docs/reference/kubectl/overview/) e gestire le immagini Docker in {{site.data.keyword.registrylong_notm}}.
-   Crea un repository delle immagini privato in {{site.data.keyword.registrylong_notm}} per archiviare le tue immagini.
-   Aggiungi il servizio {{site.data.keyword.toneanalyzershort}} al cluster in modo che ogni applicazione nel cluster possa utilizzare quel servizio.


## Tempo richiesto
{: #tutorials_time}

40 minuti


## Destinatari
{: #tutorials_audience}

Questa esercitazione è destinata agli sviluppatori software e agli amministratori di rete che stanno creando un cluster Kubernetes per la prima volta.
{: shortdesc}

## Prerequisiti
{: #tutorials_prereqs}

-  Controlla i passi che devi seguire per [prepararti a creare un cluster](/docs/containers?topic=containers-clusters#cluster_prepare).
-  Assicurati di disporre delle seguenti politiche di accesso:
    - [**Amministratore** come ruolo della piattaforma {{site.data.keyword.Bluemix_notm}} IAM](/docs/containers?topic=containers-users#platform) per {{site.data.keyword.containerlong_notm}}
    - [**Scrittore** o **Gestore** come ruolo del servizio {{site.data.keyword.Bluemix_notm}} IAM](/docs/containers?topic=containers-users#platform) per {{site.data.keyword.containerlong_notm}}


## Lezione 1: Creazione di un cluster e configurazione della CLI
{: #cs_cluster_tutorial_lesson1}

Crea il tuo cluster Kubernetes nella console {{site.data.keyword.Bluemix_notm}} e installa le CLI richieste.
{: shortdesc}

**Per creare il tuo cluster**

Poiché servono alcuni minuti per eseguire il provisioning, crea il tuo cluster prima di installare le CLI.

1.  [Nella console {{site.data.keyword.Bluemix_notm}} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://cloud.ibm.com/containers-kubernetes/catalog/cluster/create), crea un cluster gratuito o standard con 1 pool di nodi di lavoro contenente 1 nodo di lavoro.

    Puoi anche creare un [cluster nella CLI](/docs/containers?topic=containers-clusters#clusters_cli).
    {: tip}

Come viene eseguito il provisioning del tuo cluster, installa le seguenti CLI che vengono utilizzate per gestire i cluster:
-   CLI {{site.data.keyword.Bluemix_notm}}
-   Plugin {{site.data.keyword.containerlong_notm}}
-   CLI Kubernetes
-   Plugin {{site.data.keyword.registryshort_notm}}



</br>
**Per installare le CLI e i loro prerequisiti**

1.  Come prerequisito per il plug-in {{site.data.keyword.containerlong_notm}}, installa la [CLI {{site.data.keyword.Bluemix_notm}} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](/docs/cli?topic=cloud-cli-ibmcloud-cli#ibmcloud-cli). Per eseguire i comandi CLI {{site.data.keyword.Bluemix_notm}}, utilizza il prefisso `ibmcloud`.
2.  Segui le istruzioni per selezionare un account e un'organizzazione {{site.data.keyword.Bluemix_notm}}. I cluster sono specifici di un account, ma sono indipendenti da un'organizzazione o uno spazio {{site.data.keyword.Bluemix_notm}}.

4.  Installa il plug-in {{site.data.keyword.containerlong_notm}} per creare i cluster Kubernetes e gestire i nodi di lavoro. Per eseguire i comandi del plug-in {{site.data.keyword.containerlong_notm}}, utilizza il prefisso `ibmcloud ks`.

    ```
    ibmcloud plugin install container-service -r Bluemix
    ```
    {: pre}

5.  Per distribuire le applicazioni nei tuoi cluster, [installa la CLI Kubernetes ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://kubernetes.io/docs/tasks/tools/install-kubectl/). Per eseguire i comandi utilizzando la CLI Kubernetes, utilizza il prefisso `kubectl`.
    1.  Per una compatibilità funzionale completa, scarica la versione della CLI Kubernetes che corrisponde alla versione del cluster Kubernetes che vuoi utilizzare. L'attuale versione Kubernetes predefinita per {{site.data.keyword.containerlong_notm}} è la 1.12.6.

        OS X:   [https://storage.googleapis.com/kubernetes-release/release/v1.12.6/bin/darwin/amd64/kubectl ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://storage.googleapis.com/kubernetes-release/release/v1.12.6/bin/darwin/amd64/kubectl)

        Linux:   [https://storage.googleapis.com/kubernetes-release/release/v1.12.6/bin/linux/amd64/kubectl ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://storage.googleapis.com/kubernetes-release/release/v1.12.6/bin/linux/amd64/kubectl)

        Windows:   [https://storage.googleapis.com/kubernetes-release/release/v1.12.6/bin/windows/amd64/kubectl.exe ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://storage.googleapis.com/kubernetes-release/release/v1.12.6/bin/windows/amd64/kubectl.exe)

          **Suggerimento:** se stai utilizzando Windows, installa la CLI Kubernetes nella stessa directory della CLI {{site.data.keyword.Bluemix_notm}}. Questa configurazione ti consente di ridurre le modifiche al percorso file quando esegui i comandi in un secondo momento.

    2.  Se stai utilizzando OS X o Linux, completa la seguente procedura.
        1.  Sposta il file eseguibile nella directory `/usr/local/bin`.

            ```
            mv filepath/kubectl /usr/local/bin/kubectl
            ```
            {: pre}

        2.  Assicurati che `/usr/local/bin` sia elencato nella tua variabile di sistema `PATH`. La variabile `PATH` contiene tutte le directory in cui il tuo sistema operativo
può trovare i file eseguibili. Le directory elencate nella variabile `PATH`
servono a diversi scopi. `/usr/local/bin` viene utilizzata per memorizzare i file eseguibili
per il software che non fa parte del sistema operativo e che viene installato manualmente dall'amministratore
di sistema.

            ```
            echo $PATH
            ```
            {: pre}

            L'output della CLI
sarà simile al seguente.

            ```
            /usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
            ```
            {: screen}

        3.  Rendi il file eseguibile.

            ```
            chmod +x /usr/local/bin/kubectl
            ```
            {: pre}

6. Per configurare e gestire un repository delle immagini privato in {{site.data.keyword.registryshort_notm}}, installa il plugin
{{site.data.keyword.registryshort_notm}}. Per eseguire i comandi del registro, utilizza il prefisso `ibmcloud cr`.

    ```
    ibmcloud plugin install container-registry -r Bluemix
    ```
    {: pre}

    Per verificare che i plugin container-service e container-registry siano installati correttamente, esegui il seguente comando:

    ```
    ibmcloud plugin list
    ```
    {: pre}

Ottimo lavoro! Hai installato correttamente le CLI per le seguenti esercitazioni e lezioni. Successivamente, configura il tuo ambiente cluster e aggiungi il servizio {{site.data.keyword.toneanalyzershort}}.


## Lezione 2: Configurazione del tuo registro privato
{: #cs_cluster_tutorial_lesson2}

Configura un repository delle immagini privato in {{site.data.keyword.registryshort_notm}} e aggiungi i segreti al tuo cluster Kubernetes
in modo che l'applicazione possa accedere al servizio {{site.data.keyword.toneanalyzershort}}.
{: shortdesc}

1.  Accedi alla CLI {{site.data.keyword.Bluemix_notm}} utilizzando le tue credenziali {{site.data.keyword.Bluemix_notm}}, quando richiesto.

    ```
    ibmcloud login [--sso]
    ```
    {: pre}

    Se disponi di un ID federato, utilizza l'indicatore `--sso` per accedere. Immetti il tuo nome utente e usa l'URL fornito nell'output della CLI per richiamare la tua passcode monouso.
    {: tip}

2.  Se il cluster si trova in un gruppo di risorse diverso da quello di `default`, specifica tale gruppo di risorse. Per visualizzare il gruppo di risorse a cui appartiene ciascun cluster, esegui `ibmcloud ks clusters`.
   ```
   ibmcloud target -g <resource_group_name>
   ```
   {: pre}

3.  Configura il tuo proprio repository delle immagini privato in {{site.data.keyword.registryshort_notm}} per condividere ed archiviare in modo sicuro le immagini Docker
con tutti gli utenti del cluster. Un repository delle immagini privato in {{site.data.keyword.Bluemix_notm}} viene
identificato da uno spazio dei nomi. Lo spazio dei nomi viene utilizzato per creare un URL univoco per il tuo repository delle immagini
che gli sviluppatori possono utilizzare per accedere alle immagini Docker private.

    Ulteriori informazioni sulla [protezione delle tue informazioni personali](/docs/containers?topic=containers-security#pi) quando utilizzi le immagini del contenitore.

    In questo esempio, l'agenzia di PR vuole creare solo
un repository delle immagini in {{site.data.keyword.registryshort_notm}}, per cui sceglie
`agenzia_PR` come proprio spazio dei nomi per raggruppare tutte le immagini nel suo account. Sostituisci &lt;namespace&gt; con uno spazio dei nomi di tua scelta che non sia correlato all'esercitazione.

    ```
    ibmcloud cr namespace-add <namespace>
    ```
    {: pre}

4.  Prima di continuare con il prossimo passo, verifica che la distribuzione del tuo nodo di lavoro sia completa.

    ```
    ibmcloud ks workers --cluster <cluster_name_or_ID>
    ```
    {: pre}

    Quando è finito il provisioning del tuo nodo di lavoro, lo stato viene modificato in **Pronto** e puoi iniziare il bind dei servizi {{site.data.keyword.Bluemix_notm}}.

    ```
    ID                                                 Public IP       Private IP       Machine Type   State    Status   Zone   Version
    kube-mil01-pafe24f557f070463caf9e31ecf2d96625-w1   169.xx.xxx.xxx   10.xxx.xx.xxx   free           normal   Ready    mil01      1.12.6
    ```
    {: screen}

## Lezione 3: Configurazione del tuo ambiente cluster
{: #cs_cluster_tutorial_lesson3}

Imposta il contesto per il tuo cluster Kubernetes nella tua CLI.
{: shortdesc}

Ogni volta che accedi alla tua CLI {{site.data.keyword.containerlong}} per utilizzare i cluster, devi eseguire questi comandi
per configurare il percorso al file di configurazione del cluster come una variabile di sessione. La CLI Kubernetes
utilizza questa variabile per trovare i certificati e un file di configurazione locale necessari per il collegamento con il
cluster in {{site.data.keyword.Bluemix_notm}}.

1.  Richiama il comando per impostare la variabile di ambiente e scaricare i file di configurazione Kubernetes.

    ```
    ibmcloud ks cluster-config --cluster <cluster_name_or_ID>
    ```
    {: pre}

    Quando il download dei file di configurazione è terminato, viene visualizzato un comando che puoi utilizzare per impostare il percorso al file di configurazione di Kubernetes locale come una variabile di ambiente.

    Esempio per OS X:

    ```
    export KUBECONFIG=/Users/<user_name>/.bluemix/plugins/container-service/clusters/pr_firm_cluster/kube-config-prod-par02-pr_firm_cluster.yml
    ```
    {: screen}

2.  Copia e incolla il comando visualizzato nel tuo terminale per impostare la variabile di ambiente `KUBECONFIG`.

    **Utenti Windows PowerShell**: invece di copiare e incollare il comando `SET` dall'output di `ibmcloud ks cluster-config`, devi impostare la variabile di ambiente `KUBECONFIG` eseguendo, ad esempio, `$env:KUBECONFIG = "C:\Users\<user_name>\.bluemix\plugins\container-service\clusters\mycluster\kube-config-prod-dal10-mycluster.yml"`.
        {: note}

3.  Verifica che la variabile di ambiente `KUBECONFIG` sia impostata correttamente.

    Esempio per OS X:

    ```
    echo $KUBECONFIG
    ```
    {: pre}

    Output:

    ```
    /Users/<user_name>/.bluemix/plugins/container-service/clusters/pr_firm_cluster/kube-config-prod-par02-pr_firm_cluster.yml
    ```
    {: screen}

4.  Verifica che i comandi `kubectl` siano eseguiti correttamente con il tuo cluster controllando la versione server della CLI
Kubernetes.

    ```
    kubectl version --short
    ```
    {: pre}

    Output di esempio:

    ```
    Client Version: v1.12.6
    Server Version: v1.12.6
    ```
    {: screen}

## Lezione 4: Aggiunta di un servizio al tuo cluster
{: #cs_cluster_tutorial_lesson4}

Con i servizi {{site.data.keyword.Bluemix_notm}},
puoi approfittare della funzionalità già sviluppata nelle tue applicazioni. Qualsiasi servizio {{site.data.keyword.Bluemix_notm}} associato al
cluster Kubernetes può essere utilizzato da qualsiasi applicazione distribuita in tale cluster. Ripeti i seguenti passi per ogni
servizio {{site.data.keyword.Bluemix_notm}} che desideri utilizzare con le tue applicazioni.
{: shortdesc}

1.  Aggiungi il servizio {{site.data.keyword.toneanalyzershort}} al tuo account {{site.data.keyword.Bluemix_notm}}. Sostituisci <service_name> con un nome per la tua istanza di servizio.

    Quando aggiungi il servizio {{site.data.keyword.toneanalyzershort}} al tuo account, viene visualizzato un messaggio che indica che il servizio non è gratuito. Se limiti la tua chiamata API, per questa esercitazione non sarà addebitato nulla dal tuo servizio {{site.data.keyword.watson}}. [Rivedi le informazioni sul prezzo per il servizio {{site.data.keyword.watson}} {{site.data.keyword.toneanalyzershort}} ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://cloud.ibm.com/catalog/services/tone-analyzer).
    {: note}

    ```
    ibmcloud service create tone_analyzer standard <service_name>
    ```
    {: pre}

2.  Esegui il bind dell'istanza {{site.data.keyword.toneanalyzershort}} allo spazio dei nomi Kubernetes `default` del cluster. Successivamente, puoi creare i tuoi propri spazi dei nomi per gestire l'accesso utente alle risorse Kubernetes, ma per ora, utilizza lo spazio dei nomi `default`. Gli spazi dei nomi Kubernetes
sono diversi dallo spazio dei nomi del registro che hai precedentemente creato

    ```
    ibmcloud ks cluster-service-bind <cluster_name> default <service_name>
    ```
    {: pre}

    Output:

    ```
    ibmcloud ks cluster-service-bind pr_firm_cluster default mytoneanalyzer
    Binding service instance to namespace...
    OK
    Namespace:	default
    Secret name:	binding-mytoneanalyzer
    ```
    {: screen}

3.  Verifica che il segreto Kubernetes sia stato creato nel tuo spazio dei nomi del cluster. Ogni servizio {{site.data.keyword.Bluemix_notm}} è definito da un file JSON che include informazioni riservate come la chiave API {{site.data.keyword.Bluemix_notm}} IAM (Identity and Access Management) e l'URL utilizzato dal contenitore per ottenere l'accesso. Per archiviare in modo sicuro queste informazioni, vengono utilizzati i segreti
Kubernetes. In questo esempio, il segreto include la chiave API per l'accesso all'istanza di {{site.data.keyword.watson}} {{site.data.keyword.toneanalyzershort}} di cui è stato eseguito il provisioning nel tuo account.

    ```
    kubectl get secrets --namespace=default
    ```
    {: pre}

    Output:

    ```
    NAME                       TYPE                                  DATA      AGE
    binding-mytoneanalyzer     Opaque                                1         1m
    bluemix-default-secret     kubernetes.io/dockercfg               1         1h
    default-token-kf97z        kubernetes.io/service-account-token   3         1h
    ```
    {: screen}

</br>
Ottimo lavoro! Il tuo cluster è configurato e il tuo ambiente locale è pronto per iniziare a distribuire le applicazioni nel cluster.

## Operazioni successive
{: #tutorials_next}

* Verifica le tue conoscenze e [fai un quiz ![Icona link esterno](../icons/launch-glyph.svg "Icona link esterno")](https://ibmcloud-quizzes.mybluemix.net/containers/cluster_tutorial/quiz.php)!
* Prova l'[Esercitazione: Distribuzione delle applicazioni nei cluster Kubernetes](/docs/containers?topic=containers-cs_apps_tutorial#cs_apps_tutorial) per distribuire l'applicazione dell'agenzia di PR
nel cluster che hai creato.
