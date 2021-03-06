# Winter 2019 Mid-Atlantic Microbiome Meetup
# Taxonomic Identification Workshop

## Logging onto the server
There is username and password at the back of your name tag.
```bash
ssh USERNAME@openclass.umiacs.umd.edu
```
Enter password. Once you are logged in, you should be able to see m3taxworkshop folder in your directory. <br />
In order to load all of the pre-installed software, please run:

```bash
# Load all software modules
source ~/m3taxworkshop/test_user_profile.sh
```
Now, download this github repository 
```bash
git clone https://github.com/shahnidhi/m3-taxonomy-workshop.git
```
We will run all our exercises in an interactive job. To start an interactive job run the following command
```bash
srun --pty --partition class --account=class --qos class --mem=8g --time=04:00:00 bash
```
or just type ```int```, we have created an alias.
## Datasets 

### HMP Stool Samples 

We selected ten stool samples sequenced by the Human Microbiome Project using both V1V3 marker gene methods and whole metagenomic shotgun sequencing. <br />
Location - ```~/m3taxworkshop/data/1-datasets/hmp/```

### Halite (Salt Rock) from the Atacama Desert, Chile 

This whole metagenomic shotgun sequencing study characterized the microbial communities in halite modules in the Atacama Desert, and in particular their response to unusual rainfall in August 2015. Samples are from four time points (pre-rain in Sep 2014 and June 2015 and post rain in Feb 2016 and Feb 2017) with five replicates for each time point. <br />
Special thanks to Gherman Uritskiy and Jocelyne DiRuggiero for providing the data! <br />
The preprint including this data can be found at https://www.biorxiv.org/content/early/2018/10/13/442525 <br />
Location - ```~/m3taxworkshop/data/1-datasets/atacama_halite_timeline/```

## Machine Learning Approaches 
### Running RDP classifier
For this part, we are going to use stool dataset and greengenes 16S rRNA gene database. 
```bash
cd ~/m3-taxonomy-workshop/run_rdp
assign_taxonomy.py -h

assign_taxonomy.py -m rdp -o rdp_taxonomy_stool_v1v3 -i ~/m3taxworkshop/data/1-datasets/hmp/stool_sample_subset_rep_set_filtered_final.fna
```
## Fast Metagenomic Profiling Methods
### Running Kraken
```bash
cd ~/m3-taxonomy-workshop/run_kraken

```
## Database searching - sequence alignment based approaches
### Running BLAST
```bash
cd ~/m3-taxonomy-workshop/run_blast/blast_toy_example
ls
makeblastdb -in database.fasta -out database.fasta -dbtype nucl
blastn -h
blastn -query query.fasta  -db database.fasta  -outfmt " 6 qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore qseq sseq qlen " -out blast.out -num_threads 4
```
### Running outlier detection pipeline 
We need python3 environment and python packages such as scipy, networkx, python-louvain. We have created a virtual environment with all these installed, you just have to source it
```bash
export PYTHONPATH=''
source ~/m3taxworkshop/software/outlier_env/bin/activate
```
We are going to work on HMP stool dataset here, and use SILVA v.128 database in this example. <br />
- Query sequences: ```~/m3taxworkshop/data/1-datasets/hmp/stool_sample_subset_rep_set_filtered_final.fna```
- Database sequences: ```~/m3taxworkshop/databases/silva/~/m3taxworkshop/databases/silva/SILVA_132_SSURef_Nr99_tax_silva_subset.fasta```
- Database taxonomy: ```~/m3taxworkshop/databases/silva/silva_nr_99_subset_taxonomy.tsv```

Staging BLAST output for hmp stool dataset
```bash
cd ~/m3-taxonomy-workshop/run_blast/hmp_example
cp ~/m3taxworkshop/previous_run/blast/stool_blast.out.gz .
gunzip stool_blast.out.gz 
```
Check outlier detection pipeline options and run on stool sample dataset
```bash
run_pipeline.py -h
run_pipeline.py -q ~/m3taxworkshop/data/1-datasets/hmp/stool_sample_subset_rep_set_filtered_final.fna -b stool_blast.out -t ~/m3taxworkshop/databases/silva/silva_nr_99_subset_taxonomy.tsv -o stool_blast_outlier
```
Check specifically these files in the output folder
1. results_outliers.txt - a tsv with reads and relevant DB sequences
2. results_partition_map_FINAL.txt - partition number to DB sequence mapping
3. results_read_to_partition_assignment.txt - partition assignment for reads
4. consensus_taxonomy_based_on_outliers.txt - LCA of outliers

## Phylogenetic methods
### Running TIPP pipeline
If you are still in python3 environment, run ```deactivate```
Because TIPP needs python2, switch to python2 environment by running 
```bash
source ~/m3taxworkshop/test_user_profile.sh
source ~/m3taxworkshop/software/tipp_env/bin/activate
```
Inputs
- Set of query sequences, i.e., fragments/reads of unknown origin
- Reference alignment and tree or taxonomy
```bash
cd ~/m3-taxonomy-workshop/run_tipp/
# Run on small example
and give output of hmp sample
```
Change the confidence to 0.50 and see the result
