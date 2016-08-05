# fusarium_ex_pea
Commands used in the analysis of Fusarium oxysporum isolates ex. pea

To update profiles in future:

cp /home/armita/generic_profiles/2016-07-28/.generic_profile ~/.profile


(change the dates for future updates)


To look at your profile:

less ~/.profile
OR
cd /home/armita
less .profile


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

Script ran- not sure where the estimated coverage and genome size comes from?


#Assembly
Assembly was performed using: SPAdes

A range of hash lengths were used and the best assembly selected for subsequent analysis


```bash
for StrainPath in $(ls -d qc_dna/paired/*/*); do
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/spades
		Strain=$(echo $StrainPath | rev | cut -f1 -d '/' | rev)
		Organism=$(echo $StrainPath | rev | cut -f2 -d '/' | rev)
		F_Read=$(ls $StrainPath/F/*.fq.gz)
		R_Read=$(ls $StrainPath/R/*.fq.gz)
		OutDir=assembly/spades/$Organism/$Strain
		echo $F_Read
		echo $R_Read
	qsub $ProgDir/submit_SPAdes.sh $F_Read $R_Read $OutDir correct 10
done
```

Assembly stats can be visualised after running Quast. 

But first you need to remove contaminants and change the headers to contigs.
 
 
#Removing contaminants- changing headers to contigs

This changes headers from Nodes to contigs in the fasta file and renames the file. 

```bash
for OutDir in $(ls -d assembly/spades/*/*/filtered_contigs); do
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/assembly_qc/remove_contaminants
		AssFiltered=$OutDir/contigs_min_500bp.fasta
		AssRenamed=$OutDir/contigs_min_500bp_renamed.fasta
		echo $AssFiltered
		printf '.\t.\t.\t.\n' > editfile.tab
		$ProgDir/remove_contaminants.py --inp $AssFiltered --out $AssRenamed --coord_file editfile.tab
		rm editfile.tab
	done
```


#Quast 

Tools used to summarise assembly statistics- to do with submission of genome to NCBI.

```bash
ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/assembly_qc/quast
		for Assembly in $(ls assembly/spades/*/*/filtered_contigs/*_500bp_renamed.fasta); do
		Strain=$(echo $Assembly | rev | cut -d '/' -f3 | rev)
		Organism=$(echo $Assembly | rev | cut -d '/' -f4 | rev)
		OutDir=assembly/spades/$Organism/$Strain/filtered_contigs
		qsub $ProgDir/sub_quast.sh $Assembly $OutDir
	done
```

For future reference these report stats can be found in e.g. less assembly/spades/F.oxysporum_fsp_pisi/PG18/filtered_contigs/report.txt

Can note down assembly stats in an Excel file or txt file. 



# Repeat masking
Repeat masking was performed and used the following programs: Repeatmasker Repeatmodeler

The best assembly was used to perform repeatmasking

```bash
ProgDir=/home/adamst/git_repos/tools/seq_tools/repeat_masking
for BestAss in $(ls assembly/spades/*/*/filtered_contigs/*_500bp_renamed.fasta); do
		echo $BestAss
		qsub $ProgDir/rep_modeling.sh $BestAss
		qsub $ProgDir/transposonPSI.sh $BestAss
	done
 ```
Takes like 24 hours



The number of bases masked by transposonPSI and Repeatmasker were summarised using the following commands:


```bash
for RepDir in $(ls -d repeat_masked/F.oxysporum_fsp_pisi/*/filtered_contigs_repmask); do
		Strain=$(echo $RepDir | rev | cut -f2 -d '/' | rev)
		Organism=$(echo $RepDir | rev | cut -f3 -d '/' | rev)  
		RepMaskGff=$(ls $RepDir/*_contigs_hardmasked.gff)
		TransPSIGff=$(ls $RepDir/*_contigs_unmasked.fa.TPSI.allHits.chains.gff3)
		printf "$Organism\t$Strain\n"
		printf "The number of bases masked by RepeatMasker:\t"
		sortBed -i $RepMaskGff | bedtools merge | awk -F'\t' 'BEGIN{SUM=0}{ SUM+=$3-$2 }END{print SUM}'
		printf "The number of bases masked by TransposonPSI:\t"
		sortBed -i $TransPSIGff | bedtools merge | awk -F'\t' 'BEGIN{SUM=0}{ SUM+=$3-$2 }END{print SUM}'
		printf "The total number of masked bases are:\t"
		cat $RepMaskGff $TransPSIGff | sortBed | bedtools merge | awk -F'\t' 'BEGIN{SUM=0}{ SUM+=$3-$2 }END{print SUM}'
		echo
	done
```

