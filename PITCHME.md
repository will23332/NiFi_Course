# Apache NiFi Crash Course

![LOGO](https://solace.com/wp-content/uploads/2017/05/nifi.jpg)

+++

A cura di Guglielmo Piacentini

---

## Che cos'è NiFi?

Un sistema di elaborazione e distribuzione dati, facile da usare e particolarmente versatile e potente. 

+++

Fa parte della piattaforma [Hortonforks HDF](https://it.hortonworks.com/products/data-platforms/hdf/)(Hortonworks DataFlow) per l'analisi di dati in tempo reale, composta da NiFi, MiNiFi, Kafka/Storm, Superset...  

+++

## Curiosità

Apache NiFi è un software della Apache Software Foundation basato sul software "NiagaraFiles" sviluppato dall' NSA.

---

## Caratteristiche

@ul

- Interfaccia Web-based
- Altamente configurabile (il flusso può essere modificato a runtime)
- Data Provenance 

@ulend

+++

@ul

- Estensibile (processori custom)
- Sicurezza (SSL, SSH, HTTPS)
- Rest API

@ulend

+++

## NiFi vs Kettle

@ul

- Kettle è un ETL batch-oriented (Informatica/Talend/Datastage)
- Kettle non è particolarmente indicato per lo streaming dati
- Kettle è una Java Runtime Machine, nativa in Hadoop come job MR o Yarn 
- NiFi è a modellazione interattiva, nessuna compilazione, modifiche a runtime

@ulend

+++

@ul

- NiFi Data Provenance
- NiFi ha clustering nativo, backpressure e flow control oltre che REST API
- Nifi non è principalmente per l'orchestrazione/batching ma per trasformazioni in stream

@ulend

---

## Core Concepts

| NiFi Term | Descrizione |
| --- | --- |
| `FlowFile` | Un FlowFile rappresenta ogni oggetto che si muove nel sistema. Per ognuno NiFi tiene traccia di una mappa chiave/valore per le stringhe degli attributi e il contenuto di zero o più bytes.|

+++

| NiFi Term | Descrizione |
| --- | --- |
| `Processor` | I processori sono il cuore del funzionamento. Un processore esegue una combinazione di operazioni (data routing, trasformazioni, mediazione tra sistemi). I processori hanno accesso agli attributi di un dato FlowFile.|

+++

| NiFi Term | Descrizione |
| --- | --- |
| `Connection` | Le connessioni permettono il collegamento tra i processori. Funzionano come code e permettono lo sviluppo dei processi con volumi differenti. Le code possono essere prioritizzate dinamicamente e avere limiti sul carico, permettendo la gestione della back pressure.|

+++

| NiFi Term | Descrizione |
| --- | --- |
| `Controller` | I Controller hanno al loro interno le informazioni su come i processi si connettono e sui thread che utilizzano.|

+++

| NiFi Term | Descrizione |
| --- | --- |
| `Process Group` | Un Process Group è un insieme di processi e connessioni, che può ricevere dati dalle porte di input o inviarne da quelle di output.|

---

## Hands On
[NiFi Cluster](http://10.121.172.33:32770/nifi/?processGroupId=25a99f06-016a-1000-ffff-ffffbb1fff81&componentIds=)

+++

## Processori Disponibili

@ul

- Data Transformation
- Routing and Mediation
- Database Access
- Attribute Extraction
- System Interaction
- Data Ingestion
- Splitting and Aggregation
- HTTP
- Amazon Web Services

@ulend

---

## Basic Architecture

![LOGO](https://www.tutorialspoint.com/apache_nifi/images/apache_web_server.jpg)

+++
NiFi stocca localmente tre repository su disco:

Flowfile Repository 

Contiene lo stato e gli attributi di ogni flowfile che attraversa il flusso NiFi.

+++

Content Repository

Contiene praticamente tutto il contenuto di tutti i flowfiles. Richiede spazio disco in relazione ai dati trattati, quindi ci si deve organizzare di conseguenza.

+++

Provenance Repository

Contiene tutti gli eventi relativi ai flowfiles. Sia volatile (dati persi dopo il restart) che persistente.

---

## REST API
NiFi espone delle [API](https://nifi.apache.org/docs/nifi-docs/rest-api/index.html) molto potenti che permettono di:

@ul
- Controllare configurazione dei controller, organizzare il cluster
- Amministrare il flow, ottenere lo status di componenti
- Creare processori, importare template 
- Gestire le code, porte di input, porte di output
@ulend

---

## Hands On pt. II

[NiFi Cluster](http://10.121.172.33:32770/nifi/?processGroupId=cf5b3636-758f-116a-8fc7-512e2dd418bc&componentIds=)

+++

## NiFi Expression Language

NiFi mette a disposizione un [linguaggio](https://nifi.apache.org/docs/nifi-docs/html/expression-language-guide.html) particolarmente versatile per le operazioni sui flowfile e i loro attributi.

---

# THE END

