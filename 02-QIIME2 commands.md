# Commands

## QIIME2 Data Processing and Analyses


Create directory
```bash
mkdir ./dada2
```


Ran dada2 
```bash
qiime dada2 denoise-single \
  --i-demultiplexed-seqs ./deblur/demux-filtered-deblur2.qza \
  --p-trunc-len 275 \
  --o-representative-sequences ./dada2/rep-seq3.qza \
  --o-table ./dada2/table-dada2.qza \
  --o-denoising-stats ./dada2/denoising-stats.qza \
  --p-n-threads 16 \
  --verbose
  ```

Generating statistics 

```bash
qiime metadata tabulate \
--m-input-file ./dada2/denoising-stats.qza \
--o-visualization ./dada2/denoising-stats.qzv
```

Generating the feature table with the denoised table with metadata.

```bash
qiime feature-table summarize \
--i-table ./dada2/table-dada2.qza \
--m-sample-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv \
--o-visualization ./dada2/table-dada2.qzv 
```

```bash
qiime feature-table tabulate-seqs \
--i-data ./dada2/rep-seq3.qza \
--o-visualization ./dada2/rep-seqs.qzv
```

Train a classifier using green genes database

```bash
qiime feature-classifier classify-sklearn \
  --i-classifier /mnt/HDD/samanthag/2024.09.backbone.v4.nb.sklearn-1.4.2.qza \
  --i-reads ./dada2/rep-seq3.qza \
  --p-n-jobs 18 \
  --o-classification ./dada2/taxonomy_greengenes_dada2.qza
```

The output will generate a vizualization of the reuslting mapping from sequence to taxonomy.
```bash
qiime metadata tabulate \
  --m-input-file ./dada2/taxonomy_greengenes_dada2.qza \
  --o-visualization ./dada2/taxonomy_greengenes_dada2.qzv
```

View the taxonomic composition of the samples by generating an interactive bar plots

```bash
qiime taxa barplot \
  --i-table ./dada2/table-dada2.qza \
  --i-taxonomy ./dada2/taxonomy_greengenes_dada2.qza \
  --m-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv \
  --o-visualization ./dada2/taxa-bar-plot_unfiltered_dada2.qzv
```

Remove Unassigned,d__Bacteria

```bash
qiime taxa filter-table \
  --i-table ./dada2/table-dada2.qza \
  --i-taxonomy ./dada2/taxonomy_greengenes_dada2.qza \
  --p-mode exact \
  --p-exclude "Unassigned,d__Bacteria" \
  --o-filtered-table ./dada2/table-dada2-filtered.qza
```


Wanted to check how many seq per sample
```bash
qiime feature-table summarize \
--i-table ./dada2/table-dada2-filtered.qza \
--o-visualization ./dada2/table-summary_filtered.qzv \
--m-sample-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv
```

Remove samples MU-B4-165.0, WHE-345, MURE311 by creating a minimum frequency of 2000 and specifically removing by ID

```bash
qiime feature-table filter-samples \
--i-table ./dada2/table-dada2-filtered.qza \
--p-min-frequency 2000 \
--o-filtered-table ./dada2/sample-frequency-filtered-table.qza
```
Check to make sure the two samples were removed 

```bash
qiime feature-table summarize \
--i-table ./dada2/sample-frequency-filtered-table.qza \
--o-visualization ./dada2/table-summary_filtered2.qzv \
--m-sample-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv
```
Filter out sample by ID
```bash
qiime feature-table filter-samples \
--i-table ./dada2/sample-frequency-filtered-table.qza \
--m-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv \
--p-where '[sample-id]!="MU-B4-165.0"' \
--o-filtered-table ./dada2/final-filtered-table.qza
```
Check to see if it was removed
```bash
qiime feature-table summarize \
--i-table ./dada2/final-filtered-table.qza \
--o-visualization ./dada2/finaltable-summary_filtered.qzv \
--m-sample-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv
```

Create taxa bar plot 

```bash
qiime taxa barplot \
  --i-table ./dada2/final-filtered-table.qza \
  --i-taxonomy ./dada2/taxonomy_greengenes_dada2.qza \
  --m-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv \
  --o-visualization ./dada2/taxa-bar-plot_final-filtered_dada2.qzv
  ```


