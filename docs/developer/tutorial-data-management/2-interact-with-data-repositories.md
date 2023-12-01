---
title: Interact with data repositories
author: Paul van Genuchten
date: 2023-05-09
---

# Interact with data repositories

In this section a crawler tool is introduced which let's you interact wit the metadata in a file based data repository. 

For this exersice we've prepared a minimal data repository containing a number of Excel-, Shape- and Tiff-files. Unzip the repository to a location on disk. 

Notice a index.yml in the root folder. The tool we use is able to inherit metadata properties from index.yml files through the file hierarchy. Open index.yml and customise the contact details. Later you will notice that these details will be applied to all datasets which themselves do not provide contact details. Consider to add additional index.yml files in other folders to override the values of index.yml at top level.

The tool we will use is based on python. It has some specific dependencies which are best installed via [Conda](https://conda.io). Conda creates a virtual python environment, so any activity will not interfere with the base python environment of your machine.

If you don't have Conda, you can install [Anaconda](https://www.anaconda.com/download) or [Miniconda](https://docs.conda.io/en/latest/miniconda.html#installing) and consider to read the [getting started](https://docs.conda.io/projects/conda/en/stable/user-guide/getting-started.html).

Now start a commandline or powershell with conda enabled (or add conda to your PATH). On windows look for the `Anaconda prompt` in start menu.
First we will navigate to the folder in which we unzipped the sample data repository. Make sure you are not in the `data` directory but one above.

```bash
cd {path-where-you-unzipped-zipfile}
```

We will create a virtual environment `pgdc` (using Python 3.9) for our project and activate it.

```bash
conda create --name pgdc python=3.9 
conda activate pgdc
```

Notice that you can deactivate this environment with: `conda deactivate` and you will return to the main Python environment. The tools we will install below, will not be available in the main environment.

Install the dependencies for the tool:

```
conda install -c conda-forge gdal==3.3.2
conda install -c conda-forge pysqlite3==0.4.6
```

Now install the crawler tool, [GeoDataCrawler](https://pypi.org/project/geodatacrawler/). The tool is under active development at ISRIC and facilitates many of our data workflows. It is powered by some popular metadata and transformation libraries; [OWSLib](https://github.com/geopython/OWSLib), [pygeometa](https://github.com/geopython/pygeometa) and [GDAL](https://gdal.org).

```
pip install geodatacrawler
```

Verify the different crawling options by typing:

```
crawl-metadata --help
```

The initial task for the tool is to create for every data file in our repository a sidecar file based on embedded metadata from the resource.

```
crawl-metadata --mode=init --dir=data
```

Notice that for each resource a {dataset}.yml file has been created. Open a .yml file in a text editor and review its content.

The `update` mode is meant to be run at intervals, it will update the mcf files if changes have been made on a resource. 

```
crawl-metadata --mode=update --dir=data
```

In certain cases the update mode will also import metadata from remote url's. This happens for example if the dataset-uri is a [DOI](https://www.doi.org/the-identifier/what-is-a-doi/). The update mode will ten fetch metadata of the DOI and push it into the MCF. 

Finally we want to export the MCF's to actual iso19139 metadata to be loaded into a catalogue like pycsw, GeoNetwork, CKAN etc.

```
crawl-metadata --mode=export --dir=data --dir-out=export --dir-out-mode=flat
```

Open one of the xml files and evaluate if the contact information from step 1 is available.

## Summary

In this paragraph you are introduced to the MDME MCF editor and pygeometa library. In the [next section]() we are looking at bulk metadata imports.