Results for ^ :

F.oxysporum_fsp_pisi	FOP1
The number of bases masked by RepeatMasker:	8091753
The number of bases masked by TransposonPSI:	2341584
The total number of masked bases are:	8415943

F.oxysporum_fsp_pisi	FOP2
The number of bases masked by RepeatMasker:	4710956
The number of bases masked by TransposonPSI:	1485557
The total number of masked bases are:	4972874

F.oxysporum_fsp_pisi	FOP5
The number of bases masked by RepeatMasker:	3841697
The number of bases masked by TransposonPSI:	1313995
The total number of masked bases are:	4180881

F.oxysporum_fsp_pisi	PG18
The number of bases masked by RepeatMasker:	5247951
The number of bases masked by TransposonPSI:	1627744
The total number of masked bases are:	5575676

F.oxysporum_fsp_pisi	PG3
The number of bases masked by RepeatMasker:	4898473
The number of bases masked by TransposonPSI:	1680264
The total number of masked bases are:	5170668


#Gene Prediction 


##Pre-gene Prediction - CEGMA

Pre-gene prediction - Quality of genome assemblies were assessed using Cegma to see how many core eukaryotic genes can be identified- looks for gene space. 

```bash
for Assembly in $(ls assembly/spades/*/*/filtered_contigs/*_500bp_renamed.fasta); do
ProgDir=/home/jenkis/git_repos/tools/gene_prediction/cegma
qsub $ProgDir/sub_cegma.sh $Assembly dna
done
```

Results can be found in: e.g/  less gene_pred/cegma/fusarium_ex_pea/PG18/
Look for the in completeness report


Output were summarised using the commands:

```bash
for File in $(ls gene_pred/cegma/*/*/*_dna_cegma.completeness_report); do
		Strain=$(echo $File | rev | cut -f2 -d '/' | rev);
		Species=$(echo $File | rev | cut -f3 -d '/' | rev);
		printf "$Species\t$Strain\n";
		cat $File | head -n18 | tail -n+4;printf "\n";
	done > gene_pred/cegma/cegma_results_dna_summary.txt
```
To view: 

less gene_pred/cegma/cegma_results_dna_summary.txt

F.oxysporum_fsp_pisi	FOP1
              #Prots  %Completeness  -  #Total  Average  %Ortho 

  Complete      238       95.97      -   282     1.18     11.76

   Group 1       61       92.42      -    71     1.16     11.48
   Group 2       54       96.43      -    62     1.15     11.11
   Group 3       58       95.08      -    75     1.29     15.52
   Group 4       65      100.00      -    74     1.14      9.23

   Partial      243       97.98      -   297     1.22     14.81

   Group 1       64       96.97      -    74     1.16     10.94
   Group 2       55       98.21      -    65     1.18     14.55
   Group 3       59       96.72      -    81     1.37     23.73
   Group 4       65      100.00      -    77     1.18     10.77

F.oxysporum_fsp_pisi	FOP2
              #Prots  %Completeness  -  #Total  Average  %Ortho 

  Complete      236       95.16      -   283     1.20     11.86

   Group 1       60       90.91      -    71     1.18     11.67
   Group 2       53       94.64      -    61     1.15      7.55
   Group 3       58       95.08      -    79     1.36     20.69
   Group 4       65      100.00      -    72     1.11      7.69

   Partial      241       97.18      -   294     1.22     13.28

   Group 1       63       95.45      -    75     1.19     11.11
   Group 2       54       96.43      -    64     1.19     11.11
   Group 3       59       96.72      -    80     1.36     20.34
   Group 4       65      100.00      -    75     1.15     10.77

F.oxysporum_fsp_pisi	FOP5
              #Prots  %Completeness  -  #Total  Average  %Ortho 

  Complete      241       97.18      -   277     1.15     10.37

   Group 1       62       93.94      -    74     1.19     11.29
   Group 2       54       96.43      -    59     1.09      7.41
   Group 3       61      100.00      -    76     1.25     16.39
   Group 4       64       98.46      -    68     1.06      6.25

   Partial      244       98.39      -   290     1.19     12.70

   Group 1       64       96.97      -    76     1.19     10.94
   Group 2       55       98.21      -    62     1.13     10.91
   Group 3       61      100.00      -    81     1.33     21.31
   Group 4       64       98.46      -    71     1.11      7.81

