# whole-genome-sequencing-pipeline
A comprehensive workflow for de novo assembly of whole-genome shotgun sequencing data using Velvet, followed by BLAST searches to analyze assembled contigs.

## WGS Data Analysis Workflow
### Step 1: Download and Extract Data
wget https://sra-pub-run-odp.s3.amazonaws.com/sra/SRR26624132/SRR26624132

./sratoolkit.3.0.7-mac64/bin/fastq-dump --split-files SRR26624132
fastqc SRR26624132_1.fastq SRR26624132_2.fastq

### Step 2: Install FastQC
##### For Ubuntu
sudo apt install fastqc
#### For macOS
brew install fastqc

### Step 3: Trimming with Trimmomatic
#### Download Trimmomatic
http://www.usadellab.org/cms/?page=trimmomatic
##### Assuming you have already downloaded and extracted Trimmomatic
java -jar trimmomatic-0.39.jar PE -phred33 SRR26624132_1.fastq SRR26624132_2.fastq trim1_paired.fastq trim1_unpaired.fastq trim2_paired.fastq trim2_unpaired.fastq ILLUMINACLIP:TruSeq3-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36

### Step 4: Install Velvet
brew install velvet 
sudo apt-get install velvet

####
https://github.com/dzerbino/velvet

### Step 5: Velvet Assembly
velvetg ./velvet_output -clean yes -exp_cov 21 -cov_cutoff 2.81 -min_contig_lgth 200

### Step 6: Download and Prepare Reference Genome for BLAST
brew install blast
sudo apt-get install ncbi-blast+

##### blast executables
https://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/LATEST/


#### Downaload Reference genome from NCBI GENOME
https://www.ncbi.nlm.nih.gov/genome/

#### Assuming you have downloaded and extracted refgenome.zip
makeblastdb -in ref_genome -dbtype prot(rename GCF_000183385.1_ASM18338v1_genomic.fna as ref_genome)  

### Step 7: Run BLAST
blastp -query ./velvet_output/contigs.fa -db ref_genome -out blast_result.txt -evalue 1e-04 -outfmt 6 -max_target_seqs 5 -num_threads 8

### Step 8: Extract Top Gene IDs from BLAST Results and Annotate in UniProt
cut -f 2 blast_result.txt | sort | uniq | head -n 10 > top_gene_ids.txt

### Step 9: Annotation
Add the Gene List to PANTHER for Annotation
Visit the PANTHER website (https://www.pantherdb.org/) and upload the gene list for functional annotation.

------------------------------------------------------------------------------------------------------------------>>>

### Downloading Data
- Use wget to download data from SRA.
- Use fastq-dump to convert SRA to fastq

### Quality Control
- Use FastQC to assess data quality.

### Trimming
- Use Trimmomatic to trim low-quality bases.

### Genome Assembly
- Use Velvet for de novo genome assembly.

### BLAST Analysis
- Use BLAST to compare assembled contigs with a reference genome.

### Functional Annotation
- Retrieve top gene IDs from BLAST results.
- Annotate genes using UniProt and PANTHER databases.
