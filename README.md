## WGS Data Analysis Workflow
```bash
# **Step 1: Downloading Data**
# Download and install SRA Toolkit
# [SRA Toolkit](https://github.com/ncbi/sra-tools)

# Downloading Data
# Use wget to download data from SRA.
wget https://sra-pub-run-odp.s3.amazonaws.com/sra/SRR26624132/SRR26624132

# Use fastq-dump to convert SRA to fastq
./sratoolkit.3.0.7-mac64/bin/fastq-dump --split-files SRR26624132

# **Step 2: Quality Control**
# Use FastQC to assess data quality.
fastqc SRR26624132_1.fastq SRR26624132_2.fastq

# **Step 3: Trimming**
# Use Trimmomatic to trim low-quality bases.
# [Trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic)

java -jar trimmomatic-0.39.jar PE -phred33 SRR26624132_1.fastq SRR26624132_2.fastq trim1_paired.fastq trim1_unpaired.fastq trim2_paired.fastq trim2_unpaired.fastq ILLUMINACLIP:TruSeq3-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36

# **Step 4: Genome Assembly**
# Use Velvet for de novo genome assembly.
# Install Velvet (For macOS)
brew install velvet
# Install Velvet (For Ubuntu)
sudo apt-get install velvet
# [velvet](https://github.com/dzerbino/velvet)

velvetg ./velvet_output -clean yes -exp_cov 21 -cov_cutoff 2.81 -min_contig_lgth 200

# **Step 5: BLAST Analysis**
# Use BLAST to compare assembled contigs with a reference genome.
# Install BLAST
brew install blast
sudo apt-get install ncbi-blast+

# [blast executables](https://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/LATEST/)
blastp -query ./velvet_output/contigs.fa -db ref_genome -out blast_result.txt -evalue 1e-04 -outfmt 6 -max_target_seqs 5 -num_threads 8

# **Step 6: Functional Annotation**
# Retrieve top gene IDs from BLAST results.
cut -f 2 blast_result.txt | sort | uniq | head -n 10 > top_gene_ids.txt

# Annotate genes using UniProt and PANTHER databases.
# Add the Gene List to PANTHER for Annotation
# Visit the [PANTHER](https://www.pantherdb.org/) and upload the gene list for functional annotation.
```