F.oxysporum_fsp_pisi	PG18
              #Prots  %Completeness  -  #Total  Average  %Ortho 

  Complete      242       97.58      -   295     1.22     15.70

   Group 1       62       93.94      -    75     1.21     12.90
   Group 2       54       96.43      -    63     1.17     12.96
   Group 3       61      100.00      -    79     1.30     22.95
   Group 4       65      100.00      -    78     1.20     13.85

   Partial      246       99.19      -   311     1.26     18.70

   Group 1       65       98.48      -    79     1.22     12.31
   Group 2       55       98.21      -    67     1.22     18.18
   Group 3       61      100.00      -    81     1.33     24.59
   Group 4       65      100.00      -    84     1.29     20.00

F.oxysporum_fsp_pisi	PG3
              #Prots  %Completeness  -  #Total  Average  %Ortho 

  Complete      241       97.18      -   288     1.20     12.86

   Group 1       62       93.94      -    73     1.18     12.90
   Group 2       54       96.43      -    63     1.17     14.81
   Group 3       60       98.36      -    81     1.35     18.33
   Group 4       65      100.00      -    71     1.09      6.15

   Partial      245       98.79      -   300     1.22     15.10

   Group 1       65       98.48      -    76     1.17     12.31
   Group 2       55       98.21      -    66     1.20     18.18
   Group 3       60       98.36      -    85     1.42     21.67
   Group 4       65      100.00      -    73     1.12      9.23




##Gene prediction 1 - Braker1 gene model training and prediction
(taken/adapted from the Fusarium repos)
Gene prediction was performed using Braker1.

First, RNAseq data was aligned to Fusarium genomes.

qc of RNA seq data is detailed below:


```bash
for Folder in $(ls -d ../fusarium/raw_rna/paired/F.oxysporum_fsp_cepae/*); do
FolderName=$(echo $Folder | rev | cut -f1 -d '/' | rev);
echo $FolderName;
ls $Folder/F;
ls $Folder/R;
done
```
Results here for ^ commands:

55_72hrs_rep1
sample013_1.combined.fastq.gz
sample013_2.combined.fastq.gz
55_72hrs_rep2
sample014_1.combined.fastq.gz
sample014_2.combined.fastq.gz
55_72hrs_rep3
sample015_1.combined.fastq.gz
sample015_2.combined.fastq.gz
control_72hrs_rep1
sample002_1.combined.fastq.gz
sample002_2.combined.fastq.gz
control_72hrs_rep2
sample004_1.combined.fastq.gz
sample004_2.combined.fastq.gz
control_72hrs_rep3
sample005_1.combined.fastq.gz
sample005_2.combined.fastq.gz
FO47_72hrs_rep1
sample006_1.combined.fastq.gz
sample006_2.combined.fastq.gz
FO47_72hrs_rep2
sample007_1.combined.fastq.gz
sample007_2.combined.fastq.gz
FO47_72hrs_rep3
sample012_1.combined.fastq.gz
sample012_2.combined.fastq.gz
Fus2_0hrs_prelim
1_S1_L001_R1_001.fastq.gz
1_S1_L001_R2_001.fastq.gz
Fus2_16hrs_prelim
3_S2_L001_R1_001.fastq.gz
3_S2_L001_R2_001.fastq.gz
Fus2_24hrs_prelim_rep1
4_S3_L001_R1_001.fastq.gz
4_S3_L001_R2_001.fastq.gz
Fus2_24hrs_prelim_rep2
Fus2_36hrs_prelim
36hr-root_S10_L001_R1_001.fastq.gz
36hr-root_S10_L001_R2_001.fastq.gz
Fus2_48hrs_prelim
6_S4_L001_R1_001.fastq.gz
6_S4_L001_R2_001.fastq.gz
Fus2_4hrs_prelim
4hr-root_S7_L001_R1_001.fastq.gz
4hr-root_S7_L001_R2_001.fastq.gz
Fus2_72hrs_prelim
7_S5_L001_R1_001.fastq.gz
7_S5_L001_R2_001.fastq.gz
Fus2_72hrs_rep1
sample016_1.combined.fastq.gz
sample016_2.combined.fastq.gz
Fus2_72hrs_rep2
sample018_1.combined.fastq.gz
sample018_2.combined.fastq.gz
Fus2_72hrs_rep3
sample019_1.combined.fastq.gz
sample019_2.combined.fastq.gz
Fus2_8hrs_prelim
8hr-root_S8_L001_R1_001.fastq.gz
8hr-root_S8_L001_R2_001.fastq.gz
Fus2_96hrs_prelim
8_S6_L001_R1_001.fastq.gz
8_S6_L001_R2_001.fastq.gz
Fus2_CzapekDox
6_S2_L001_R1_001_fastqc  6_S2_L001_R1_001.fastq.gz
6_S2_L001_R2_001_fastqc  6_S2_L001_R2_001.fastq.gz
Fus2_GlucosePeptone
7_S3_L001_R1_001_fastqc  7_S3_L001_R1_001.fastq.gz
7_S3_L001_R2_001_fastqc  7_S3_L001_R2_001.fastq.gz
Fus2_PDA
9_S4_L001_R1_001_fastqc  9_S4_L001_R1_001.fastq.gz
9_S4_L001_R2_001_fastqc  9_S4_L001_R2_001.fastq.gz
Fus2_PDB
4_S1_L001_R1_001_fastqc  4_S1_L001_R1_001.fastq.gz
4_S1_L001_R2_001_fastqc  4_S1_L001_R2_001.fastq.gz



