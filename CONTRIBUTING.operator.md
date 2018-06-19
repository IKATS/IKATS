# CONTRIBUTING to an operator

The purpose of this document is to describe how a contributor will develop and propose
an operator for IKATS.

This document does not focus on the installation of the development means (virtualenv, docker, IDE ...)
and assumes that the developer environment is fully operational.

## Repository content

* An operator repository begins with `op-` to quickly identify it
* It is composed of at least the following files (detailed below):
  * `LICENSE`: License of the contribution
  * `NOTICE`: To list all the dependencies of the contribution
  * `README.md`: Description of the operator
  * `catalog_def.json`: definition of the operator in the catalog
  * *operator_name* folder: folder containing the python code (The folder containing the python code shall be the same name as repository name without `op-` (ie. `op-my_operator/my_operator/my_code_here.py`)).

### README.md

For understanding purposes , the `README.md` file should :

* describe the purpose of the operator
* give a description of the inputs, ouputs, and parameters to know how to use it
* a paragraph about the interaction with IKATS
  * Wrappers (entry point of the operator for IKATS call)
  * IKATS API calls used by operator
  * Spark usage
  * Other resources readings/writings
* explain some aspects of the internal behaviour (this can be described in another file)

### catalog_def.json

This file will be read at startup.
It is used to fill in the catalog database with the operators beeing integrated to the current instance.

In other words, it let IKATS knows how to use the operator in IKATS environment.

#### File naming convention

The name of the catalog file shall be compliant with regexp : `catalog_def(_[0-9]{1,2})?\.json`

* Valid files:
  * catalog_def.json
  * catalog_def_03.json
  * catalog_def_2.json
* Not valid files:
  * catalog_def_.json
  * catalog_def_A.json
  * catalog_def_012.json

#### Description of the expected JSON content

This aims at providing information about the operator. It intends to be used by IKATS to bind the GUI operator parts (inputs, parameters and outputs) to the Python script operator.

| JSON field  | Type   | Required | Description                                      | Constraints                                                                                                                      |
|-------------|--------|----------|--------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| name        | String | Yes      | Implementation name                              | Unique across the IKATS operators database. Shall match regexp `^[a-zA-Z0-9_]+$`                                                 |
#Review#176667 label is not required to be unique
| label       | String | No       | Implementation label                             | Shall reference a unique name in the IKATS operators database or a reference to a newly defined operator in that file            |
| description | String | No       | Description of the implementation                |                                                                                                                                  |
#Review#176667 family is optional
#Review#176667 should point to a paragraph listing allowed families (and saying to do a PR to add a family)
| family      | String | Yes      | Family name reference                            | Shall reference a unique family name in IKATS                                                                                    |
| entry_point | String | Yes      | Path to the entry point of the implementation    | Shall match regexp `^[a-zA-Z0-9_]+[a-zA-Z0-9_.]*[a-zA-Z0-9_]+::[a-zA-Z0-9_]+)`. Corresponds to <module path>::<function to call> |
| inputs      | Array  | No       | List of inputs (connected to previous operator)  |                                                                                                                                  |
| parameters  | Array  | No       | List of parameters                               |                                                                                                                                  |
#Review#176667 outputs shall be optional (i.e import metadata)
| outputs     | Array  | Yes      | List of outputs (results)                        |                                                                                                                                  |

##### inputs

Provides the operator `inputs` description. One or more `inputs` could be provided, the list is ordered regarding the operator Python script signature.

| JSON field  | Type   | Required | Description                                | Constraints                                                           |
|-------------|--------|----------|--------------------------------------------|-----------------------------------------------------------------------|
| name        | String | Yes      | Input name                                 | Unique in all inputs/parameters. Shall match regexp `^[a-zA-Z0-9_]+$` |
| label       | String | No       | Displayed title for this input             | 8 chars max (recommended for readability)                             |
| description | String | No       | Description of the input                   |                                                                       |
| type        | String | Yes      | Type of the input (see allowed types part) |                                                                       |

##### parameters

Provides the operator `parameters` description. One or more `parameters` could be provided, the list is ordered regarding the operator Python script signature.

| JSON field    | Type   | Required | Description                                    | Constraints                                                           |
|---------------|--------|----------|------------------------------------------------|-----------------------------------------------------------------------|
| name          | String | Yes      | Parameter name                                 | Unique in all inputs/parameters. Shall match regexp `^[a-zA-Z0-9_]+$` |
| label         | String | No       | Displayed title for this parameter             | 15 chars max (recommended for readability)                            |
| description   | String | No       | Description of the parameter                   |                                                                       |
| type          | String | Yes      | Type of the parameter (see allowed types part) |                                                                       |
| domain        | String | No       | Value domain (see value domain part)           |                                                                       |
| default_value | any    | No       | Default value of the parameter.                |                                                                       |

##### outputs

Provides the operator `outputs` description. One or more `outputs` could be provided, the list is ordered regarding the opeartor Python script returns.

