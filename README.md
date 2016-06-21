# fusarium_ex_pea
Commands used in the analysis of Fusarium oxysporum isolates ex. pea


Fusarium oxysporum f.sp pisi
====================


Commands used during analysis of the F.oxysporum f.sp pisi genome. Note - all this work was performed in the directory:
```bash
mkdir -p /home/groups/harrisonlab/project_files/fusarium_ex_pea
cd /home/groups/harrisonlab/project_files/fusarium_ex_pea
```
This makes a new directory in project files called fusarium_ex_pea and then changes directory to this new directory


The following is a summary of the work presented in this Readme:
Data organisation:
  * Preparing data  
Draft Genome assembly
  * Data qc
  * Genome assembly
  * Repeatmasking
  * Gene prediction
  * Functional annotation
Genome analysis
  * Homology between predicted genes & published effectors


#Data organisation

Data was copied from the raw_data repository to a local directory for assembly
and annotation.


```bash
RawDatDir=/home/groups/harrisonlab/raw_data/raw_seq/fusarium/HAPI_seq_3/
ProjectDir=/home/groups/harrisonlab/project_files/fusarium_ex_pea
mkdir -p $ProjectDir/raw_dna/paired/F.oxysporum_fsp_pisi/PG3/F
mkdir -p $ProjectDir/raw_dna/paired/F.oxysporum_fsp_pisi/PG3/R
mkdir -p $ProjectDir/raw_dna/paired/F.oxysporum_fsp_pisi/PG18/F
mkdir -p $ProjectDir/raw_dna/paired/F.oxysporum_fsp_pisi/PG18/R
cp $RawDatDir/FoxysporumPG3_S3_L001_R1_001.fastq.gz $ProjectDir/raw_dna/paired/F.oxysporum_fsp_pisi/PG3/F/.
cp $RawDatDir/FoxysporumPG3_S3_L001_R2_001.fastq.gz $ProjectDir/raw_dna/paired/F.oxysporum_fsp_pisi/PG3/R/.
cp $RawDatDir/FoxysporumPG18_S4_L001_R1_001.fastq.gz $ProjectDir/raw_dna/paired/F.oxysporum_fsp_pisi/PG18/F/.
cp $RawDatDir/FoxysporumPG18_S4_L001_R2_001.fastq.gz $ProjectDir/raw_dna/paired/F.oxysporum_fsp_pisi/PG18/R/.
```

I also made new directories for FOP1, FOP2 and FOP5 and copied over the data (by being in the folder I wanted the data to be in, and changing the FOP number and file for each)
```bash
scp /home/groups/harrisonlab/project_files/fusarium/raw_dna/paired/F.oxysporum_fsp_pisi/FOP5/F/FOP5_S2_L001_R1_001.fastq.gz .
```


#Data qc

programs: fastqc fastq-mcf kmc

Data quality was visualised using fastqc:

```bash
for RawData in $(ls raw_dna/paired/*/*/*/*.fastq.gz); do
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/dna_qc
		echo $RawData;
		qsub $ProgDir/run_fastqc.sh $RawData
	done
```
The output for this is into the same location that the raw file came from, so e.g. raw_dna/paired/F.oxysporum_fsp_pisi/PG3/R. A new directory should appear in F/R directories for each isolate. 

Can visualise this by navigating to the file in finder (after cluster mount on a new tab in terminal) and opening the html file in the browser.


##Data Trimming

Trimming was performed on data to trim adapters from sequences and remove poor quality data.
This was done with fastq-mcf


```bash
for StrainPath in $(ls -d raw_dna/paired/*/*); do
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/rna_qc
		IlluminaAdapters=/home/jenkis/git_repos/tools/seq_tools/ncbi_adapters.fa
		ReadsF=$(ls $StrainPath/F/*.fastq*)
		ReadsR=$(ls $StrainPath/R/*.fastq*)
		echo $ReadsF
		echo $ReadsR
		qsub $ProgDir/rna_qc_fastq-mcf.sh $ReadsF $ReadsR $IlluminaAdapters DNA
    done
```

This trims off the adapters from the fastq.gz file in raw_dna/paired/FOP1/F etc directories


Fastqc was performed again to visualise the quality of the data following trimming (using the same script as before):

Data quality was visualised once again following trimming: (with the same fast qc programme as before)

```bash
for RawData in qc_dna/paired/*/*/*/*.fq.gz; do
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/dna_qc
		echo $RawData;
		qsub $ProgDir/run_fastqc.sh $RawData
    done
```

##kmer counting was performed using kmc.
This allowed estimation of sequencing depth and total genome size:

```bash
for TrimPath in qc_dna/paired/*/*; do
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/dna_qc
		TrimF=$(ls $TrimPath/F/*.fq.gz)
		TrimR=$(ls $TrimPath/R/*.fq.gz)
		echo $TrimF
		echo $TrimR
		qsub $ProgDir/kmc_kmer_counting.sh $TrimF $TrimR
    done
```