... if doing this with my own RNAseq data there would be additional steps here between these 2 commands. 

Then Rnaseq data was aligned to each genome assembly:

```bash
for Assembly in $(ls repeat_masked/*/*/*/*_contigs_unmasked.fa); do
        Strain=$(echo $Assembly| rev | cut -d '/' -f3 | rev)
        Organism=$(echo $Assembly | rev | cut -d '/' -f4 | rev)
        echo "$Organism - $Strain"
        for RNADir in $(ls -d ../fusarium/qc_rna/paired/F.oxysporum_fsp_cepae/* | grep -v -e '_rep'); do
            Timepoint=$(echo $RNADir | rev | cut -f1 -d '/' | rev)
            echo "$Timepoint"
            FileF=$(ls $RNADir/F/*_trim.fq.gz)
            FileR=$(ls $RNADir/R/*_trim.fq.gz)
            OutDir=alignment/$Organism/$Strain/$Timepoint
            InsertGap='-20'
            InsertStdDev='78'
            Jobs=$(qstat | grep 'tophat' | grep 'qw' | wc -l)
            while [ $Jobs -gt 1 ]; do
                sleep 10
                printf "."
                Jobs=$(qstat | grep 'tophat' | grep 'qw' | wc -l)
            done
            printf "\n"
            ProgDir=/home/jenkis/git_repos/tools/seq_tools/RNAseq
            qsub $ProgDir/tophat_alignment.sh $Assembly $FileF $FileR $OutDir $InsertGap $InsertStdDev
        done
    done
```

ran again without: (as it stopped running when I quit terminal)

Jobs=$(qstat | grep 'tophat' | grep 'qw' | wc -l)
            while [ $Jobs -gt 1 ]; do
                sleep 10
                printf "."
                Jobs=$(qstat | grep 'tophat' | grep 'qw' | wc -l)
            done




Up until now we have been using just the repeatmasker/repeatmodeller fasta file when we have used softmasked fasta files. You can merge in transposonPSI masked sites using the following command:

```bash  
  for File in $(ls repeat_masked/*/*/filtered_contigs_repmask/*_contigs_softmasked.fa); do
        OutDir=$(dirname $File)
        TPSI=$(ls $OutDir/*_contigs_unmasked.fa.TPSI.allHits.chains.gff3)
        OutFile=$(echo $File | sed 's/_contigs_softmasked.fa/_contigs_softmasked_repeatmasker_TPSI_appended.fa/g')
        bedtools maskfasta -soft -fi $File -bed $TPSI -fo $OutFile
        echo "$OutFile"
        echo "Number of masked bases:"
        cat $OutFile | grep -v '>' | tr -d '\n' | awk '{print $0, gsub("[a-z]", ".")}' | cut -f2 -d ' '
    done
    
    
repeat_masked/F.oxysporum_fsp_pisi/FOP1/filtered_contigs_repmask/FOP1_contigs_softmasked_repeatmasker_TPSI_appended.fa
Number of masked bases:
8415943
repeat_masked/F.oxysporum_fsp_pisi/FOP2/filtered_contigs_repmask/FOP2_contigs_softmasked_repeatmasker_TPSI_appended.fa
Number of masked bases:
4972874
repeat_masked/F.oxysporum_fsp_pisi/FOP5/filtered_contigs_repmask/FOP5_contigs_softmasked_repeatmasker_TPSI_appended.fa
Number of masked bases:
4180881
repeat_masked/F.oxysporum_fsp_pisi/PG18/filtered_contigs_repmask/PG18_contigs_softmasked_repeatmasker_TPSI_appended.fa
Number of masked bases:
5575676
repeat_masked/F.oxysporum_fsp_pisi/PG3/filtered_contigs_repmask/PG3_contigs_softmasked_repeatmasker_TPSI_appended.fa
Number of masked bases:
5170668





#Braker prediction 

Before braker predictiction was performed, I double checked that I had the genemark key in my user area and copied it over from the genemark install directory:

```bash
    ls ~/.gm_key
    cp /home/armita/prog/genemark/gm_key_64 ~/.gm_key
