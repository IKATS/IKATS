# Developing with Jupyter notebooks for IKATS

## Purpose

This document will indicate how to work with IKATS using any jupyter notebook.

## Prerequisites

To be able to follow this guide, you shall be familiar with the following technologies:

- Jupyter notebook / Jupyter hub
- python3
- pip

and have access to a cluster running jupyter hub with a working Internet access.

## Configuration

In your recent web browser, go to the jupyter notebook URL of the cluster : [http://jupyter.your_ikats_cluster.corp](http://jupyter.your_ikats_cluster.corp)

If you encounter a "Sign In" panel, choose a username and a password, a workspace will be created (for new username) and opened (if username exists and password is valid)

Now you have your own personal environment, you can start installing the needed modules.

Create a new terminal and run the following snippet:

```sh
# Upgrade pip
pip install --upgrade pip

# Install IKATS API from sources
pip install git+http://github.com/ikats/ikats_api

# Install Tutorial.ipynb from latest IKATS API version
wget https://raw.githubusercontent.com/IKATS/ikats_api/master/Tutorial.ipynb
```

## Developing

You can now create a new notebook(`*.ipynb` file) to begin your work or open an existing one.

If you are discovering IKATS, it is recommended to start with "Tutorial.ipynb".  
Don't forget to modify the API instance creation by connecting to your host using: `api = IkatsAPI(host="http://your_ikats_cluster.corp")`

### Development rules

In order to ease the integration of the contribution, some rules must be applied.

- The notebook uses IKATS python API to communicate with IKATS
- The notebook doesn't use modules other than those specified in IKATS
- The notebook shall run all cells from top to bottom without any "cells jump" without errors
- The documentation of the notebook entry point is sufficient to create `catalog.json`
- The inputs/outputs types are compliant with available IKATS types
