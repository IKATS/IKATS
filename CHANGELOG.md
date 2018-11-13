# Release Notes

## 0.11.0 - 2018-10-16

### Improvements

New functionality : Created ExportTable operator to download tables as CSV.

New operator : *Scale* operator aims at normalizing TS, with three scaler modes.

The operator *Rollmean* has been updated to use Spark dataframes.

* **gui-builder**: added IKATS version in the GUI footer
* **gui-builder**: updated LICENSE headers
* **gui**: new front server / reverse proxy

---

## Version 0.10.1 - 2018-08-31

### Improvements
* **ikats-baseimages** and **ikats-pybase** : updated spark version to 2.3.1
* **gui-builder** and **ikats-datamodel**: updated message returned to user in case of operator error

### Fixes
* **op-resampling**: changed upsampling VALUE_BEFORE or VALUE_AFTER behaviour on a single point chunk
* **vt-sax**: fix on the viztool to use correct input name to know the source dataset

---

## Version 0.10.2 - 2018-08-31

This release concerns installation - this has no impact on the user.

---

## Version 0.10.0 - 2018-07-30

### Improvements
A new operator *Export TS*  allows to export TS in .csv files , in order to do an archive, to reimport the TS in a new instance, or to use them outside IKATS.
The operator *Cut TS* has been extended to manage long and/or many TS (using Spark).


### Fixes

Fixed API call (concerns operators sax, rollmean, discretization).
* **gui-builder**: replaced ikats by IKATS + removed copyright date in footer
* **ikats-datamodel**: added table API  V2 + headers management
* **ikats-pybase**: Fixed versions for python modules
* **ikats-pybase**: fixed gunicorn version


## Version 0.9.0 - 2018-07-05

### Improvements

* **gui-builder**: default viztool displayed depending on output type
* **gui-builder**: updated families of core operators
* **op-discretization**: updated family for operator discretize
* **operator-fetcher**: catalog modification to be robust to evolutions on operator (addd/replace / delete)

### Fixes

* **ikats-pybase**: updated `Requests` python module to fix 'Connection pool is full'
* **catalog def fixes**

---

## Version 0.8.0 - 2018-06-20

### Improvements

* **ikats-datamodel**: updated path to operators
* **ikats-pybase**: separated viztools and operators from the main code base
* **ikats-sandbox**: operator-fetcher linked to sandbox
* **ikats-sandbox**: updated docker-compose configuration
* **ikats-sandbox**: updated image to python base
* **ikats-sandbox**: updated with `gui-builder`
* **operator-fetcher**: created to handle operators to use in IKATS instance
* **gui-builder**: created to handle viztools to use in IKATS instance
* **op-correlation**: created
* **op-cut_y**: created
* **op-decision_tree**: created
* **op-decision_tree_cv**: created
* **op-discretization**: created
* **op-kmeans**: created
* **op-paa**: created
* **op-pattern**: created
* **op-quality_stats**: created
* **op-resampling**: created
* **op-rollmean**: created
* **op-sax**: created
* **op-slope**: created
* **op-ts_cut**: created
* **op-unwrap**: created
* **vt-cluster**: created
* **vt-correl-loop**: created
* **vt-curve**: created
* **vt-metadata**: created
* **vt-metadata-list**: created
* **vt-non-temporal-curve**: created
* **vt-pattern**: created
* **vt-percent**: created
* **vt-random-projection**: created
* **vt-raw**: created
* **vt-sax**: created
* **vt-scatter-plot-ts**: created
* **vt-table**: created
* **vt-text**: created
* **vt-ts-table**: created
* **vt-tsuid**: created

---

## Version 0.7.39 - 2018-05-28

First public release
