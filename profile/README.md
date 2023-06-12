## MicroAlgae DB

<!--

**Here are some ideas to get you started:**

🙋‍♀️ A short introduction - what is your organization all about?
🌈 Contribution guidelines - how can the community get involved?
👩‍💻 Useful resources - where can the community find your docs? Is there anything else the community should know?
🍿 Fun facts - what does your team eat for breakfast?
🧙 Remember, you can do mighty things with the power of [Markdown](https://docs.github.com/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
-->

# MicroAlgae Parsing & Searching

Credits
[`federico-rosatelli`](https://github.com/federico-rosatelli) [`Mat`](https://github.com/AxnNxs) [`Loriv3`](https://github.com/Loriv3) [`Samsey`](https://github.com/Samseys) [`Calli`](https://github.com/BboyCaligola)

# Requisiti:

Biopython:            "https://biopython.org/wiki/Documentation"

Pymongo:              "https://pymongo.readthedocs.io/en/stable/"

MongoDB CE "Jammy":   "https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/"

NPM:                  "https://docs.npmjs.com/downloading-and-installing-node-js-and-npm"

GoLang:               "https://go.dev/doc/install"

Docker:               "https://docs.docker.com/engine/install/ubuntu/"

Docker compose:       "https://docs.docker.com/desktop/install/linux-install/"


# Fonti ufficiali:


NCBI:                 "https://www.ncbi.nlm.nih.gov/"    DB americano

EBI:                  "https://www.ebi.ac.uk/"           DB europeo

# Struttura del Progetto:
Il progetto è composto dai seguenti quattro moduli:

- Database: MongoDB
- Parser:   BioPyParse (Python)
- Backend:  BioServer (Go)
- Frontend: Vulgaris Platform (Vue.js)

# Guida al primo utilizzo
Per la prima installazione su un qualsiasi PC Linux, seguire i seguenti passaggi:

[TODO] Docker image [TODO] 
- Scaricare/clonare il progetto tramite git alla repository "https://github.com/federico-rosatelli/biologia"
- Installare tutti i Requisiti Software, settando, se necessario, le variabili di ambiente a livello globale
- Inizializzare MongoDB. Dare i seguenti comandi da terminale per evitare problemi di permessi di scrittura:
  
    ``sudo chown -R mongodb:mongodb /var/lib/mongodb``          Setting di permessi
        
    ``sudo systemctl start mongod``                             Avviare MongoDB
        
    ``sudo systemctl enable mongod.service``                    Per rendere automatico l'avvio
        
- [DEPRECATED] ~~runnare lo script genbank.py, specificando un file .gbk sorgente e selezionando il tipo di database desiderato (-n per MongoDB, -s per SQLite3)~~ [COMMENT]: # La maggior parte dei dati presenti nel Database del progetto sono stati scaricati manualmente a causa delle restrizioni sui
download da parte delle piattaforme dei Riferimenti Ufficiali. Tali metodi sono elencati in parsingMethods.py. In seguito, si é proceduto a eliminare le voci che non erano interesse del progetto,come per esempio il ramo tassonomico dei Bacteria, intervenendo a livello di DB interno con i comandi forniti da MongoDB, effettuando query dei dati tramite regex e ranking tassonomico.

- MongoDB conserva i dati implicitamente in memoria. Effettuare il download dei dump del DB presenti al link [INSERIRELINK!], decomprimere gli archivi e lanciare, per ogni cartella decompressa:

    ``mongorestore --db=Biologia <path_to_folder_extracted>``
    
    Da questo momento é possibile consultare il DB da terminale.


- Scaricare i dati mancanti di Genome insieme alle analisi prodotte dal software BUSCO dal link [INSERIRELINK!]
- Scaricare i dati mancanti di TSA ed SRA eseguendo lo script [INSERIRELINK!]



- [DEPRECATED] ~~Aprire il server, digitando su console all'interno della cartella nodeServer:~~
    ~~``npm install --save express`` per il primo avvio;~~
    ~~``node server.js``~~ [COMMENT]: # Il backend è stato sostituito da una versione più completa e consistente scritta in GoLang.

- Inizializzare il server, digitando, all'interno della cartella GOServer:

    ``go build ./cmd/webapi/``                                  Per avviare il backend

    ``run npm dev``                                             Per avviare il frontend


- Ora é possibile consultare il DB da terminale. Si riportano alcuni comandi utili allo scopo:
    
    ``mongosh``                                                 Per utilizzare MongoDB
    ``show dbs``                                                Per visualizzare i DB attivi
    ``use Biologia``                                            Per entrare nel DB creato durante il mongorestore
    ``show collections``                                        Per visualizzare le collezioni di elementi del DB
    ``db.<collection_name>.find()``                             Query che ritorna tutte le occorrenze se vuota
    ``db.<collection_name>.findOne()``                          Query che ritorna la prima occorrenza dell'input



- Ora é possibile consultare il DB da interfaccia web al seguente indirizzo:

    ``http://localhost:5173``



# Dati esterni al DB:
Per motivi di performance e trattamento di grandi moli di dati, si é deciso di:

- Per la collection nucleotide_data, le specie a cui fanno riferimento più di 10.000 entry su NCBI sono state tagliate.
Ciò perché NCBI limita fortemente la velocità di download e a livello prettamente numerico il peso del DB locale sarebbe
salito di un ordine di grandezza. Le voci sono incomplete se al numero di occorrenze viene accodato un "+" nella view
di nucleotide consultabile da interfaccia web. Le voci mancanti possono essere inserite, per singola specie, utilizzando
le funzioni efetch ed esearch di biopython e incrementando il parametro retstart al precedente retmax (il massimo è appunto
10.000). Un ulteriore requisito é essere registrati su NCBI e inserire le proprie credenziali per fare le consultazioni:

    ``Entrez.email = <email_registered_on_NCBI>``
    ``Entrez.api_key = "cc030996838fc52dd1a2653fad76bf5fe408"``

# Le collections trattate: schema e specifiche
Data e Complete collections (contenenti la maggior parte dei dati):

- `nucleotide_data`       contiene tutti i dati che sono riscontrabili su NCBI alla voce Nucleotide;
- `taxonomy_data`         contiene tutti i dati che sono riscontrabili su NCBI alla sezione Taxonomy;
- `protein_data`          contiene tutti i dati che sono riscontrabili su NCBI alla sezione Protein;

Basic collections (per effettuare query ricorrenti ad alte performance):
  
- `table_basic`           contiene i dati, allegeriti soltanto a nome e NCBI_ID, per questioni di performance quando si effettuano query di conteggio;
- `table_complete`        contiene i dati per questioni di performance quando si effettuano query di conteggio;
- `taxonomy_tree`         contiene i link di Lineage per singola specie, generata su base di taxonomy_data con generateTaxonomyTree() del modulo BioPyParse;

# `taxonomy_data`

View sulla struttura ottenuta con comando del tipo (i.e. per Chlorella vulgaris):
``db.taxonomy_data.findOne({ScientificName:"Chlorella vulgaris"})``
```json
{
	"TaxID"			: "string",
	"ScientificName"	: "string",
	"ParentTaxID"		: "string",
	"Rank"			: "string",
	"Divisio"		: "string",
	"GeneticCode":{
		"GCID"		: "string",
		"GCName"	: "string"
	},
	"MitoGeneticCode":{
		"MGCID"		: "string",
		"MGCName"	: "string"
	},
	"Lineage"	: "string",
	"LineageEx": [
		{
			"TaxID"			: "string",
			"ScientificName"	: "string",
			"Rank"			: "string",
		},
	]
	"CreateDate"	: "string",
	"UpdateDate"	: "string",
	"PubDate"	: "string"
}
```

# `nucleotide_data`

View sulla struttura ottenuta con comando del tipo (i.e. per Chlorella vulgaris):
``db.nucleotide_data.findOne({GBSeq_organism:"Chlorella vulgaris"})``
```json
{
	"GBSeqAccessionVersion": "string",
	"GBSeqComment"         : "string",
	"GBSeqCreateDate"      : "string",
	"GBSeqDefinition"      : "string",
	"GBSeqDivision"        : "string",
	"GBSeqFeatureTable"    : [
		{
		    "GBFeatureIntervals": {
			"GBIntervalAccession": "string",
			"GBIntervalFrom"     : "string",
			"GBIntervalTo"       : "string"
		    },
		    "GBFeatureKey"     : "string",
		    "GBFeatureLocation": "string",
		    "GBFeatureQuals": [
			{
			    "GBQualifierName" : "string",
			    "GBQualifierValue": "string"
			}
		    ],
		    "GBFeaturePartial3": "string",
		    "GBFeaturePartial5": "string"
		}
	],
	"GBSeqLength"          : "string",
	"GBSeqLocus"           : "string",
	"GBSeqMoltype"         : "string",
	"GBSeqOrganism"        : "string",
	"GBSeqOtherSeqids"     : ["string"],
	"GBSeqPrimaryAccession": "string",
	"GBSeqReferences"      : [
		{
		    "GBReferenceAuthors"  : ["string"],
		    "GBReferenceJournal"  : "string",
		    "GBReferencePosition" : "string",
		    "GBReferenceReference": "string",
		    "GBReferenceTitle"    : "string"
		}
    	],
	"GBSeqSource"      : "string",
	"GBSeqStrandedness": "string",
	"GBSeqTaxonomy"    : "string",
	"GBSeqTopology"    : "string",
	"GBSeqUpdateDate"  : "string"
}
```

# `protein_data`

View sulla struttura ottenuta con comando del tipo (i.e. per Chlorella vulgaris):
``db.protein_data.findOne({GBSeq_organism:"Chlorella vulgaris"})``
```json
{
	"GBSeqAccessionVersion"	: "string",
	"GBSeqComment"		: "string",
	"GBSeqCreateDate"	: "string",
	"GBSeqDefinition"	: "string",
	"GBSeqDivision"		: "string",
	"GBSeqFeatureTable"	: [
		{
			"GBFeatureIntervals": {
				"GBIntervalAccession"	: "string",
				"GBIntervalFrom"      	: "string",
				"GBIntervalTo"		: "string"
			},
			"GBFeatureKey"		: "string",
			"GBFeatureLocation"	: "string",
			"GBFeatureQuals"	: [
			{
				"GBQualifierName"	: "string",
				"GBQualifierValue"	: "string"
			}
			],
			"GBFeaturePartial3"	: "string",
			"GBFeaturePartial5"	: "string"
		}
	],
	"GBSeqLength"		: "string",
	"GBSeqLocus"            : "string",
	"GBSeqMoltype"		: "string",
	"GBSeqOrganism"        	: "string",
	"GBSeqOtherSeqids"	: "string",
	"GBSeqPrimaryAccession" : "string",
	"GBSeqReferences"	: [
		{
			"GBReferenceAuthors"	: ["string"],
			"GBReferenceJournal"	: "string",
			"GBReferencePosition"	: "string",
			"GBReferenceReference"	: "string",
			"GBReferenceTitle"	: "string"
		}
	],
	"GBSeqSource"		: "string",
	"GBSeqStrandedness"	: "string",
	"GBSeqTaxonomy"		: "string",
	"GBSeqTopology"		: "string",
	"GBSeqUpdateDate"	: "string"
}
```

NB: Struttura identica a nucleotide_data

# `table_basic`

View sulla struttura ottenuta con comando del tipo (i.e. per Chlorella vulgaris):
``db.table_basic.findOne({ScientificName:"Chlorella vulgaris"})``
```json
{
	"ScientificName"	: "string",
	"TaxId"			: "string",
	"Nucleotides":		[
		{
			"GBSeq_locus"	: "string"
		}
	],
	"Proteins"	: [
		{
			"GBSeq_locus" : "string"
		}
	],
	"Products"	: [
		{
			"ProductName"	: "string",
			"QtyProduct"	: "string"
		}
	],
	"Country"	: [
		{
			"CountryName" : "string"
		}
	]
}
```


# `table_complete`

View sulla struttura ottenuta con comando del tipo (i.e. per Chlorella vulgaris):
``db.table_complete.findOne({ScientificName:"Chlorella vulgaris"})``
```json
{
	"ScientificName"	: "string",
	"TaxId"			: "string",
	"Nucleotides"		: [
		{
			"SameAs"	: "nucleotide_data"
		}
	],
	"Proteins"	: [
		{
			"SameAs"	: "protein_data"
		}
	],
	"Products"	: [
		{
			"ProductName"	: "string",
			"QtyProduct"	: "string"
		}
	],
	"Country"	: [
		{
			"CountryName"	: "string"
		}
	]
}
```

Nota: Nucleotides e Proteins contengono una struttura uguale rispettivamente a nucleotide_data e protein_data.

# `taxonomy_tree`

View sulla struttura ottenuta con comando del tipo (i.e. per Chlorella vulgaris):
``db.taxonomy_tree.findOne({TaxId:"3077"})``
```json
{
	"TaxId"			: "string",
	"Rank"			: "string",
	"ScientificName"	: "string",
	"SubClasses"		: [
		{
			"TaxId"			: "string",
			"Rank"			: "string",
			"ScientificName"	: "string"
		}
	]
}
```
