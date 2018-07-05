# CONTRIBUTING to viztool

The purpose of this document is to describe how a contributor will develop and propose
a viztool for IKATS.

This document does not focus on the installation of the development means (docker, IDE ...) and assumes that the developer environment is fully operational. For contributors that already have installed the [IKATS Sandbox](https://github.com/IKATS/ikats-sandbox), all the environnement for Docker and git is setup.

## Definitions

List of some terms used in this document

* **VizEngine**: the _VizEngine_ is a GUI component managing and suggesting _VizTools_ according to the type of an _operator_ output, in a workflow.
  The _VizEngine_ also handles a _VizTool_ chain (represented in GUI by a bread-crumb).
* **VizTool**: the graphical container displayed on GUI allowing to visualize the output of an _operator_
  A _VizTool_ is called so if and only if:
  * the code building the container implements the _VizTool_ class,
  * the _VizTool_ is made available in the GUI for one or several IKATS [functional types](IKATS_types.md),
  * the _VizTool_ has to be available into the _VizToolsLibrary_,

## Repository content

* A viztool repository begins with `vt-` to quickly identify it. This is an internal practice used for our scripts, you could choose to ignore it.
* It is composed of at least the following files (detailed below):
  * `LICENSE`: license of the contribution
  * `NOTICE`: to list all the dependencies of the contribution. That document is a good practice and [is mandatory for Apache Licence, version 2](http://apache.org/dev/apply-license.html).
  * `README.md`: description of the viztool for both the user and the developer
  * `viztool_def.json`: definition of the viztool in the catalog (the list of viztools and their associated information)
  * `manifest.json`: definition of the includes order and libraries used
  * `*.js`: viztool code implementing the _VizTool_ class

You can have a look at some [IKATS viztools](https://github.com/IKATS?q=vt-) for some examples

### README.md

For understanding purposes, the `README.md` file should:

* describe the purpose of the viztool
* explain some aspects of the internal behaviour (this can be described in another file)

### manifest.json

To handle multiple resources files, this file will help IKATS to know the inclusion order.
This file is a JSON composed of 2 parts:

* `js`: a list of javascript paths (relative to git repository folder: `vt-*`) sorted by import order
* `css`: a list of CSS file paths (relative to git repository folder: `vt-*`) sorted by import order

Example:

```json
{
  "js": [
    "vt-curve/D3Curve.js"
  ],
  "css": [
    "vt-curve/css/custom.css"
  ]
}
```

### viztool_def.json

This file will be read at startup.
It is used to reference the viztool database being integrated in the GUI of the current instance.

In other words, it let IKATS knows how to use the viztool in IKATS environment.

#### Description of the expected JSON content

This aims at providing information about the viztool. It intends to be used by IKATS to bind the GUI viztool parts (inputs, parameters and outputs) to the javascript script viztool.

| JSON field | Type    | Required | Description                                                                                                                         | Constraints                                                                     |
| ---------- | ------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| name       | String  | Yes      | Viztool internal name                                                                                                               | Unique across the IKATS viztools database. Shall match regexp `^[a-zA-Z0-9_]+$` |
| types      | Array   | Yes      | list of allowed input types. ([see this page](IKATS_types.md))                                                                      |                                                                                 |
| classRef   | `Class` | Yes      | Internal class name extending `VizTool`                                                                                             | Shall be a valid className, **not a string**!                                   |
| keyMap     | Object  | No       | Keyboard shortcuts helping user to understand viztools features. `key` is the shortcut and `value` is the description of the action |                                                                                 |
| desc       | String  | No       | Global description of the viztool                                                                                                   |                                                                                 |

#### Example

Given the following javascript viztool signature:

```javascript

class D3Curve extends VizTool {
    constructor(container, data, callbacks) {
        // Call super-class constructor
        super(container, data, callbacks);

        // ...
    }

    // ...
```

The corresponding JSON content could be:

```json
{
    name: "Curve",
    types: ["ts_list", "ts_bucket"],
    classRef: D3Curve,
    keyMap: {
        "drag": "Zoom on an area (may be vertical or horizontal)",
        "shift + drag": "Pan",
        "double click": "Reset zoom",
        "click on legend": "Toggle on/off a curve display"
    },
    desc: "Plot one or several time series"
}
```

Allowed **Types** are defined [in this page](IKATS_types.md)

## Create your viztool

### Prerequisites

To develop a VizTool, there are few prerequisites:

* VizTool must be written in `ES5` or `ES6`
* VizTool must not use `AngularJS`
* Your contribution has to extend(implement) the [`VizTool` class](https://github.com/IKATS/gui-builder/blob/master/src/js/VizModule/VizTool.js) as [template provided in Git repository (latest version)](https://github.com/IKATS/gui-builder/blob/master/src/js/VizModule/VizToolsImplems/ExampleTemplate.js)
* You should only use available javascript libraries in the IKATS GUI or package your own libraries and declare them into the [manifest.json](#manifestjson)
  * List of available javascript libraries is available [here](https://github.com/IKATS/gui-builder/NOTICE)

### JS API Explanation

In order to manipulate IKATS data (time series, meta data, ...), you may use IKATS API on client side.
This API can be found under `ikats.api` in your code.
The several endpoints are able to be consulted into the `ikats_api.js` file (located in `gui-builder` [`src/js (latest version)`](https://github.com/IKATS/gui-builder/blob/master/src/js/ikats_api.js)), each resource to manipulate are located in a sub variable (ex: timeseries are under `ikats.api.ts`, metadata are availabled under `ikats.api.md`, ...).

Example usage for reading a time series:

```javascript
ikats.api.ts.read(
    {
        "tsuid": "ABCDEF",
        "async": true,
        "success": function(result){...},
        "error": function(error){...},
        "complete": function(xhr){...}
    }
);
```

_Note_: every api call should use its asynchronous form (when it is available for the endpoint): Synchronous version may imply high latency into the user interface when trying to get big set of data.

Async version is written with the specification of the async parameter to true and with the definition of any of the 3 behaviour callbacks:

* `success`: called when the result of the call is OK
* `error`: called when an error occurred during the call
* `complete`: called after completion of `success` or `error` (only one time)

### Coding rules

Even if contribution is seen as a *black box* by IKATS, it is easier to understand the purpose of the entry points.
We recommend to be compliant as much as possible with the JSHINT standards using [this file](https://github.com/IKATS/gui-builder/blob/master/src/.jshintrc)

### Comment rules

If you need some support from IKATS team, consider commenting your code properly.
A good start is to have at least 10% of lines as comment.

We use `JSDoc` for our documentation.
In order to include the VizTool documentation to our documentation, you have to specify namespaces as:

```javascript
/**
 * Definition of the contributor namespace (anywhere in delivered code)
 * @namespace CONTRIBUTOR_NAME
 */
```

After that, you may want to scope your code into sub-modules, to do so, use class definition:

```javascript
/**
 * Viztool permitting to display ... ... ...
 *
 * @class CONTRIBUTOR_NAME.VizTool_Name
 * @memberOf CONTRIBUTOR_NAME
 * @constructor
 * @param {string} container - the ID of the injected div
 * @param {Object} data - the data used in the visualization
 * @param {Array} callbacks - the list of the callbacks used by Viz
 */
 class VizXXXXXX extends VizTool {
    .....
 }
```

For a cleaner documentation (in some cases), you can also use alias tag which permits to keep hierarchical structure
without printing too large names.

```javascript

/**
 * @class CONTRIBUTOR_NAME.VizTool_Name.func
 * @memberOf CONTRIBUTOR_NAME.VizTool_Name
 * @alias func
 */
const func = function(){...}

```

### Logging rules

Contributor shall use standard console logger to log any kind of information.
About the verbosity/level:

* **console.error** should be restricted to fatal errors the VizTools won't handle (reason of incomplete or bad rendering)
* **console.warn** should be used to indicate an alternative behavior than the nominal one
* **console.info** should be used to log information over performances (rendering time, number of points loaded,...)
* **console.log** can be used for any information. No limitation.
* ~~**console.debug**~~ should be reserved for development purposes and should not be present in delivered packages.

### Test your VizTool in the IKATS Sandbox

* Prepare your environment
  * Download the [IKATS sandbox](https://github.com/IKATS/ikats-sandbox)
  * Before starting the sandbox, do some changes in the sandbox project
    * Create a `vt-repo-list.yml` file with the following line `- url:/app/local/vt/my_viztool`
    * In `docker-compose.yml`, find the `gui-builder` service and append new volumes:
      ```yaml
      - <path_to_your_viztool>:/app/local/vt/my_viztool:ro
      - ./vt-repo-list.yml:/app/repo-list.yml
      ```
* Start the sandbox: `docker-compose up` (or refer to [repository](https://github.com/IKATS/ikats-sandbox) for more details)

You should be able to perform your tests directly using the IKATS GUI.

### Output builder

In GUI, you can use `output builder` operator to prepare the output content to be used by your viztool.

* Drop it to the *workflow area*
* Write the functional type you declared in [`viztool_def.json`](#viztool_defjson) (field `name`) to let IKATS connect your viztool to the output
* Paste the raw data
* If needed, tick *JSON* to create a `JS Object` based on raw content interpretation.
* Click on `Run` button and double-click on operator to reveal the *results panel* where you can select your viztool
