# Digital Cell Sorter
Identification of hematological cell types from heterogeneous single cell RNA-seq data.

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes.

### Prerequisites



The code runs in Python>=3.7 environment. 

It is highly recommended to install Anaconda.
Installers are available at https://www.anaconda.com/distribution/

It uses packages numpy, pandas, matplotlib, scikit-learn, scipy, mygene, fftw, pynndescent, networkx, python-louvain, fitsne, and multiple standard Python packages. All these packages are installed with installation of DigitalCellSorter:

```
pip install DigitalCellSorter
```

### Input Data Format

The input data is expected in a format of pandas DataFrame provided as input to the ```process()``` function. The dataframe has genes as rows and ('patients/batchs', 'cells') as columns, and each grid is the expression of that gene in the cell. 

### Other Data

```geneLists/CIBERSORT.xlsx```: An excel sheet with marker data. Rows are markers and columns are cell types. '1' means that the gene is a marker for that cell type, and '0' otherwise.

## Method

The main class for cell sorting functions and producing output images is DigitalCellSorter located in scrDigitalCellSorteripts/DigitalCellSorter.py. A class instance need to be created with:
```DCS = DigitalCellSorter.DigitalCellSorter()``` 

During the initialization the following parameters can be specified:

            ```dataName```: name used in output files, Default ''

            ```geneListFileName```: marker cell type list name, Default None

            ```mitochondrialGenes```: list of mitochondrial genes for quality conrol routine, Default None

            ```sigmaOverMeanSigma```: threshold to consider a gene constant, Default 0.3

            ```nClusters```: number of clusters, Default 5

            ```nComponentsPCA```: number of pca components, Default 100

            ```nSamplesDistribution```: number of random samples to generate, Default 10000

            ```saveDir```: directory for output files, Default is current directory

            ```makeMarkerSubplots```:  whether to make subplots on markers, Default True

            ```makePlots```: whether to make all major plots, Default True

            ```votingScheme```: voting shceme to use instead of the built-in, Default None

            ```availableCPUsCount```: number of CPUs available, Default os.cpu_count()

            ```zScoreCutoff```: zscore cutoff when calculating Z_mc, Default 0.3

            ```clusterName```: parameter used in subclustering, Default None

            ```doQualityControl```: whether to remove low quality cells, Default True

            ```doBatchCorrection```: whether to correct data for batches, Default False

These and other parameters can be modified after initialization using, e.g.:
```DCS.toggleMakeStackedBarplot = False```
```DCS.dataName = "someDataName"```



The ```process()``` function will produce all necessary files for post-analysis of the data. The visualization tools include:
 
- ```makeMarkerExpressionPlot()```: an image that shows the clustering of cells and identified cell type of each cluster. 

 <img src="https://github.com/sdomanskyi/DigitalCellSorter/blob/master/output/BM1/BM1_voting.png" align="center" height="500" width="500" >


- ```makeMarkerSubplot()```: z-scores of the voting results for each input cell type and each cluster, in addition this figure contains relative (%) and absolute (cell counts) cluster sizes 

 <img src="https://github.com/sdomanskyi/DigitalCellSorter/blob/master/output/BM1/BM1_clusters_by_patients.png" align="center" width="800" >


- ```makeVotingResultsMatrixPlot()```: a heatmap that shows all markers and their expression levels in the clusters

<img src="https://github.com/sdomanskyi/DigitalCellSorter/blob/master/output/BM1/BM1_matrix_voting.png" align="center" height="1000">


- ```makeHistogramNullDistributionPlot()```: a heatmap that shows all markers and their expression levels in the clusters

<img src="https://github.com/sdomanskyi/DigitalCellSorter/blob/master/output/BM1/BM1_null_distributions.png" align="center" height="1000">


- ```makeTSNEplot()```: a heatmap that shows all markers and their expression levels in the clusters

