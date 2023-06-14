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
This is a platform of microalgae with cross-referencing functionality, linking data from the most important biologic databases. It's meant for consulting taxonomic, nucleotide and protein data on over 900+ species of microalgae. One of the major focus of the project was to identify the species with carbon capture properties in order to reduce CO2 level in atmosphere.

## Solved Issues
Consulting data is now possible in an easier way: you can search for a species, what genes are related to it, what Bioprojects and Experiments have been carried out. It provides references to raw data that can be passed through some major analysis tools. The output from one of this program is intended to be passed to another one, here are listed what it has been used for our end, in this exact order of execution:
[Busco](https://busco.ezlab.org/) for checking genomes quality;
[trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic) for trimming of genomic data and pair-end division in strands 1 and 2;
[hisat2](https://ccb.jhu.edu/software/hisat/index.shtml) for creation, from fasta 1 and 2, a sam file that contains treated data;
[Samtools sort](https://www.htslib.org/) for organizing data contained in sam files and transform them in .bam format; 
[Stringtie](https://ccb.jhu.edu/software/stringtie/) that creates .gtf files from .srr, thanks to the notation and to the .bam file bam obtained before.
If data are available after these steps, our platform can also show them.

## Development choices
For modularity and portability purpoposes we created two dockerized modules, one for the Frontend and one for the Backend, and another module for retrieving data from NCBI databases. We chose to use a no-SQL database due to its properties to store and manage eterogeneous data. So we preferred MongoDB for its reliability and documentation. 

We chose to use Python for the Biopyparse module because there are useful libraries like biopython to fetch data. 

We chose to use GoLang for the Backend because it allowed us to develop the API in an easier way than other languages, with its great handling of JSON structures.

We chose to use Vue for the Frontend because it is a modern, standard Framework and we had previous experience using it.

# How to run the project
We offer two modalities of use:
- Follow the Installation paragraph showed below. Only Backend and Frontend need to be downloaded if you want to restore a backup database with MongoDB.
- Run the modules stand-alone. The commands that are needed to run to use the software without docker are listed in the omonimous sub-repositories, in relative README.md. 

# Installation
- Install [Mongotools](https://www.mongodb.com/try/download/database-tools)
	- Mongotools is needed only to manage DB data. It provides functions like mongorestore to retrive data from a local backup.
		```properties
		mongorestore --db=<DB-Name> <backup_path>
		```
- Install [Docker](https://docs.docker.com/engine/install/) following docker-engine or docker-server guide
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
