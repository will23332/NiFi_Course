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
| `Controller` | The Flow Controller maintains the knowledge of how processes connect and manages the threads and allocations thereof which all processes use. The Flow Controller acts as the broker facilitating the exchange of FlowFiles between processors. |

+++

| NiFi Term | Descrizione |
| --- | --- |
| `Process Group` | A Process Group is a specific set of processes and their connections, which can receive data via input ports and send data out via output ports. In this manner, process groups allow creation of entirely new components simply by composition of other components. |

---

## Hands On
[DEMO](http://10.121.172.33:32770/nifi/)

---

## Installazione ES
[Link](https://www.elastic.co/downloads/elasticsearch)

+++
Creiamo un indice 
```
PUT /customer?pretty
```
Chiediamo a ES la lista degli indici
```
GET /_cat/indices?v
```
La risposta:
```
health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   customer 95SQ4TSUT7mWBT7VNHH67A   5   1          0            0       260b           260b
```
+++
Inseriamo un documento semplice
```
PUT /customer/_doc/1?pretty
{
  "name": "John Doe"
}
```

+++

La risposta:
```
{
  "_index" : "customer",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "result" : "created"
  [...]
}
```
+++

Ricerchiamo il documento appena inserito
```
GET /customer/_doc/1?pretty
```
La risposta:
```
{
  "_index" : "customer",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "found" : true,
  "_source" : { "name": "John Doe" }
}
```
+++
Eliminiamo l'indice creato
```
DELETE /customer?pretty
```
Richiamiamo la lista degli indici
```
GET /_cat/indices?v
```
La risposta sarà @color[#DC143C](vuota)

---

## Installazione Cerebro
[Link](https://github.com/lmenezes/cerebro)

Cerebro è una GUI che permette di interagire con Elasticsearch.

---

## REST API
Elastic espone delle API molto potenti che permettono di:

@ul
- Controllare lo stato di salute e le statistiche del cluster
- Amministrare il cluster, i nodi, gli indici, i dati e i metadati
- Performare CRUD (Create, Read, Update, and Delete) e operazioni di ricerca sugli indici
- Eseguire operazioni avanzate come paging, sorting, filtering, aggregazioni...
@ulend

---

## Hands On pt. II
Dataset [accounts.json](https://github.com/elastic/elasticsearch/blob/master/docs/src/test/resources/accounts.json?raw=true)

Bulk insert (anche tramite Cerebro):
```
curl -H "Content-Type: application/json" 
-XPOST "localhost:9200/bank/_doc/_bulk?pretty&refresh" 
--data-binary "@accounts.json"
```
+++
## La ricerca tramite URI
```
GET /bank/_search?q=*&sort=account_number:asc&pretty
```
+++
## Query DSL
La più semplice:
```
GET /bank/_search
{
  "query": { "match_all": {} }
}
```
+++
Sorting:
```
GET /bank/_search
{
  "query": { "match_all": {} },
  "sort": { "balance": { "order": "desc" } }
}
```
+++
Match Query:
```
GET /bank/_search
{
  "query": { "match": { "address": "mill lane" } }
}
```
+++
Filtri:
```
GET /bank/_search
{
  "query": {
    "bool": {
      "must": { "match_all": {} },
      "filter": {
        "range": {
          "balance": {
            "gte": 20000,
            "lte": 30000 }}}}}}
```

+++

Aggregazioni:
```
GET /bank/_search
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state.keyword"
      }
    }
  }
}
```
+++

Documentazione [Search API](https://www.elastic.co/guide/en/elasticsearch/reference/current/search.html)

Documentazione [Query DSL](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html)

---

## Use Case

Regione Veneto:

@[2-4](Settaggi del Cluster)
@[5-11](Dichiarazione Analyzer Custom)
@[14-17](Settaggi Analyzer)
@[21-29](Definizione del filtro custom dei sinonimi)
@[24](Path dove risiede il file dei sinonimi)
@[35-38](Mapping campo comuni)
@[40-42](Mapping campo province)

+++
Documenti contenuti:
```
{
	"index": {
		"_index": "luoghi-istat",
		"_type": "luogo"
	}
} 
{
	"nome_comune": "Ala di Stura",
	"nome_provincia": "",
	"sigla_provincia": "TO",
	"nome_regione": "Piemonte",
	"citta_metropolitana": "Torino",
	"codice_comune": 1003,
	"codice_provincia": 1,
	"codice_regione": 1,
	"codice_citta_metropolitana": 201
}
```
@[1-6](Prefisso Bulk Import)
@[7-16](Documento)
---
## Index Mapping
Elastic si fonda sul concetto di indice inverso:
- Un indice inverso consiste nella lista di tutte le parole non ripetute che appaiono in un documento e, per ognuna di queste, una lista dei documenti nelle quali appaiono.
- Questo permette una ricerca full-text particolarmente rapida.

+++

+ Analyzer

ES permette l'uso di diversi [analyzer](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-analyzers.html) out of the box, oltre che la possibilità di costruirne di custom.

+++
```
GET _analyze
{
  "analyzer" : "standard",
  "text" : "this is a test"
}
```
Altri casi di [test](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-analyze.html)

+++

## N.B. 
C'è una differenza sostianziale nella analisi a livello indice e l'analisi durante la ricerca.

Term Query VS Full-Text Query

More [here](https://madewithlove.be/basic-understanding-of-text-search/)

+++

---

# THE END

