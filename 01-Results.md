
# Results

## QIIME2 Report

## Metadata and Sequence Information

***note: All files ending with .qzv are visualizations and can be viewed on [https://view.qiime2.org](https://view.qiime2.org)***

- Metadata submitted: [Nachusa_16s_all_metadata_edited2.tsv]()

- Sequences submitted:  **Forward**

- Initial Sequence statistics (Feature Table): [demultiplexed-seqs2.qzv]()

![Screenshot of feature table including sequence statistics](./files/demultiplex-seq2.png)

### Quality Plot

![Screenshot of quality plot that displays quality scores among samples](./files/quality-plot.png)

Quality drops around sequence base 140 and drops again at 280. **Quality scores >Q25 are the benchmark**.

## Denoising and Sequence Variants

- Denoiser used: **DADA2**

- Length was truncated to **275**

- Denoising statistics .tsv file can be downloaded from [https://view.qiime2.org](https://view.qiime2.org): [denoising-stats.qzv]()

- Representative sequences: [rep-seqs.qzv]() 

- Feature table statistics: [table-dada2.qzv]()


![Screenshot 1 including table summary](./files/table-dada2.png)

![Screenshot of Freq per table](./files/table-dada2-0.png)

![Screenshot of frequency per table bar graph](./files/table-dada2-1.png)

![Screenshot of Frequency per feature bar graph](./files/table-dada2-2.png)


## Visualizations

- Database used for taxanomic classification: **greengenes**

- taxonomic mapping from sequences .tsv file can be downloaded from **Qiime2 Viewer**: [taxonomy_greengenes_dada2.qzv]()

- Interactive Bar Plot was filtered, removing unknown Bacteria: [taxa-bar-plot_final-filtered_dada2.qzv]()

![Screenshot of taxonomic bar plot](./files/filtered-interactive-barplot.png)

***

## Principal Component Analysis (PCA)

***

## Random Forest Classification and Regression - GENUS

### <u>Classification</u>

Categorical Environmental Features Used, Accuracy, Classification Report, and top 5 species

**site**

![Screenshot of site classification report](./regression_images/site_genus_class.png)

**quadrat**

![Screenshot of quadrant classification report](./regression_images/quadrat_genus_class.png)

**site type**

![Screenshot of site type classification report](./regression_images/sitetype_genus_class.png)

**restoration year**

![Screenshot of restoration year classification report](./regression_images/restorationyear_genus_class.png)

**bison**

![Screenshot of bison classification report](./regression_images/bison_genus_class.png)

**fire 2022**

![Screenshot of fire 2022 classification report](./regression_images/fire2022_genus_class.png)

### <u>Regression</u> 

 **$R^2$** - measures the proportion of variance in your data explained by a model

 **Root Meaned Squared Error (RMSE)** - calculates the square root of the average squared differences between your predicted values and the actual observed values, closer to 0 means the predictions match the actual data 

Continuous Environmental Features Used

- plantrichness

- plantshannon

- c4grass_percent_cover

- c4grass_proportion_cover

- nfix_percent_cover

- nfix_proportion_cover

- woody_percent_cover

- graminoid_cover

- forb_cover

- gram_nongram_cover_ratio

- perc_water

- aboveground_plant_biomass_g

- betaglucosidase_nmol_g_h

- NAG_nmol_g_h

- acid_phosphomonoesterase_nmol_g_h

- polyphenoloxidase_nmol_g_h

- peroxidase_nmol_g_h

- soil_percent_water

- plant_FDis

- plant_FDis_leaf_traits_only



![Genus Regression table](./regression_images/genus_regression.png)

***

## Random Forest Classification and Regression - FAMILY

The following include: 

- Categorical Environmental Features Used

- Accuracy 

- Classification Report

- Top 5 species

### <u>Classification</u>

**site**

![Screenshot of site classification report](./regression_images/site_family_class.png)

**quadrat**

![Screenshot of quadrat classification report](./regression_images/quadrant_family_class.png)

**site type**

![Screenshot of site type classification report](./regression_images/sitetype_family_class.png)

**restoration year**

![Screenshot of restoration year classification report](./regression_images/restorationyear_family-class.png)

**bison**

![Screenshot of bison classification report](./regression_images/bison_family_class.png)

**fire 2022**

![Screenshot of fire 2022 classification report](./regression_images/fire2022_family_class.png)

### <u>Regression</u> 

 **$R^2$** - measures the proportion of variance in your data explained by a model

 **Root Meaned Squared Error (RMSE)** - calculates the square root of the average squared differences between your predicted values and the actual observed values, closer to 0 means the predictions match the actual data 


Continuous Environmental Features Used

- plantrichness

- plantshannon

- c4grass_percent_cover

- c4grass_proportion_cover

- nfix_percent_cover

- nfix_proportion_cover

- woody_percent_cover

- graminoid_cover

- forb_cover

- gram_nongram_cover_ratio

- perc_water

- aboveground_plant_biomass_g

- betaglucosidase_nmol_g_h

- NAG_nmol_g_h

- acid_phosphomonoesterase_nmol_g_h

- polyphenoloxidase_nmol_g_h

- peroxidase_nmol_g_h

- soil_percent_water

- plant_FDis

- plant_FDis_leaf_traits_only


![Family Regression table](./regression_images/family_regression.png)

***

## Network Analysis

***