```


DONE up to here.


```bash
for Assembly in $(ls repeat_masked/*/*/*/*_contigs_softmasked_repeatmasker_TPSI_appended.fa); do
    printf "\n"
    Strain=$(echo $Assembly| rev | cut -d '/' -f3 | rev)
    Organism=$(echo $Assembly | rev | cut -d '/' -f4 | rev)
    echo "$Organism - $Strain"
    mkdir -p alignment/$Organism/$Strain/concatenated
    samtools merge -f alignment/$Organism/$Strain/concatenated/concatenated.bam \
    alignment/$Organism/$Strain/Fus2_0hrs_prelim/accepted_hits.bam \
    alignment/$Organism/$Strain/Fus2_16hrs_prelim/accepted_hits.bam \
    alignment/$Organism/$Strain/Fus2_36hrs_prelim/accepted_hits.bam \
    alignment/$Organism/$Strain/Fus2_48hrs_prelim/accepted_hits.bam \
    alignment/$Organism/$Strain/Fus2_4hrs_prelim/accepted_hits.bam \
    alignment/$Organism/$Strain/Fus2_72hrs_prelim/accepted_hits.bam \
    alignment/$Organism/$Strain/Fus2_8hrs_prelim/accepted_hits.bam \
    alignment/$Organism/$Strain/Fus2_96hrs_prelim/accepted_hits.bam \
    alignment/$Organism/$Strain/Fus2_CzapekDox/accepted_hits.bam \
    alignment/$Organism/$Strain/Fus2_GlucosePeptone/accepted_hits.bam \
    alignment/$Organism/$Strain/Fus2_PDA/accepted_hits.bam \
    alignment/$Organism/$Strain/Fus2_PDB/accepted_hits.bam
    OutDir=gene_pred/braker/$Organism/"$Strain"_braker
    AcceptedHits=alignment/$Organism/$Strain/concatenated/concatenated.bam
    GeneModelName="$Organism"_"$Strain"_braker
    ProgDir=/home/jenkis/git_repos/tools/gene_prediction/braker1
    qsub $ProgDir/sub_braker_fungi.sh $Assembly $OutDir $AcceptedHits $GeneModelName
    done
```
      





...........................................................................................
NOTES FROM SKYPE


for the gene pred bit:

when you get here:
 for Folder in $(ls -d raw_rna/paired/F.oxysporum_fsp_cepae/*); do
     FolderName=$(echo $Folder | rev | cut -f1 -d '/' | rev);
     echo $FolderName;
     ls $Folder/F;
     ls $Folder/R;
    done


Do this:  
for Folder in $(ls -d ../fusarium/raw_rna/paired/F.oxysporum_fsp_cepae/*); do
     FolderName=$(echo $Folder | rev | cut -f1 -d '/' | rev);
     echo $FolderName;
     ls $Folder/F;
     ls $Folder/R;
    done
    
To prevent copying over too much info. It will use Andrews folder instead.

ignore all commands between the above commands and:
‘Then Rnaseq data was aligned to each genome assembly:’
(qc and estimating insert size of reads - dont need any of this)
Need command below this line ^ use initiative when running these- they will need adapting
e.g grep -w "FUS2' (filters something out) I would not use this line as im not doing FUS2

the following command (in the last script after Then Rnaseq..) has the line:
for RNADir in $(ls -d qc_rna/paired/F.oxysporum_fsp_cepae/* | grep -v -e '_rep'); do

again, you want to use the rnaseq data in my folder, so change this to:
for RNADir in $(ls -d ../fusarium/qc_rna/paired/F.oxysporum_fsp_cepae/* | grep -v -e '_rep'); do

alignig RNAseq to data

From here the commands will be similar- they submit the braker job using the bam file you have just merged together (see 46mins)




