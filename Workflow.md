# Workflow

## Below is a SUMMARIZED idea of the workflow, currently IN-PROGRESS

# Part 1: Data Prep and Quality Control

Convert your raw sequencing fastq files(.fasta, .gz, .fa) into a QIIME 2 artifact (.qza). This command demultiplexes your sequences using the barcodes located in the sequences.

```bash
qiime tools import \
--type MultiplexedSingleEndBarcodeInSequence \
--input-path /mnt/HDD/samanthag/soil-microbial-original/01id01NB_S1_R1_001.fastq.gz \
--output-path ./results/artifacts/multiplexed-seqs.qza
```

Denoising and ASV Generation (DADA2 and Deblur)

Filter low-quality bases, merge paired reads, remove chimeras, and cluster sequences into exact Amplicon Sequence Variants (ASVs).

Note: DADA2 and Deblur are two different denoisers that differ in output, read [here](link?) for additional information to decide which one to use.

```bash
 qiime cutadapt demux-single \
  --p-cores 16 \
  --p-minimum-length 150 \
  --i-seqs ./results/artifacts/multiplexed-seqs.qza \
  --m-barcodes-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv \
  --m-barcodes-column barcode-sequence \
  --p-error-rate 0   \
  --o-per-sample-sequences ./results/artifacts/demultiplexed-seqs2.qza \
  --o-untrimmed-sequences ./results/artifacts/untrimmed-seqs2.qza \
  --verbose 
```
# Part 2: Taxanomic Assignment

In order to classify ASVs, you can either train or use a pre-trained Naive Bayes classifier (e.g., SILVA database, greengenes) to assign taxonomic identities to your representative sequences. See additional information [here]().

```bash
qiime feature-classifier classify-sklearn \
  --i-classifier /mnt/HDD/samanthag/2024.09.backbone.v4.nb.sklearn-1.4.2.qza \
  --i-reads ./deblur/rep-seqs-deblur2.qza \
  --p-n-jobs 18 \
  --o-classification ./results/artifacts/taxonomy_greengenes_deblur.qza
```

It is also possible to generate taxanomic barplots.

```bash
qiime taxa barplot \
  --i-table ./deblur/table-deblur2.qza \
  --i-taxonomy ./results/artifacts/taxonomy_greengenes_deblur.qza \
  --m-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv \
  --o-visualization ./results/visualizations/taxa-bar-plot_unfiltered_deblur.qzv
```

# Part 3: Diversity Analyses 


A phylogenetic tree is built from your ASVs, which is a requirement for calculating faith's phylogenetic diversity and UniFrac distances.

```bash
qiime phylogeny align-to-tree-mafft-fasttree \
--i-sequences ./deblur/rep-seqs-deblur2.qza \
--o-alignment ./phylogeny/aligned-rep-seqs.qza \
--o-masked-alignment ./phylogeny/masked-aligned-rep-seqs.qza \
--o-tree ./phylogeny/unrooted-tree.qza \
--o-rooted-tree ./phylogeny/rooted-tree.qza \
--p-n-threads 14
```

Core metric results will also be generated at a specified rarefraction/sampling depth.

```bash
qiime diversity alpha-rarefaction \
--i-table ./results/artifacts/table-deblur-final.qza \
--i-phylogeny ./phylogeny/rooted-tree.qza \
--p-max-depth 7044 \
--m-metadata-file ./metadata/Nachusa_16s_all_metadata_edited2.tsv \
--o-visualization ./results/visualizations/alpha-rarefaction_max-7044.qzv

```




