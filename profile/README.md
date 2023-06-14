# MicroAlgae DB
[![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)](https://docs.docker.com/engine/install/ubuntu/)

## Credits
[`federico-rosatelli`](https://github.com/federico-rosatelli)
[`Mat`](https://github.com/AxnNxs)
[`Loriv3`](https://github.com/Loriv3)
[`Samsey`](https://github.com/Samseys)
[`Calli`](https://github.com/BboyCaligola)

## Sources:
- [NCBI](https://www.ncbi.nlm.nih.gov/)
- [EBI](https://www.ebi.ac.uk/)

# Project structure:
The project consists of the following four modules:

- Database: MongoDB
- Parser:   [BioPyParse](https://github.com/BITSapienza/biopyparse) (Python)
- Backend:  [BioServer](https://github.com/BITSapienza/Bio-Server) (GoLang)
- Frontend: [Vulgaris Platform](https://github.com/BITSapienza/Vulgaris-Platform) (Vue.js)

# Main goals
## Description
This is a platform of microalgae with cross-referencing functionality, linking data from the most important biologic databases. It a database for consulting taxonomic, nucleotide and protein data on over 900+ species of microalgae. 
- with coal capture properties;

## Solved Issues
Consulting data is now possible in an easier way: you can search for a species and what genes are related to it, what Bioprojects and Experiments have been carried out, and it provides references to raw data that can be passed through the major analysis tools, such as [Busco](https://busco.ezlab.org/), [Samtools](https://www.htslib.org/), [test](), [test](), [test](), [test](), [test](). If resulting data are already available, it can also show them.

## Development choices
For modularity and portability purpoposes we created two dockerized modules, one for the Frontend and one for the Backend, and another module for retrieving data from NCBI databases. The database engine we chose to use is MongoDB, due to its no-SQL design. That makes it adequate to store and manage eterogeneous data with decent performance.

We chose to use Python for the Biopyparse module because there are useful libraries like biopython to fetch data. 

We chose to use GoLang for the Backend because it allowed us to develop the API in an easier way than other languages, with its great handling of JSON structures.

We chose to use Vue for the Frontend because it is a modern, standard Framework and we had previous experience using it.

# Installation
- Install [Mongotools](https://www.mongodb.com/try/download/database-tools)
	- Mongotools is needed only to manage DB data. It provides functions like mongorestore to retrive data from a local backup.
		```properties
		mongorestore --db=<DB-Name> <backup_path>
		```
- Install [Docker](https://docs.docker.com/engine/install/)
- Make a new folder, move to that directory and run these commands:
	```properties
	wget https://raw.githubusercontent.com/BITSapienza/.github/main/docker-compose.yml
	```
	```bash 
	git clone https://github.com/BITSapienza/Bio-Server
	```
	```bash
	git clone https://github.com/BITSapienza/Vulgaris-Platform
	```
- Finally, run:
	```shell
	docker compose up -d
	```
- Optionally, restore a DB backup using the mongorestore command.


# Collections
Base collections:

- `nucleotide_data`			contains the data obtainable by searching in the Nucleotide NCBI database.
- `taxonomy_data`			contains the data obtainable by searching in the Taxonomy NCBI database.
- `protein_data`			contains the data obtainable by searching in the Nucleotide Protein database.
- `sra`						contains the data obtainable by searching in the SRA NCBI database.

Collections made for performance reason:
  
- `taxonomy_tree`         	contains the childrens for every taxonomy term, used in Vulgaris Platform's Taxonomy View.
- `table_basic`           	contains a lightweight version of the data, used in Vulgaris Platform's Search view.
- `table_complete`        	contains a more complete version of the data, used in Vulgaris Platform's Organism View.

### `taxonomy_data`

Example of an element from taxonomy_data:
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

### `nucleotide_data`

Example of an element from nucleotide_data:
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

### `protein_data`

Example of an element from protein_data:
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

### `table_basic`

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


### `table_complete`

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

### `taxonomy_tree`

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