<img src="https://github.com/sdomanskyi/DigitalCellSorter/blob/master/output/BM1/BM1_clusters_by_clusters_annotated.png" align="center" height="1000">


- ```makeStackedBarplot()```: a heatmap that shows all markers and their expression levels in the clusters

<img src="https://github.com/sdomanskyi/DigitalCellSorter/blob/master/output/BM1/BM1_subclustering_stacked_barplot_.png" align="center" height="1000">


- ```makeQualityControlHistogramPlot()```: a heatmap that shows all markers and their expression levels in the clusters

<img src="https://github.com/sdomanskyi/DigitalCellSorter/blob/master/output/BM1/QC_plots/BM1_fraction_of_mitochondrialGenes_histogram.png" align="center" height="1000">


- ```makeSankeyDiagram()```: a heatmap that shows all markers and their expression levels in the clusters

<img src="https://github.com/sdomanskyi/DigitalCellSorter/blob/master/output/BM1/Sankey_exampe.png" align="center" height="1000">


- ```makePlotOfNewMarkers()```: a heatmap that shows all markers and their expression levels in the clusters

<img src="https://github.com/sdomanskyi/DigitalCellSorter/blob/master/output/BM1/QC_plots/BM1_new_markers.png" align="center" height="1000">


## Demo

### Usage

We have made an example execution file ```demo.py``` that shows how to use DigitalCellSorter.

In the demo, folder ```data``` is intentionally left empty. The reader can download the file ```ica_bone_marrow_h5.h5``` from https://preview.data.humancellatlas.org/ (Raw Counts Matrix - Bone Marrow) and place in folder ```data```. The file is ~485Mb and contains all 378000 cells from 8 bone marrow donors. In our example, the MatrixMarket IJV format files are prepared automatically on the fly for each donor separately using ```ReadPrepareDataHCApreviewDataset.PrepareData()```. For detail see ```demo.py```.







The demo.py first converts the data to a ```pandas``` dataframe as input for Process function. Then it calls ```Process()``` function from ```DigitalCellSorter``` class, which does all the projection, clustering, identification and images production.

To see our example work, you just need to download everything, go to the directory then run

```
python DCS_demo_on_HCA_BM.py
```
Note that to use your own data, you would also need to convert them to a ```pandas``` dataframe. You can also customize the parameters in ```process()``` function as listed above. For example, you can change it to

```
    dataName = 'BM1'
    HCAtool.PrepareDataOnePatient(os.path.join('data', 'ica_bone_marrow_h5.h5'), dataName, os.path.join('data', ''), useAllData=False, cellsLimitToUse=3000)
    df_expr = pd.read_hdf(os.path.join('data', 'HCA_%s.h5'%(dataName)), key=dataName, mode='r')

    DCS = DigitalCellSorter.DigitalCellSorter(nSamplesDistribution=1000, nClusters=20)
    DCS.dataName = dataName
    DCS.saveDir = os.path.join('output', dataName, '')
    DCS.geneListFileName = os.path.join('geneLists', 'CIBERSORT.xlsx')
    DCS.process(df_expr)

    DCSsub = DigitalCellSorter.DigitalCellSorter(dataName=dataName, nClusters=10, doQualityControl=False, nSamplesDistribution=1000)

    DCSsub.subclusteringName = 'T cell'
    DCSsub.saveDir = os.path.join('output', dataName, 'subclustering T cell', '')
    DCSsub.geneListFileName = os.path.join('geneLists', 'CIBERSORT_T_SUB.xlsx')
    DCSsub.process(df_expr[DCS.getCells(celltype='T cell')])

    DCSsub.subclusteringName = 'B cell'
    DCSsub.saveDir = os.path.join('output', dataName, 'subclustering B cell', '')
    DCSsub.geneListFileName = os.path.join('geneLists', 'CIBERSORT_B_SUB.xlsx')
    DCSsub.process(df_expr[DCS.getCells(celltype='B cell')])
```

### Output

All the output files are saved in ```output``` directory. 

