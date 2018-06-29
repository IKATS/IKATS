# Functional types

This page reference the complete list of types handled by IKATS.

> This document will be updated with more details. Contact dev@ikats.org if you need more information.

## Simple types

* **bool** : boolean (true,false)
* **date** : timestamp (milliseconds since EPOCH). Is formatted to ISO-8601 date in GUI
* **ds_name** : string identifying the dataset name
* **number** : any number
* **text** : any string
* **tsuid** : string defining the unique identifier of a TS in database

## Structured types

* **correlation_dataset** : aggregation model of a set of correlation matrices
* **dot** : DOT format proposed by graphviz dot tool
* **kmeans_mds** : list of grouped TS used to store and to visualize k-means algorithms results
* **list** : selectbox using `domain` field for allowed values
* **md_list** : list of metadata where format is: `{tsuid1: {metadataName1 : metadataValue1, ...}, ...}`
* **pattern_groups** : list of grouped patterns used to store and to visualize search patterns algorithms results
* **table** : JSON structure defining a table
* **ts_bucket** : list of timeseries definitions, augmented with flags.
* **ts_list** : list of duet `{tsuid: x, funcId: y}` where `tsuid` is the unique identifier of a TS in database and `funcId` the Functional Identifier of the TS (human readable version of `TSUID`)
* **tsuid_list** : list of TSUID
* **sk_model** : model from scikit-learn library
