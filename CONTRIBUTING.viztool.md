# CONTRIBUTING to viztool

The purpose of this document is to describe how a contributor will develop and propose
a viztool for IKATS.

This document does not focus on the installation of the development means (docker, IDE ...) and assumes that the developer environment is fully operational. For contributors that already have installed the [IKATS Sandbox](https://github.com/IKATS/ikats-sandbox), all the environnement for Docker and git is setup.

## Definitions

List of some terms used in this document

* **VizEngine** : The _VizEngine_ is a GUI component managing and suggesting _VizTools_ according to the type of an _operator_ output, in a workflow.
  The _VizEngine_ also handle a _VizTool_ chain (represented in GUI by a bread-crumb).
* **VizTool** : the graphical container displayed on GUI allowing to visualize the output of an _operator_
  A _VizTool_ is called so if and only if :
  * the code building the container implements the _VizTool_ class,
  * the _VizTool_ is made available in the GUI for one or several IKATS [functional types](IKATS_types.md),
  * the _VizTool_ have to be available into the _VizToolsLibrary_,

## Repository content

* A viztool repository begins with `vt-` to quickly identify it. This an internal practice used for our scripts, you could choose to ignore it.
* It is composed of at least the following files (detailed below):
  * `LICENSE`: License of the contribution
  * `NOTICE`: To list all the dependencies of the contribution. That document is a good practice and [is mandatory for Apache Licence, version 2](http://apache.org/dev/apply-license.html).
  * `README.md`: Description of the viztool
  * `viztool_def.json`: definition of the viztool in the catalog
  * `manifest.json`: definition of the inlude order and libraries used
  * `*.js`: viztool code implementing the _VizTool_ class

### README.md

For understanding purposes, the `README.md` file should :

* describe the purpose of the viztool
* explain some aspects of the internal behaviour (this can be described in another file)

### manifest.json

To handle multiple resources files, this file will help IKATS to know the inclusion order.
This file is a JSON composed of at most 3 parts : `js`, `css` and `lib`.

* `js` : a list of javascript paths (relative to git repository folder: `vt-*`) sorted by import order
* `css` : a list of CSS file paths (relative to git repository folder: `vt-*`) sorted by import order
* `lib` : a list of tiers libraries name (this part is only informative). Each item of the list is composed of:
  * `name`: the name of the library
  * `version`: its version

Example:

```json
{
  "js": [
    "vt-curve/D3Curve.js"
  ],
  "lib": [
    {
      "name": "d3",
      "version": "4.4.0"
    },
    {
      "name": "jquery",
      "version": "3.2.1"
    },
    {
      "name": "bootstrap",
      "version": "3.3.7"
    }
  ]
}
```

### viztool_def.json

This file will be read at startup.
It is used to reference the viztool database beeing integrated in the GUI of the current instance.

In other words, it let IKATS knows how to use the viztool in IKATS environment.

#### Description of the expected JSON content

This aims at providing information about the viztool. It intends to be used by IKATS to bind the GUI viztool parts (inputs, parameters and outputs) to the javascript script viztool.

| JSON field | Type    | Required | Description                                                                                                              | Constraints                                                                     |
| ---------- | ------- | -------- | ------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------- |
| name       | String  | Yes      | Viztool internal name                                                                                                    | Unique across the IKATS viztools database. Shall match regexp `^[a-zA-Z0-9_]+$` |
| type       | Array   | Yes      | list of accepted type for input ([see this page](IKATS_types.md) for list of allowed types                               |                                                                                 |
| classRef   | `Class` | Yes      | Internal class name extending `VizTool`                                                                                  | Shall be a valid className, **not a string**!                                   |
| keyMap     | Object  | No       | Keyboard shortcuts used by this viztool where the *key* is the shortcut and the *value* is the description of the action |                                                                                 |
| desc       | String  | No       | Global description of the viztool                                                                                        |                                                                                 |

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
* A VizTool must implement [the template provided in Git repository.](https://github.com/IKATS/gui-builder/blob/master/src/js/VizModule/VizToolsImplems/ExampleTemplate.js)
* You should only use available javascript libraries or package their own libraries

List of available javascript libraries is available [here](https://github.com/IKATS/gui-builder/NOTICE)

### JS API Explanation

In order to manipulate IKATS data (time series, meta data, ...), you may use IKATS API on client side.
This API can be found under `ikats.api` in your code.
The several endpoints are able to be consulted into the `ikats_api.js` file (located in `gui-builder` [`src/js`](https://github.com/IKATS/gui-builder/blob/master/src/js/ikats_api.js)), each resource to manipulate are located in a sub variable (ex : timeseries are under `ikats.api.ts`, metadata are availabled under `ikats.api.md`, ...).

Example usage for reading a time series :

```javascript
ikats.api.ts.read(
    {
        "tsuid" : "ABCDEF",
        "async" : true,
        "success" : function(result){...},
        "error" : function(error){...},
        "complete" : function(xhr){...}
    }
);
```

_Note_ : Every api call should use its asynchronous form (when it is available for the endpoint) : Synchronous version may imply high latency into the user interface when trying to get big set of data.

Async version is written with the specification of the async parameter to true and with the definition of any of the 3 behaviour callbacks:

* `success` : called when the result of the call is OK
* `error` : called when an error occurred during the call
* `complete` : called after completion of `success` or `error` (only one time)

You can have a look at some [IKATS viztools](https://github.com/IKATS?q=vt-) for some examples

### Coding rules

Even if contribution is seen as a *black box* by IKATS, it is easier to understand the purpose of the entry points.
We recommend to be compliant as much as possible with the JSHINT standards using [this file](https://github.com/IKATS/gui-builder/blob/master/src/.jshintrc)

### Comment rules

If you need some support from IKATS team, consider commenting your code properly.
A good start is to have at least 10% of lines as comment.

We use `JSDoc` for our documentation.
In order to include the VizTool documentation to our documentation, you have to specify namespaces as :

```javascript
/**
 * Definition of the contributor namespace (anywhere in delivered code)
 * @namespace CONTRIBUTOR_NAME
 */
```

After that, you may want to scope your code into sub-modules, to do so, use class definition :

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