| JSON field  | Type   | Required | Description                                 | Constraints                                                 |
|-------------|--------|----------|---------------------------------------------|-------------------------------------------------------------|
| name        | String | Yes      | Output name                                 | Unique in all outputs. Shall match regexp `^[a-zA-Z0-9_]+$` |
| label       | String | No       | Displayed title for this output             | 8 chars max (recommended)                                   |
| description | String | No       | Description of the output                   |                                                             |
| type        | String | Yes      | Type of the output (see allowed types part) |                                                             |

#### Example

Given the following Python operator signature:

```python
def kmeans (tslist, cluster, max_iter, n_init):
    # Kmeans code here ...
    return result
```

The corresponding JSON content could be:

```json
{
  "name": "kmeans",
  "label": "K-Means",
  "description": "Implementation description ...",
  "family": "clustering",
  "entry_point": "ikats.algo.clustering::kmeans",
  "inputs": [
    {
      "name": "tslist",
      "label": "TS list",
      "description": "Timeseries references to use",
      "type": "ts_list"
    }
  ],
  "parameters": [
    {
      "name": "cluster",
      "label": "clusters",
      "description": "The number of clusters to form as well as the number of centroids to generate",
      "type": "number",
#Review#176667 are null allowed ?
      "domain": null,
      "default_value": 8
    },
    {
      "name": "max_iter",
      "label": "Max Iter",
      "description": "Maximum number of iterations of the k-means algorithm for a single run",
      "type": "number",
      "domain": null,
      "default_value": 300
    },
    {
      "name": "n_init",
      "label": "N init",
      "description": "Number of time the k-means algorithm will be run with different centroid seeds. The final results will be the best output of n_init consecutive runs in terms of inertia",
      "type": "number",
      "domain": null,
      "default_value": 10
    }
  ],
  "outputs": [
    {
      "name": "result",
      "label": "Result",
      "description": "Clustering results",
      "type": "xxx"
    }
  ]
 }
```

#### Common information

**label Field**:

Label is not mandatory. If not defined, the name is used as label (may be truncated if too long)

**Value domain**:

Value domain is a string describing the domain of possible values. For now, only a discrete series is allowed using the JSON list format.

As an example, if you want to constrain a parameter to 3 values: A, B or C, you shall define the domain field to : `"['A','B','C']"`

**Allowed types**:

Here is the list (*in progress*) of the types to be used by operators

#Review#176667 mise en page : il manque des retours à la ligne entre chaque type
**bool** : boolean (true,false)
**date** : timestamp (milliseconds since EPOCH). Is formatted to ISO-8601 date in GUI
**ds_name** : string identifying the dataset name
#Review#176667 give an example of metadata list
**md_list** : list of metadata
**number** : any number
**text** : any string
**list** : selectbox using “domain” field for allowed values
#Review#176667 precise : tsuid is the unique identifier of a TS *IN DATABASE* (2 occurences in 2 lines)
**ts_list** : list of duet “{tsuid: x, fid: y}” where tsuid is the unique identifier of a TS and fid the Functional Identifier of the TS (human readable version of TSUID)
**tsuid** : string defining the unique identifier of a TS
**tsuid_list** : list of TSUID

## Create your operator

### Prerequisites

* The operator must be developed in Python version compatible with 3.4
#Review#176667 precise or replace: contributor shall do a PR if he wants to add other libraries into ikats python core
* Contributor should not add any external python module except inside its own module

List of available python modules:

#Review#176667 provide a sorted list
#Review#176667 2 libraries lacking compared to /SCM/pybase/assets/requirements.txt
* Django = 1.8.6
* eventlet = >= 0.19.0
* gunicorn = 19.3.0
* requests = 2.8.1
* mock = >=1.3.0
* httpretty = >=0.8.10
* cffi = 1.8.3
* djangorecipe = 2.2.1
* flake8 = 3.0.4
* mccabe = 0.5.2
* numpy = 1.11.2
* pbr = 1.10.0
* py4j = 0.10.3
* pycodestyle = 2.0.0
* pyflakes = 1.2.3
* scikit-learn = 0.19.0
* scipy = 0.18.1
* setuptools = 28.8.0
* six = 1.10.0
* zc.buildout = 2.5.3
* zc.recipe.egg = 2.0.3
* fastdtw = >= 0.3.0

### IKATS API

