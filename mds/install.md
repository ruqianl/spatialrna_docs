# Installation 

SpatialRNA - A package that provides easier inferface to GNN models for single-molecule ST data analysis

## Using Conda and pip

```console
conda create -n dev-spatialrna
conda activate dev-spatialrna
conda install python=3.10.0
```

## torch 2.4.0 cuda version 12.4
```console
pip install torch==2.4.0 --index-url https://download.pytorch.org/whl/cu124
```

## torch_geometric=2.6.1 

https://pytorch-geometric.readthedocs.io/en/stable/install/installation.html#installation-via-pypi

```console
pip install torch_geometric==2.6.1
```

## Additional useful libraries 

https://pytorch-geometric.readthedocs.io/en/stable/install/installation.html#additional-libraries

```console
pip install pyg_lib torch_scatter torch_sparse torch_cluster torch_spline_conv -f https://data.pyg.org/whl/torch-2.4.0+cu124.html
```

## Install spatialrna

```console
pip install spatialrna
```

## Manuscript

[Lyu et al, bioRxive](https://doi.org/10.1101/2025.03.20.644258)