Created new directory with unfiltered zeros subdirectory
```bash
mkdir ASVs
mkdir ASVs/unfiltered_ASVs
```
output was feature-table.biom will be placed in unfiltered ASVs
```bash
qiime tools export \
--input-path ./dada2/final-filtered-table.qza \
--output-path ./ASVs/unfiltered-ASVs
```
Convert feature-table.biom to .tsv file
```bash
biom convert -i ./ASVs/unfiltered-ASVs/feature-table.biom -o ./ASVs/unfiltered-ASVs/ASV_table.tsv --to-tsv
```
create associated directories
```bash
mkdir ./ASVs/unfiltered-ASVs/taxtable_species
mkdir ./ASVs/unfiltered-ASVs/taxtable_genus
mkdir ./ASVs/unfiltered-ASVs/taxtable_family
```
create by family 5 - family, 6 - genus, 7 - species

```bash
qiime taxa collapse \
--i-table ./dada2/final-filtered-table.qza \
--i-taxonomy ./dada2/taxonomy_greengenes_dada2.qza \
--p-level 5 \
--o-collapsed-table ./ASVs/unfiltered-ASVs/taxtable_family/family_table-dada2.qza
```

```bash
qiime taxa collapse \
--i-table ./dada2/final-filtered-table.qza \
--i-taxonomy ./dada2/taxonomy_greengenes_dada2.qza \
--p-level 6 \
--o-collapsed-table ./ASVs/unfiltered-ASVs/taxtable_genus/genus_table-dada2.qza
```

```bash
qiime taxa collapse \
--i-table ./dada2/final-filtered-table.qza \
--i-taxonomy ./dada2/taxonomy_greengenes_dada2.qza \
--p-level 7 \
--o-collapsed-table ./ASVs/unfiltered-ASVs/taxtable_species/species_table-dada2.qza
```

Converting species to .tsv
```bash
qiime tools export \
--input-path ./ASVs/unfiltered-ASVs/taxtable_species/species_table-dada2.qza \
--output-path ./ASVs/unfiltered-ASVs/biom_table_species
```

```bash
biom convert -i ./ASVs/unfiltered-ASVs/biom_table_species/feature-table.biom -o  ./ASVs/unfiltered-ASVs/biom_table_species/species_table_from_biom.tsv --to-tsv
```

Converting genus to .tsv
```bash
qiime tools export \
--input-path ./ASVs/unfiltered-ASVs/taxtable_genus/genus_table-dada2.qza \
--output-path ./ASVs/unfiltered-ASVs/biom_table_genus
```

```bash
biom convert -i ./ASVs/unfiltered-ASVs/biom_table_genus/feature-table.biom -o ./ASVs/unfiltered-ASVs/biom_table_genus/genus_table_from_biom.tsv --to-tsv
```

Converting family to .tsv

```bash
qiime tools export \
  --input-path ./ASVs/unfiltered-ASVs/taxtable_family/family_table-dada2.qza \
  --output-path ./ASVs/unfiltered-ASVs/biom_table_family
```

```bash
biom convert -i ./ASVs/unfiltered-ASVs/biom_table_family/feature-table.biom -o ./ASVs/unfiltered-ASVs/biom_table_family/family_table_from_biom.tsv --to-tsv
```

Create a path for all taxonomy
```bash
qiime tools export \
--input-path ./dada2/taxonomy_greengenes_dada2.qza \
--output-path ./dada2/taxonomy_export
```

Make dir to place rooted tree information
```bash
mkdir phylogeny
```

Get rooted tree - --p-n-threads 14; when none specified default is 1?
```bash
qiime phylogeny align-to-tree-mafft-fasttree \
--i-sequences ./dada2/rep-seq3.qza \
--o-alignment ./phylogeny/aligned-rep-seqs.qza \
--o-masked-alignment ./phylogeny/masked-aligned-rep-seqs.qza \
--o-tree ./phylogeny/unrooted-tree.qza \
--o-rooted-tree ./phylogeny/rooted-tree.qza \
--p-n-threads 14
```

