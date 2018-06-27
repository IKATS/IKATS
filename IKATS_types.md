# Functional types

This page reference the complete list of types handled by IKATS.

WORK IN PROGRESS


* **bool** : boolean (true,false)
* **correlation_dataset** : aggregation model of a set of correlation matrices
* **date** : timestamp (milliseconds since EPOCH). Is formatted to ISO-8601 date in GUI
* **dot** : DOT format proposed by graphviz dot tool
* **ds_name** : string identifying the dataset name
* **kmeans_mds** : list of grouped TS used to store and to visualize k-means algorithms results
* **list** : selectbox using `domain` field for allowed values
* **md_list** : list of metadata where format is: `{tsuid1: {metadataName1 : metadataValue1, ...}, ...}`
* **number** : any number
* **pattern_groups** : list of grouped patterns used to store and to visualize search patterns algorithms results
* **table** : JSON structure defining a table
* **text** : any string
* **ts_bucket** : list of timeseries definitions, augmented with flags.
* **ts_list** : list of duet `{tsuid: x, funcId: y}` where `tsuid` is the unique identifier of a TS in database and `funcId` the Functional Identifier of the TS (human readable version of `TSUID`)
* **tsuid** : string defining the unique identifier of a TS in database
* **tsuid_list** : list of TSUID
* **sk_model** : model from scikit-learn library
