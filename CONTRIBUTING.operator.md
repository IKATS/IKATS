# CONTRIBUTING to an operator

The purpose of this document is to describe how a contributor will develop and propose
an operator for IKATS.

This document does not focus on the installation of the development means (virtualenv, docker, IDE ...) and assumes that the developer environment is fully operational. For contributors that already have installed the [IKATS Sandbox](https://github.com/IKATS/ikats-sandbox), all the environnement for Docker and Git is setup.

## Repository content

* An operator repository begins with `op-` to quickly identify it. This is an internal practice used for our scripts, you could choose to ignore it.
* It is composed of at least the following files (detailed below):
  * `LICENSE`: license of the contribution
  * `NOTICE`: to list all the dependencies of the contribution. That document is a good practice and [is mandatory for Apache Licence, version 2](http://apache.org/dev/apply-license.html).
  * `README.md`: description of the operator for both the user and the developer
  * `catalog_def.json`: definition of the operator in the catalog (the list of operators and their associated information)
  * *operator_name* folder: folder containing the python code (The folder containing the python code shall have the same name as repository name without `op-` (ie. `op-my_operator/my_operator/my_code_here.py`)).

### README.md

For understanding purposes, the `README.md` file should:

* describe the purpose of the operator
* give a description of the inputs, ouputs, and parameters to know how to use it
* contain a paragraph about the interaction with IKATS
  * Wrappers (entry point of the operator for IKATS call)
  * IKATS API calls used by operator
  * Other resources readings/writings

### catalog_def.json

This file will be read at startup.
It is used to fill in the catalog database with the operators being integrated to the current instance.

In other words, it let IKATS knows how to use the operator in IKATS environment.

#### File naming convention

The name of the catalog file shall be compliant with regexp: `catalog_def(_[0-9]{1,2})?\.json`

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

| JSON field  | Type   | Required | Description                                     | Constraints                                                                                                                        |
| ----------- | ------ | -------- | ----------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| name        | String | Yes      | Implementation name                             | Unique across the IKATS operators database. Shall match regexp `^[a-zA-Z0-9_]+$`                                                   |
| label       | String | Yes      | Displayed name for this operator in GUI         |                                                                                                                                    |
| description | String | No       | Description of the implementation               |                                                                                                                                    |
| family      | String | No       | Family name reference                           | Shall reference a unique family name in IKATS                                                                                      |
| entry_point | String | Yes      | Path to the entry point of the implementation   | Shall match regexp `^[a-zA-Z0-9_]+[a-zA-Z0-9_.]*[a-zA-Z0-9_]+::[a-zA-Z0-9_]+)`. Corresponds to `<module path>::<function to call>` |
| inputs      | Array  | No       | List of inputs (connected to previous operator) |                                                                                                                                    |
| parameters  | Array  | No       | List of parameters                              |                                                                                                                                    |
| outputs     | Array  | No       | List of outputs (results)                       |                                                                                                                                    |

List of allowed families:

* `Stats__TS_Correlation_Computation`: set of correlation functions, applied on Time series
* `Stats__TS_Stats`: set of functions about statistics features on Time series
* `Preprocessing_TS__Reduction`: set of pre-processing functions which reduce information of Time series
* `Preprocessing_TS__Cleaning`: set of pre-processing functions which are cleaning the information of Time series.
* `Preprocessing_TS__Transforming`: set of pre-processing functions which are transforming the Time series: not classified as cleaning, or reduction functions.
* `Data_Modeling__Supervised_Learning`: supervised learning
* `Data_Exploration`: functions exploring the data: searches, highlighs some element, ...
* `Data_Modeling__Unsupervised_Learning`: collection of Unsupervised learning agorithms
* `supervised_learning`: this family contains all supervised learning algorithms

Please do a PR if none of above families matches your operator

##### inputs

Provides the operator `inputs` description. One or more `inputs` could be provided, the list is ordered regarding the operator Python script signature.

| JSON field  | Type   | Required | Description                                | Constraints                                                           |
| ----------- | ------ | -------- | ------------------------------------------ | --------------------------------------------------------------------- |
| name        | String | Yes      | Input name                                 | Unique in all inputs/parameters. Shall match regexp `^[a-zA-Z0-9_]+$` |
| label       | String | Yes      | Displayed title for this input             | 8 chars max (recommended for readability)                             |
| description | String | No       | Description of the input                   |                                                                       |
| type        | String | Yes      | Type of the input (see allowed types part) |                                                                       |

##### parameters

Provides the operator `parameters` description. One or more `parameters` could be provided, the list is ordered regarding the operator Python script signature.

| JSON field    | Type   | Required | Description                                    | Constraints                                                           |
| ------------- | ------ | -------- | ---------------------------------------------- | --------------------------------------------------------------------- |
| name          | String | Yes      | Parameter name                                 | Unique in all inputs/parameters. Shall match regexp `^[a-zA-Z0-9_]+$` |
| label         | String | Yes      | Displayed title for this parameter             | 15 chars max (recommended for readability)                            |
| description   | String | No       | Description of the parameter                   |                                                                       |
| type          | String | Yes      | Type of the parameter (see allowed types part) |                                                                       |
| domain        | String | No       | Value domain (see value domain part)           |                                                                       |
| default_value | any    | No       | Default value of the parameter.                |                                                                       |

##### outputs

Provides the operator `outputs` description. One or more `outputs` could be provided, the list is ordered regarding the operator Python script returns.

| JSON field  | Type   | Required | Description                                 | Constraints                                                 |
| ----------- | ------ | -------- | ------------------------------------------- | ----------------------------------------------------------- |
| name        | String | Yes      | Output name                                 | Unique in all outputs. Shall match regexp `^[a-zA-Z0-9_]+$` |
| label       | String | Yes      | Displayed title for this output             | 8 chars max (recommended)                                   |
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
      "default_value": 8
    },
    {
      "name": "max_iter",
      "label": "Max Iter",
      "description": "Maximum number of iterations of the k-means algorithm for a single run",
      "type": "number",
      "default_value": 300
    },
    {
      "name": "n_init",
      "label": "N init",
      "description": "Number of time the k-means algorithm will be run with different centroid seeds. The final results will be the best output of n_init consecutive runs in terms of inertia",
      "type": "number",
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

**Label field**:

Label is not mandatory. If not defined, the name is used as label (may be truncated if too long)

**Value domain**:

Value domain is a string describing the domain of possible values. For now, only a discrete series is allowed using the JSON list format.

As an example, if you want to constrain a parameter to 3 values: A, B or C, you shall define the domain field to: `"['A','B','C']"`

**Allowed types**:

The list of the types to be used by operators as input/output is defined [here](IKATS_types.md)

## Create your operator

### Prerequisites

* The operator must be developed in Python version compatible with 3.4
* Contributor should only use available python module. Please do a PR if you need other libraries

List of available python modules:

* cffi: 1.8.3
* Django: 1.8.6
* django-cors-headers: 2.1.0
* djangorecipe: 2.2.1
* eventlet: 0.20.1
* fastdtw: 0.3.0
* flake8: 3.0.4
* greenlet: 0.4.12
* gunicorn: 19.3.0
* httpretty: 0.8.14
* mccabe: 0.5.2
* mock: 1.3.0
* natsort: 5.0.2
* numpy: 1.11.2
* pbr: 1.10.0
* psycopg2: 2.7.3.1
* py4j: 0.10.3
* pycodestyle: 2.0.0
* pycparser: 2.17
* pyflakes: 1.2.3
* python-dateutil==2.6.0
* requests: 2.8.1
* scikit-learn: 0.19.1
* scipy: 0.18.1
* setuptools: 33.1.1
* six: 1.10.0
* zc.buildout: 2.5.3
* zc.recipe.egg: 2.0.3

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
* IKATS computed metadata name are compliant with regexp `^(ikats|qual)_.[a-zA-Z0-9_-]+[a-zA-Z0-9]$`

Almost full usage Examples:

```python
# Import IKATS API
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
We recommend to be compliant as much as possible with the PEP8 and pylint.

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

Because IKATS runs on several nodes, operators should not rely on static files location (nor absolute, nor relative paths)
not handled by IKATS.
The only files operators may access to are located under the contributor space.

### Assertion/raises

It is possible to use any type `assert`/`raise` operation in the operator.
There is no constraint.

### Working with Spark

Most ikats algorithms are developed using spark to distribute computing on a large amount of data.  
In some cases, the algorithm is able to choose between a spark and a (single process or) multi process version, according to the processed data size.  
This should be the rule, so that computing time would be in proportion to data size.  
**We currently support Spark 2.3.1** 

The first rule is to use ikats provided spark session, and to surround its usage by a try...except clause to keep the context consistent (see example below)

When using spark, keep in mind that you should never handle too much data (list or dict) in the driver (for example do not collect timeseries data in the driver).  
Each spark task should moreover work on a chunk of data (if full data is too large) that is in proportion with executor available memory.  

Available python classes for working with spark are : SSessionManager (to manage a spark session/context) and SparkUtils (which provides some functions to ease spark use)

You can also have a look at some [IKATS operators](https://github.com/IKATS?q=op-) using Spark  

**Example of spark usage in IKATS:**
```python
from ikats.core.library.spark import SparkUtils, SSessionManager

# ...

# Always set a try/except clause to make the context consistent
try:
  # Get Spark Context
  spark_context = SSessionManager.get_context()

  # From a list of timeseries
  rdd_ts_info = spark_context.parallelize(ts_list, max(8, len(ts_list)))

  # Call a user defined function (_spark_ts_read) that will read datapoints on these Timeseries within the specified range
  # and remove the ones that do'esnt have any point in this range
  rdd_1 = rdd_ts_info \
      .map(lambda x: (x[0], _spark_ts_read(tsuid=tsuid, start_date=date_range_start, end_date=date_range_start) \
      .filter(lambda x: len(x[1]) > 0)

  # Call a user defined function (_spark_ts_write) to write data points
  rdd_1.map(lambda x: (_spark_ts_write(tsuid=x[0], data=x[1]).collect()

except Exception:
    raise
finally:
    # Stop Spark context in all cases
    SSessionManager.stop()

```

## Development workflow

To test an operator, you have 2 main ways:

* Using your own development platform (out of IKATS)
* Using the IKATS sandbox

The first one will be used to *fix* and *improve* the behaviour of the **internal** part of the operator and it is up to the
contributor to set up the test process.

The second way is used for 2 purposes:

* when operator needs to communicate with Spark or real IKATS API calls (like Timeseries database)
* as *integration process* to see how the operator works with IKATS. The operator should be considered as black box.

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