```bash
qiime tools export \
--input-path ./phylogeny/rooted-tree.qza \
--output-path ./phylogeny/exported-rooted-tree
```

```bash
qiime tools export \
--input-path ./phylogeny/unrooted-tree.qza \
--output-path ./phylogeny/exported-unrooted-tree/
```

Figure out what samples to exclude (based on features/sample) (how does # of features impact metric?)
Find sampling depth (look at sampling-depth-table.qzv) -- pick a sampling depth number for which the greatest difference between samples occurs in the ordered table 

Note: Recommend making your choice by reviewing the information presented in the feature table summary file. Choose a value that is as high as possible (so you retain more sequences per sample) while excluding as few samples as possible.

```bash
qiime feature-table summarize \
  --i-table ./dada2/final-filtered-table.qza \
  --o-visualization ./dada2/sampling-depth-table.qzv \
  --m-sample-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv
```

Create rarefaction Curve
```bash
qiime diversity alpha-rarefaction \
--i-table ./dada2/final-filtered-table.qza \
--i-phylogeny ./phylogeny/rooted-tree.qza \
--p-max-depth 61000 \
--m-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv \
--o-visualization ./alpha-rarefaction_max-61000.qzv

```


![Alpha Rarefraction](./rarefraction_curve.png)



```bash
mkdir ./ASVs/zerofilteredOTU
```

Used Curvcut to see where the minimum feature frequency was 

![curvcut](./Figure_1_curvcut.png)

To maintain the most samples with the highest frequency we agreed on a sampling depth at 5000

Removed in curvcut (minimum frequency: 4)

Converting .csv to .biom

```bash
biom convert -i ./ASVs/ASV_zerofiltered.txt -o ./ASVs/ASV_table_final.biom --table-type="OTU table" --to-json
```

Filter features directly in qiime 

```bash
qiime feature-table filter-features \
--i-table ./dada2/final-filtered-table.qza \
--p-min-frequency 4 \
--o-filtered-table ./dada2/final-filtered-table2.qza
```


Create final path
```bash
qiime tools export \
--input-path ./dada2/final-filtered-table2.qza \
--output-path ./ASVs/filtered
```

```bash
qiime tools import \
--input-path ./ASVs/filtered/feature-table.biom \
--type 'FeatureTable[Frequency]' \
--input-format BIOMV210Format \
--output-path ASVs/filtered/filtered-table_zerofiltered.qza
```

Look at rarefaction slope on zero-filtered features
```bash
 qiime feature-table summarize \
  --i-table ./ASVs/filtered/filtered-table_zerofiltered.qza \
  --o-visualization ./dada2/sampling-depth-table_zero-filtered.qzv \
  --m-sample-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv 
```
make directories 
```bash
mkdir ASVs/zerofilteredOTU/taxtable_species
mkdir ASVs/zerofilteredOTU/taxtable_genus
mkdir ASVs/zerofilteredOTU/taxtable_family
```
Use zero-filtered ASVs to collapse taxonomic assignment with feature table

Family
```bash
qiime taxa collapse \
--i-table ./ASVs/filtered/filtered-table_zerofiltered.qza \
--i-taxonomy ./dada2/taxonomy_greengenes_dada2.qza \
--p-level 5 \
--o-collapsed-table ./ASVs/zerofilteredOTU/taxtable_family/family_table-dada2.qza
```

genus

```bash
qiime taxa collapse \
--i-table ./ASVs/filtered/filtered-table_zerofiltered.qza \
--i-taxonomy ./dada2/taxonomy_greengenes_dada2.qza \
--p-level 6 \
--o-collapsed-table ./ASVs/zerofilteredOTU/taxtable_genus/genus_table-dada2.qza
```
species

```bash
qiime taxa collapse \
--i-table ./ASVs/filtered/filtered-table_zerofiltered.qza \
--i-taxonomy ./dada2/taxonomy_greengenes_dada2.qza \
--p-level 7 \
--o-collapsed-table ./ASVs/zerofilteredOTU/taxtable_species/species_table-dada2.qza
```
species .biom -> .tsv
```bash
qiime tools export \
--input-path ./ASVs/zerofilteredOTU/taxtable_species/species_table-dada2.qza \
--output-path ./ASVs/zerofilteredOTU/biom_table_species
```

```bash
biom convert -i ./ASVs/zerofilteredOTU/biom_table_species/feature-table.biom -o ./ASVs/zerofilteredOTU/biom_table_species/species_table_from_biom.tsv --to-tsv
```

genus .biom -> .tsv
```bash
qiime tools export \
  --input-path ./ASVs/zerofilteredOTU/taxtable_genus/genus_table-dada2.qza \
  --output-path ./ASVs/zerofilteredOTU/biom_table_genus
```

```bash
biom convert -i ASVs/zerofilteredOTU/biom_table_genus/feature-table.biom -o ASVs/zerofilteredOTU/biom_table_genus/genus_table_from_biom.tsv --to-tsv
```
family .biom -> .tsv
```bash
qiime tools export \
--input-path ./ASVs/zerofilteredOTU/taxtable_family/family_table-dada2.qza \
--output-path ./ASVs/zerofilteredOTU/biom_table_family
```

```bash
biom convert -i ASVs/zerofilteredOTU/biom_table_family/feature-table.biom -o ASVs/zerofilteredOTU/biom_table_family/family_table_from_biom.tsv --to-tsv
```

Run max depth at 10000
```bash
qiime diversity alpha-rarefaction \
--i-table ./ASVs/filtered/filtered-table_zerofiltered.qza \
--i-phylogeny ./phylogeny/rooted-tree.qza \
--p-max-depth 10000 \
--m-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv \
--o-visualization ./ASVs/filtered/alpha-rarefaction_max-10k_zerofiltered.qzv
```

![rarefraction @ 10000](./filtered.png)

Diversity analytics 

```bash
qiime diversity core-metrics-phylogenetic \
  --i-phylogeny phylogeny/rooted-tree.qza \
  --i-table ./ASVs/filtered/filtered-table_zerofiltered.qza \
  --p-sampling-depth 5000 \
  --m-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv \
  --output-dir ./diversity/zero-filtered/core-metrics-results
```


```bash
qiime tools export \
--input-path ./diversity/zero-filtered/core-metrics-results/evenness_vector.qza \
--output-path ./diversity/zero-filtered/exported-core-metrics-tables
```

```bash
qiime tools export \
--input-path diversity/zero-filtered/core-metrics-results/faith_pd_vector.qza \
--output-path diversity/zero-filtered/exported-core-metrics-tables
```
```bash
qiime tools export \
--input-path diversity/zero-filtered/core-metrics-results/observed_features_vector.qza \
--output-path diversity/zero-filtered/exported-core-metrics-tables
```
```bash
qiime tools export \
--input-path diversity/zero-filtered/core-metrics-results/shannon_vector.qza \
--output-path diversity/zero-filtered/exported-core-metrics-tables
```

```bash
qiime tools export \
--input-path diversity/zero-filtered/core-metrics-results/unweighted_unifrac_distance_matrix.qza \
--output-path diversity/zero-filtered/exported-core-metrics-tables
```

```bash
qiime tools export \
--input-path diversity/zero-filtered/core-metrics-results/weighted_unifrac_distance_matrix.qza \
--output-path diversity/zero-filtered/exported-core-metrics-tables
```


**********************************************************************************
**********************************************************************************

Note: Used filter features qiime command where min samples is 2 vs min freq is 2
The following was asking to understand the alpha diversity between the unfiltered and filtered.

```bash
qiime feature-table filter-features \
--i-table ./dada2/final-filtered-table.qza \
--p-min-samples 2 \
--o-filtered-table ./dada2/final-filtered-table3.qza
```

```bash
qiime feature-table summarize \
  --i-table ./dada2/final-filtered-table3.qza \
  --o-visualization ./dada2/sampling-depth-table.qzv \
  --m-sample-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv
```

note: diversity-filtered refers to dir min sample of 2 filtered table file `final-filtered-table3.qza` where diversity-unfiltered refers to unfiltered table that was not ran through curvcut after removing specific samples `final-filtered-table.qza`
```bash
qiime diversity core-metrics-phylogenetic \
--i-phylogeny ./phylogeny/rooted-tree.qza \
--i-table ./dada2/final-filtered-table3.qza \
--p-sampling-depth 3000 \
--m-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv \
--output-dir diversity-filtered/filtered/core-metrics-results
```

```bash
qiime diversity core-metrics-phylogenetic \
--i-phylogeny ./phylogeny/rooted-tree.qza \
--i-table ./dada2/final-filtered-table.qza \
--p-sampling-depth 6000 \
--m-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv \
--output-dir diversity-unfiltered/unfiltered/core-metrics-results
```

Dr kelley asked for specific alpha diversity metrics including shannon and evenness

Alpha diversity metrics associated with newly filtered table 
```bash
qiime tools export \
--input-path diversity-filtered/filtered/core-metrics-results/evenness_vector.qza \
--output-path diversity-filtered/filtered/exported-core-metrics-tables
```

```bash
qiime tools export \
  --input-path diversity-filtered/filtered/core-metrics-results/faith_pd_vector.qza \
  --output-path diversity-filtered/filtered/exported-core-metrics-tables
```

```bash
qiime tools export \
  --input-path diversity-filtered/filtered/core-metrics-results/shannon_vector.qza \
  --output-path diversity-filtered/filtered/exported-core-metrics-tables
```
```bash
qiime tools export \
  --input-path diversity-filtered/filtered/core-metrics-results/observed_features_vector.qza \
  --output-path diversity-filtered/filtered/exported-core-metrics-tables
```

Alpha diversity analyses 
```bash
qiime diversity alpha-group-significance \
--i-alpha-diversity ./diversity-filtered/filtered/core-metrics-results/faith_pd_vector.qza \
--m-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv \
--o-visualization ./diversity-filtered/filtered/core-metrics-results/faith_pd_significance.qzv
```

```bash
  qiime diversity alpha-group-significance \
  --i-alpha-diversity ./diversity-filtered/filtered/core-metrics-results/evenness_vector.qza \
  --m-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv \
  --o-visualization ./diversity-filtered/filtered/core-metrics-results/evenness-group-significance.qzv
```

```bash
  qiime diversity alpha-group-significance \
  --i-alpha-diversity ./diversity-filtered/filtered/core-metrics-results/shannon_vector.qza \
  --m-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv \
  --o-visualization ./diversity-filtered/filtered/core-metrics-results/shannon-group-significance.qzv
```

Alpha diversity metrics associated with unfiltered table 

Unfiltered diveristy metrics:

```bash
qiime tools export \
--input-path diversity-unfiltered/unfiltered/core-metrics-results/evenness_vector.qza \
--output-path diversity-filtered/unfiltered/exported-core-metrics-tables
```

```bash
qiime tools export \
  --input-path diversity-unfiltered/unfiltered/core-metrics-results/faith_pd_vector.qza \
  --output-path diversity-unfiltered/unfiltered/exported-core-metrics-tables
```

```bash
qiime tools export \
  --input-path diversity-unfiltered/unfiltered/core-metrics-results/shannon_vector.qza \
  --output-path diversity-unfiltered/unfiltered/exported-core-metrics-tables
```
```bash
qiime tools export \
  --input-path diversity-unfiltered/unfiltered/core-metrics-results/observed_features_vector.qza \
  --output-path diversity-unfiltered/unfiltered/exported-core-metrics-tables
```

Alpha diversity analyses

```bash
  qiime diversity alpha-group-significance \
  --i-alpha-diversity ./diversity-unfiltered/unfiltered/core-metrics-results/faith_pd_vector.qza \
  --m-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv \
  --o-visualization ./diversity-unfiltered/unfiltered/core-metrics-results/faith_pd_significance.qzv
```

```bash
  qiime diversity alpha-group-significance \
  --i-alpha-diversity ./diversity-unfiltered/unfiltered/core-metrics-results/evenness_vector.qza \
  --m-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv \
  --o-visualization ./diversity-unfiltered/unfiltered/core-metrics-results/evenness-group-significance.qzv
```

```bash
  qiime diversity alpha-group-significance \
  --i-alpha-diversity ./diversity-unfiltered/unfiltered/core-metrics-results/shannon_vector.qza \
  --m-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv \
  --o-visualization ./diversity-unfiltered/unfiltered/core-metrics-results/shannon-group-significance.qzv
```


**********************************************************************************
**********************************************************************************

Continued with beta diversity analyses with filtered data 
```bash
qiime tools export \
  --input-path ./diversity-filtered/filtered/core-metrics-results/unweighted_unifrac_distance_matrix.qza \
  --output-path ./diversity-filtered/filtered/exported-core-metrics-tables
```

```bash
qiime tools export \
  --input-path ./diversity-filtered/filtered/core-metrics-results/weighted_unifrac_distance_matrix.qza \
  --output-path ./diversity-filtered/filtered/exported-core-metrics-tables
```


**********************************************************************************
**********************************************************************************

Attempt to download tax info and sequence variants in order to combine

```bash
qiime taxa feature-ids-to-taxonomy \
--i-table ./dada2/final-filtered-table.qza \
--o-taxonomy ./outputs/output-taxonomy.qza
```

**********************************************************************************
**********************************************************************************

06/16 : GENUS LEVEL taxanomic information was asked for by Nick Barber, will be using filtered data 


Create final path
```bash
qiime tools export \
--input-path ./dada2/final-filtered-table3.qza \
--output-path ./ASVpt2/filtered
```

```bash
qiime tools import \
--input-path ./ASVpt2/filtered/feature-table.biom \
--type 'FeatureTable[Frequency]' \
--input-format BIOMV210Format \
--output-path ./ASVpt2/filtered/filtered-table3.qza
```
Looked at rarefraction slope to check the filtered data

```bash
 qiime feature-table summarize \
  --i-table ./ASVpt2/filtered/filtered-table3.qza\
  --o-visualization ./ASVpt2/filtered/sampling-depth-table_zero-filtered.qzv \
  --m-sample-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv 
```

make directory requested GENUS
```bash
mkdir ./ASVpt2/filteredOTU/taxtable_genus
```

```bash
qiime taxa collapse \
--i-table ./ASVpt2/filtered/filtered-table3.qza \
--i-taxonomy ./dada2/taxonomy_greengenes_dada2.qza \
--p-level 6 \
--o-collapsed-table ./ASVpt2/filteredOTU/taxtable_genus/genus_table3-dada2.qza
```
genus .biom -> .tsv

```bash
qiime tools export \
  --input-path ./ASVpt2/filteredOTU/taxtable_genus/genus_table3-dada2.qza \
  --output-path ./ASVpt2/filteredOTU/biom_table_genus
```

```bash
biom convert -i ./ASVpt2/filteredOTU/biom_table_genus/feature-table.biom -o ./ASVpt2/filteredOTU/biom_table_genus/genus_table_from_biom.tsv --to-tsv
```


**********************************************************************************
**********************************************************************************

Check to see whhich table it is...


```bash
qiime feature-table summarize \
--i-table ./dada2/table-dada2.qza \
--m-sample-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv \
--o-visualization ./dada2/table-dada2.qzv 
```







**********************************************************************************
**********************************************************************************



08/27 : FAMILY LEVEL taxanomic information was extracted using filtered data 


make directory requested FAMILY
```bash
mkdir ./ASVpt2/filteredOTU/taxtable_family
```
Taxa collapse for family
```bash
qiime taxa collapse \
--i-table ./ASVpt2/filtered/filtered-table3.qza \
--i-taxonomy ./dada2/taxonomy_greengenes_dada2.qza \
--p-level 5 \
--o-collapsed-table ./ASVpt2/filteredOTU/taxtable_family/family_table3-dada2.qza
```


family .biom -> .tsv

```bash
qiime tools export \
  --input-path ./ASVpt2/filteredOTU/taxtable_family/family_table3-dada2.qza \
  --output-path ./ASVpt2/filteredOTU/biom_table_family
```

```bash
biom convert -i ./ASVpt2/filteredOTU/biom_table_family/feature-table.biom -o ./ASVpt2/filteredOTU/biom_table_family/family_table_from_biom.tsv --to-tsv
```

**********************************************************************************
**********************************************************************************