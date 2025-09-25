## Lung tissue samples

In this case study, we analyse 45 Xenium lung tissue samples from [GSE250346](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE250346), including both healthy and fibrotic conditions.  
A total of 343 genes were measured across samples, each approximately 3–5 mm in diameter, yielding around 299 million detected transcripts [Vannan, Lyu et al, 2025](https://doi.org/10.1038/s41588-025-02080-x).  

We integrate transcripts from all 45 samples by training a GNN model on the combined subgraphs. Graphs and subgraphs are first constructed for each sample individually, and then joined for integrative analysis.  
### Preparing input transcripts

Each sample’s input transcripts should be stored in a CSV file (`SampleName.csv`) containing both spatial coordinates and gene names.  

Before use, all transcript lists must be filtered to remove:  
- Control probes  
- Low-quality detections (e.g., `qv < 20`)  

For multiple samples, the input CSV files should follow the directory structure:  

```bash
data_dir_name/sampleName1/raw/sampleName1.csv
data_dir_name/sampleName2/raw/sampleName2.csv
data_dir_name/sampleName3/raw/sampleName3.csv
```

### Processing steps 

After the input transcripts are prepared as above, we can run spatialRNA to construct spatial transcript radius graphs for each sample, and we sample a subgraph from each sample which will be used for training the GNN model. 

  1. [./workflows/run_generate_subg.smk](https://gitlab.svi.edu.au/biocellgen-public/case_study_ipf/-/blob/main/workflows/run_generate_subg.smk?ref_type=heads)

Now data dir will have new foler "processed" which stores the graph objects, and subgraph data objects

```bash
data_dir_name/sampleName1/processed/sampleName1_data_tile0.pt
data_dir_name/sampleName2/processed/sampleName2_data_tile0.pt
data_dir_name/sampleName3/processed/sampleName3_data_tile0.pt
```

```bash
data_dir_name/sampleName1/subgraph/sampleName1_data_tile0.pt
data_dir_name/sampleName2/subgraph/sampleName2_data_tile0.pt
data_dir_name/sampleName3/subgraph/sampleName3_data_tile0.pt
```

2. [./workflows/run_train_gnn.smk](https://gitlab.svi.edu.au/biocellgen-public/case_study_ipf/-/blob/main/workflows/run_train_gnn.smk?ref_type=heads)

   In this step, we train the GNN model on the combined subgraphs from all 45 samples, as defined in the Snakemake workflow.  

   - All 45 subgraphs are first loaded and merged into a joined graph.  
   - Mini-batches of training graphs are then loaded onto the GPU for efficient training (see `./code/run_GNN_training.py`).  

   If your machine does not have sufficient resources to load all subgraphs into memory, you can use the `SpatialRNAOnDiskDataset` class to manage on-disk batched loading and reduce CPU memory requirements. For example:  

   ```python
   myod = SpatialRNAOnDiskDataset(root="../data/", pt_dir="subgraph")
   ```
    For a more compete example with the SpatialRNAOnDiskDataset usage, please refer to the Case Study of the [5K Ovarian Cancer data](https://gitlab.svi.edu.au/biocellgen-public/case_study_xenium_5k_panel/-/tree/spatialrna02?ref_type=heads).
   
3. [./workflows/run_pred_plot.smk](https://gitlab.svi.edu.au/biocellgen-public/case_study_ipf/-/blob/main/workflows/run_pred_plot.smk?ref_type=heads)

   In this final step, we compute the latent embeddings of all transcripts in each sample.  
   To identify transcript-based molecular niches, we perform clustering on the combined embeddings matrix.  

   - In this case study, we applied a Gaussian Mixture Model (GMM) to cluster transcripts into 12 niches, using the *Pycave* library, which provides GPU acceleration for clustering.  
   - We then use either the `plot_pixel` or `plot_hex` function to visualize the molecular niches spatially.  

   Below, we display pixel plots for several representative samples:



## A healthy lung tissue
![VUHD113](../figures/VUHD113.png)

## A fibrotic lung tissue
![TILD080LA](../figures/TILD080LA.png)