IKATS API is available [here](https://github.com/IKATS/ikats-pybase).
Clone the repository and add `src` folder to your `PYTHON_PATH`

The IKATS python API allow contributors to access all IKATS information needed by an operator.

* Access to **Timeseries** data points and associated functional identifiers
* Access to **Datasets** composed of timeseries
* Access to **Metadata** of each timeseries
* Access to **ProcessData** (results of operators)
* Access to **Learning Sets** (table)

The documentation of this API is written using Sphinx.
Currently, there is no extract of this part of the documentation available.
You can refer to the IKATS API code comments (same information without HTML rendering):

The entry point in code can be found in the [ikats-pybase](https://github.com/IKATS/ikats-pybase) repository at `src/ikats/core/resource/api.py`.

For now, there is no strong rules to name a metadata or a functional id (human readable name for a timeseries).
Keep in mind we have some naming convention:

* Functional id and metadata should be compliant with regexp `^[a-zA-Z0-9][a-zA-Z0-9_-]+[a-zA-Z0-9]$`
* Ikats computed metadata name are compliant with regexp `^(ikats|qual)_.[a-zA-Z0-9_-]+[a-zA-Z0-9]$`

Almost full usage Examples:

```python
# Import Ikats API
from ikats.core.resource.api import IkatsApi

# Defining data as numpy array (1st col is timestamp, 2nd col is value)
data = np.array([
    [1450772111000, 1.0],
    [1450772112000, 2.0],
    [1450772113000, 3.0],
    [1450772114000, 5.0],
    [1450772115000, 8.0],
    [1450772116000, 13.0]
])

# Create TS
r = IkatsApi.ts.create(fid="my_functional_name_for_this_TS", data=data)
print(r)
# {
#    'numberOfSuccess': 6,
#    'funcId': 'test_fid',
#    'errors': 0,
#    'summary': '6 points imported',
#    'status': True,
#    'responseStatus': 200,
#    'tsuid': '00001600000300077D0000040003F1'
# }

# Add Metadata for TS
r = IkatsApi.md.create(
    tsuid='00001600000300077D0000040003F1',
    name='my_label_for_classif',
    value='3',
    data_type=DTYPE.number)

# Read points
r = IkatsApi.ts.read(tsuid_list=["00001600000300077D0000040003F1"])

# Read metadata
md = IkatsApi.md.read('00001600000300077D0000040003F1')
print(md)
#{ '00001600000300077D0000040003F1':
#  {
#    'ikats_start_date': '1450772111000',
#    'ikats_end_date': '1450772116000',
#    'ikats_nb_points': '6',
#    'my_label_for_classif': '3'
#  }
#}

# Then delete TS
ikatsApi.ts.delete("00001600000300077D0000040003F1")
```

The same principle is used for datasets

### Coding rules

Even if contribution is seen as a black box by IKATS, it is easier to understand the purpose of the entry points.
We recommends to be compliant as much as possible with the PEP8 and pylint.

Please use IKATS configuration file located in [ikats-pybase](https://github.com/IKATS/ikats-pybase) repository at `tests/assets/pylint.rc`.

```bash
# Example to call pylint for a specific file with the IKATS configuration
pylint --rcfile=${path_to_ikats_pybase}/tests/assets/pylint.rc ${file_name}
```

### Documentation

If you need some support from IKATS team, consider commenting your code properly.
A good start is to have at least 10% of lines as comment.

### Logging rules

Contributor shall use `logging` python standard module to log any kind of information.
About the verbosity/level:

* **error** should be restricted to fatal errors the operator won't handle (Assertion)
* **warning** should be used to indicate an alternative behavior than the nominal one
* **info** should not be used in operators (only in IKATS)
* **debug** can be used for any information. No limitation.

Never use the root logger but a named logger containing the python file name

Use lazy evaluation of strings for logging

Here below a complete example from import to usage of logger

```python
# Import logger
import logging

# Logger definition
LOGGER = logging.getLogger(__name__)

# Logger usage
LOGGER.error("An error occurred while processing the data %s at line %s", data, line)
```

### File access

Because IKATS runs on several nodes, opeartors should not rely on static files location (nor absolute, nor relative paths)
not handled by IKATS.
The only files operators may access to are located under the contributor space.

### Assertion/raises

It is possible to use any type `assert`/`raise` operation in the operator.
There is no constraint.

## Development workflow

To test an operator, you have 2 main ways:

* Using its own development platform (out of IKATS)
* Using the IKATS sandbox

The first one will be used to *fix* and *improve* the behaviour of the **internal** part of the operator and it is up to the
contributor to set up the test process.

#Review#176667 add : sandbox use for testing is required when operator uses ikatsApi or opentsdb or spark
The second way is likely an *integration process*. It should be used to see how the operator works with IKATS.
Ideally, there should not be any issue inside the operator except at IKATS connections bounds (API calls).
The operator should be considered as black box.

### Test your operator with the IKATS sandbox

* Prepare your environment
  * Download the [IKATS sandbox](https://github.com/IKATS/ikats-sandbox)
  * Before starting the sandbox, do some changes in the sandbox project
    * Create a `op-repo-list.yml` file with the following line `- url:/app/local/op/my_operator`
    * In `docker-compose.yml`, find the `operator-fetcher` service and append new volumes:
      ```yaml
      - <path_to_your_operator>:/app/local/op/my_operator:ro
      - ./op-repo-list.yml:/app/repo-list.yml
      ```
* Start the sandbox: `docker-compose up` (or refer to [repository](https://github.com/IKATS/ikats-sandbox) for more details)

You should be able to perform your tests directly using the IKATS GUI.

The Python logfile can be printed by using `docker-compose logs -f python_api`.

There is currently no supported way to do remote debugging with IKATS environment.

#Review#176667 a paragraph on spark ikats specific use would be appreciated (version provided, open and close context, ...)
