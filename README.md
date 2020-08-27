Graphflow Optimizers
--------------------

<img src="docs/img/graphflow.png" height="181px" weight="377">

Table of Contents
-----------------
  * [Overview](#Overview)
  * [Build Steps](#Build-Steps)
  * [Executing Queries](#Executing-Queries)
  * [Contact](#contact)

Overview
-----------------
For an overview on Graphflow's one-time subgraph matching optimizer, check our [paper](http://amine.io/papers/wco-optimizer-vldb19.pdf).      
Copyright © 2016-2020. DO NOT DISTRIBUTE. USE ONLY FOR ACADEMIC RESEARCH PURPOSES.   

Build Steps
-----------------
* To do a full clean build: `./gradlew clean build installDist`
* All subsequent builds: `./gradlew build installDist`

Executing Queries
-----------------
### Getting Started
After building Graphflow, run the following command in the project root directory:
```
. ./env.sh
```
You can now move into the scripts folder to load a dataset and execute queries:
```
cd scripts
```

### Dataset Preperation
A dataset may consist of two files: (i) a vertex file, where IDs are from 0 to N and each line is of the format (ID,LABEL); and (ii) an edge file where each line is of the format (FROM,TO,LABEL). If the vertex file is omitted, all vertices are assigned the same label. We mainly used datasets from [SNAP](https://snap.stanford.edu/). The `serialize_dataset.py` script lets you load datasets from csv files and serialize them to Graphflow format for quick subsequent loading.

To load and serialize a dataset from a single edges files, run the following command in the `scripts` folder:
```
python3 serialize_dataset.py /absolute/path/edges.csv /absolute/path/data
```
The system will assume that all vertices have the same label in this case. The serialized graph will be stored in the `data` directory. If the dataset consists of an edges file and a vertices file, the following command can be used instead:
```
python3 serialize_dataset.py /absolute/path/edges.csv /absolute/path/data -v /absolute/path/vertices.csv
```
After running one of the commands above, a catalog can be generated for the optimizer using the `serialize_catalog.py` script.
```
python3 serialize_catalog.py /absolute/path/data  
```

### Executing Queries
Once a dataset has been prepared, executing a query is as follows:
```
python3 execute_query.py "(a)->(b),(b)->(c),(c)->(d)" /absolute/path/data
```

An output example on the dataset of Amazon0601 from [SNAP](https://snap.stanford.edu/) with 1 edge label and 1 verte label is shown below. The dataset loading time, the opimizer run time, the quey execution run time and the query plan with the number of output and intermediate tuples are logged.
```
Dataset loading run time: 626.713398 (ms)
Optimizer run time: 9.745375 (ms)
Plan initialization before exec run time: 9.745375 (ms)
Query execution run time: 2334.2977 (ms)
Number output tuples: 118175329
Number intermediate tuples: 34971362
Plan: SCAN (a)->(c), Single-Edge-Extend TO (b) From (a[Fwd]), Multi-Edge-Extend TO (d) From (b[Fwd]-c[Fwd])
```

In order to invoke a multi-threaded execution, one can execute the query above with the following command to use 2 threads.
```
python3 execute_query.py "(a)->(b),(b)->(c),(c)->(d)" /absolute/path/data -t 2
```

The query above assigns an arbitrary edge and vertex labels to (a), (b), (c), (a)->(b), and (b)->(c). Use it with unlabeled datasets only.
When the dataset has labels, assign labels to each vertex and edge as follows:
```
python3 execute_query.py "(a:person)-[friendof]->(b:person), (b:person)-[likes]->(c:movie)" /absolute/path/data
```

Contact
-----------------
[Amine Mhedhbi](http://amine.io/), amine.mhedhbi@uwaterloo.ca

