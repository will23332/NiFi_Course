# Apache NiFi Crash Course

![LOGO](https://solace.com/wp-content/uploads/2017/05/nifi.jpg)

+++

A cura di Guglielmo Piacentini

---

## Che cos'è NiFi?

Un sistema di elaborazione e distribuzione dati, facile da usare e particolarmente versatile e potente.

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

---

## Core Concepts

| NiFi Term | Descrizione |
| `FlowFile` | A FlowFile represents each object moving through the system and for each one, NiFi keeps track of a map of key/value pair attribute strings and its associated content of zero or more bytes. |
| `Processor` | Processors actually perform the work. In [eip] terms a processor is doing some combination of data routing, transformation, or mediation between systems. Processors have access to attributes of a given FlowFile and its content stream. Processors can operate on zero or more FlowFiles in a given unit of work and either commit that work or rollback. |

+++

| `Connection` | Connections provide the actual linkage between processors. These act as queues and allow various processes to interact at differing rates. These queues can be prioritized dynamically and can have upper bounds on load, which enable back pressure. |
| `Controller` | The Flow Controller maintains the knowledge of how processes connect and manages the threads and allocations thereof which all processes use. The Flow Controller acts as the broker facilitating the exchange of FlowFiles between processors. |

+++

| `Process Group` | A Process Group is a specific set of processes and their connections, which can receive data via input ports and send data out via output ports. In this manner, process groups allow creation of entirely new components simply by composition of other components. |

---

## Let's get our hands dirty.
[DEMO](https://demo.elastic.co/app/kibana)

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

