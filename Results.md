# Qiime2 Report

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

sdsds 
