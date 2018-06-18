# CONTRIBUTING to a operator

The purpose of this document is to describe how a contributor will develop and propose
an operator for IKATS.

This document does not focus on the installation of the development means (virtualenv, docker, IDE ...)
and assume the developer environment is fully operational.

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

TODO Format description here

## Create your operator

### Prerequisites

* The operator must be developed in Python version compatible with 3.4
* Contributor should not add any external python module except inside its own module

List of available python modules:

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
pylint --rcfile=${path_to_pylint_rc}/pylint.rc ${file_name}
```

### Documentation

If you need some support from IKATS team, consider commenting your code properly.
A good start is to have at least 10% of lines as comment.

Operators shall be described using [sphinx doc format](http://www.sphinx-doc.org).

For the wrappers, IKATS team needs the description and type of all parameters and returned values.
The bigger structure (dict, list of dict, class, complex objects, ...) should be described with an example or with an
additional description to be sure there is a complete understanding of the parameter/output.

Expected sphinx tags are:

* param
* type
* return
* rtype
* raise (if needed)

Here below a complete docstring example:

```python
def compute_slope(ts_list, fid_suffix="_slope", chunk_size=75000, save_new_ts=True):
    """
    Compute the slope of a list of timeseries using spark

    This implementation computes slope for one TS at a time in a loop.
    To know the details of the computation, see the corresponding method

    :param ts_list: list of TS. Each item is a dict composed of a TSUID and a functional id
    :param fid_suffix: Functional identifier suffix of the final timeseries
    :param chunk_size: Number of points per chunk (assuming the TS is periodic)
    :param save_new_ts: True (default) if TS must be saved to database

    :type ts_list: list of dict
    :type fid_suffix: str
    :type chunk_size: int
    :type save_new_ts: bool

    :return: the new list of derived TS (same order as input)
    :rtype: list of dict

    :raise TypeError: if ts_list type is incompatible
    """
    pass
```

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

The second way is likely an integration process. It should be used to see how the operator works with IKATS.
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

The Python logfile can be printed by using `docker-compose logs -f python_api`