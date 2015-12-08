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

This process was repeated for RNAseq data

```bash
RawDatDir=/home/groups/harrisonlab/raw_data/raw_seq/fusarium/rna_seq
ProjectDir=/home/groups/harrisonlab/project_files/fusarium_ex_pea
	mkdir -p $ProjectDir/raw_rna/paired/F.oxysporum_fsp_cepae/Fus2_PDB/F
	mkdir -p $ProjectDir/raw_rna/paired/F.oxysporum_fsp_cepae/Fus2_PDB/R
	mkdir -p $ProjectDir/raw_rna/paired/F.oxysporum_fsp_cepae/Fus2_CzapekDox/F
	mkdir -p $ProjectDir/raw_rna/paired/F.oxysporum_fsp_cepae/Fus2_CzapekDox/R
	mkdir -p $ProjectDir/raw_rna/paired/F.oxysporum_fsp_cepae/Fus2_GlucosePeptone/F
	mkdir -p $ProjectDir/raw_rna/paired/F.oxysporum_fsp_cepae/Fus2_GlucosePeptone/R
	mkdir -p $ProjectDir/raw_rna/paired/F.oxysporum_fsp_cepae/Fus2_PDA/F
	mkdir -p $ProjectDir/raw_rna/paired/F.oxysporum_fsp_cepae/Fus2_PDA/R

cp $RawDatDir/4_S1_L001_R1_001.fastq.gz $ProjectDir/raw_rna/paired/F.oxysporum_fsp_cepae/Fus2_PDB/F/.
    cp $RawDatDir/4_S1_L001_R2_001.fastq.gz $ProjectDir/raw_rna/paired/F.oxysporum_fsp_cepae/Fus2_PDB/R/.
    cp $RawDatDir/6_S2_L001_R1_001.fastq.gz $ProjectDir/raw_rna/paired/F.oxysporum_fsp_cepae/Fus2_CzapekDox/F/.
    cp $RawDatDir/6_S2_L001_R2_001.fastq.gz $ProjectDir/raw_rna/paired/F.oxysporum_fsp_cepae/Fus2_CzapekDox/R/.
    cp $RawDatDir/7_S3_L001_R1_001.fastq.gz $ProjectDir/raw_rna/paired/F.oxysporum_fsp_cepae/Fus2_GlucosePeptone/F/.
    cp $RawDatDir/7_S3_L001_R2_001.fastq.gz $ProjectDir/raw_rna/paired/F.oxysporum_fsp_cepae/Fus2_GlucosePeptone/R/.
    cp $RawDatDir/9_S4_L001_R1_001.fastq.gz $ProjectDir/raw_rna/paired/F.oxysporum_fsp_cepae/Fus2_PDA/F/.
    cp $RawDatDir/9_S4_L001_R2_001.fastq.gz $ProjectDir/raw_rna/paired/F.oxysporum_fsp_cepae/Fus2_PDA/R/.



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

Can visualise this by navigating to the file in finder (after cluster mount on a new tab in terminal) and opening the file in the browser.

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

Data quality was visualised once again following trimming: (with the same fast qc programme as before)

```bash
for RawData in qc_dna/paired/*/*/*/*.fastq*; do
        ProgDir=/home/jenkis/git_repos/tools/seq_tools/dna_qc
        echo $RawData;
        qsub $ProgDir/run_fastqc.sh $RawData
    done
```


kmer counting was performed using kmc.
This allowed estimation of sequencing depth and total genome size:

```bash
for TrimPath in qc_dna/paired/*/*; do
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/dna_qc
		TrimF=$(ls $TrimPath/F/*.fq.gz)
		TrimR=$(ls $TrimPath/F/*.fq.gz)
		echo $TrimF
		echo $TrimR
		qsub $ProgDir/kmc_kmer_counting.sh $TrimF $TrimR
    done
```

** Estimated Genome Size is:

** Esimated Coverage is:

#Assembly
Assembly was performed using: Spades

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

Assemblies were summarised to allow the best assembly to be determined by eye.

** Assembly stats are:
  * Assembly size:
  * N50:
  * N80:
  * N20:
  * Longest contig:
  **

For future reference these report stats can be found in less assembly/spades/F.oxysporum_fsp_pisi/PG18/filtered_contigs/report.txt


#Removing contaminants- changing headers to contigs
Need to be in Project directory for this.. so fusarium_ex_pea

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
ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/assembly_qc/quast
	for Assembly in $(ls assembly/spades/*/*/filtered_contigs/*_500bp_renamed.fasta); do
	Strain=$(echo $Assembly | rev | cut -d '/' -f3 | rev)
	Organism=$(echo $Assembly | rev | cut -d '/' -f4 | rev)
	OutDir=assembly/spades/$Organism/$Strain/filtered_contigs
	qsub $ProgDir/sub_quast.sh $Assembly $OutDir
done

Run for PG3 with e.g:
for Assembly in $(ls assembly/spades/*/PG3/filtered_contigs/*_500bp_renamed.fasta); do



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

** % bases masked by repeatmasker:

** % bases masked by transposon psi: **


# Gene Prediction
Gene prediction followed two steps:
Pre-gene prediction - Quality of genome assemblies were assessed using Cegma to see how many core eukaryotic genes can be identified.
Gene models were used to predict genes in the Neonectria genome. This used results from CEGMA as hints for gene models.

## Pre-gene prediction
Quality of genome assemblies was assessed by looking for the gene space in the assemblies.

```bash
for Assembly in $(ls assembly/spades/*/*/filtered_contigs/*_500bp_renamed.fasta); do
	ProgDir=/home/jenkis/git_repos/tools/gene_prediction/cegma
	qsub $ProgDir/sub_cegma.sh $Assembly dna
    done
```
Results can be found in less gene_pred/cegma/fusarium_ex_pea/PG18/
in completeness report


** Number of cegma genes present and complete:
** Number of cegma genes present and partial:

##Gene prediction

Gene prediction was performed for the neonectria genome.
CEGMA genes were used as Hints for the location of CDS.


###Gene prediction 1- Gene models predicted using Augustus

```bash
for Assembly in $(ls assembly/spades/*/*/filtered_contigs/*_500bp_renamed.fasta); do
	Strain=$(echo $Assembly | rev | cut -d '/' -f3 | rev)
	Organism=$(echo $Assembly | rev | cut -d '/' -f4 | rev)
	OutDir=gene_pred/augustus/$Organism/$Strain
	ProgDir=/home/jenkis/git_repos/tools/gene_prediction/augustus
	GeneModel=fusarium
	qsub $ProgDir/submit_augustus.sh $GeneModel $Assembly false $OutDir
done
```

** Number of genes predicted:


### Gene prediction 2- ORFs predicted following 6 frame translation 

Open reading frame predictions were made using the ORF_finder.sh script as part of the path_pipe.sh pipeline. 
This pipeline also identifies open reading frames containing Signal peptide sequences and RxLRs. 
This pipeline was run with the following commands:

```bash
	ProgDir=/home/jenkis/git_repos/tools/gene_prediction/ORF_finder
	for Assembly in $(ls assembly/spades/*/*/filtered_contigs/*_500bp_renamed.fasta); do
	qsub $ProgDir/run_ORF_finder.sh $Assembly
done
```

The Gff files from the the ORF finder are not in true Gff3 format. This script converts gff files to true gff3 files that other programmes recognise. 
These were corrected using the following commands:
Cant run this until the ORF prediction above is finished. 

```bash
for ORF_Gff in $(ls gene_pred/ORF_finder/*/*/*_ORF.gff | grep -v '_atg_'); do
    Strain=$(echo $ORF_Gff | rev | cut -f2 -d '/' | rev)
    Organism=$(echo $ORF_Gff | rev | cut -f3 -d '/' | rev)
    ProgDir=~/git_repos/tools/seq_tools/feature_annotation
    ORF_Gff_mod=gene_pred/ORF_finder/$Organism/$Strain/"$Strain"_ORF_corrected.gff3
    $ProgDir/gff_corrector.pl $ORF_Gff > $ORF_Gff_mod
  done
```

#Functional annotation

Interproscan was used to give gene models functional annotations. Need to use screen function.
open screen = screen -a
exit but keeps it running= Ctrl A then D
resume= screen -r
terminate in screen= Ctrl D (use with caution!)

Interproscan was used to give gene models functional annotations 
```bash
for Genes in $(ls gene_pred/augustus/*/*/*_EMR_singlestrand_aug_out.aa); do
	ProgDir=/home/jenkis/git_repos/tools/seq_tools/feature_annotation/interproscan
	$ProgDir/sub_interproscan.sh $Genes
done
```
Can test the command first using something like 
```bash
for Genes in $(ls gene_pred/augustus/*/*/*_EMR_singlestrand_aug_out.aa); do 
echo $Genes
cat $Genes |grep '>' | wc -l
done
```







```bash

```

###Genomic analysis
The first analysis was based upon BLAST searches for genes known to be involved in toxin production


##Genes with homology to PHIbase
Predicted gene models were searched against the PHIbase database using tBLASTx.

```bash
	ProgDir=/home/jenkis/git_repos/tools/pathogen/blast
	Query=../../phibase/v3.8/PHI_accessions.fa
	for Assembly in $(ls assembly/spades/*/*/filtered_contigs/*_500bp_renamed.fasta); do
	qsub $ProgDir/blast_pipe.sh $Query protein $Assembly
```

Top BLAST hits were used to annotate gene models.

```bash

```


#Signal peptide prediction

## RxLR genes


Proteins that were predicted to contain signal peptides were identified using
the following commands:

```bash
for Proteome in $(ls gene_pred/augustus/*/*/*_EMR_singlestrand_aug_out.aa); do
SplitfileDir=/home/jenkis/git_repos/tools/seq_tools/feature_annotation/signal_peptides
ProgDir=/home/jenkis/git_repos/tools/seq_tools/feature_annotation/signal_peptides
Strain=$(echo $Proteome | rev | cut -f2 -d '/' | rev)
Organism=$(echo $Proteome | rev | cut -f3 -d '/' | rev)
SplitDir=gene_pred/augustus_split/$Organism/$Strain
mkdir -p $SplitDir
BaseName="$Organism""_$Strain"_augustus_preds
$SplitfileDir/splitfile_500.py --inp_fasta $Proteome --out_dir $SplitDir --out_base $BaseName
for File in $(ls $SplitDir/*_augustus_preds_*); do
Jobs=$(qstat | grep 'pred_sigP' | grep 'qw' | wc -l)
while [ $Jobs -ge 1 ]; do
sleep 10
printf "."
Jobs=$(qstat | grep 'pred_sigP' | grep 'qw' | wc -l)
done
printf "\n"
echo $File
#qsub $ProgDir/pred_sigP.sh $File
qsub $ProgDir/pred_sigP.sh $File signalp-4.1
done
done
```

The batch files of predicted secreted proteins needed to be combined into a
single file for each strain. This was done with the following commands:
```bash
for SplitDir in $(ls -d gene_pred/augustus_split/*/*); do
Strain=$(echo $SplitDir | cut -d '/' -f4)
Organism=$(echo $SplitDir | cut -d '/' -f3)
InStringAA=''
InStringNeg=''
InStringTab=''
InStringTxt=''
SigpDir=augustus_signalp-4.1
for GRP in $(ls -l $SplitDir/*_augustus_preds_*.fa | rev | cut -d '_' -f1 | rev | sort -n); do  
InStringAA="$InStringAA gene_pred/$SigpDir/$Organism/$Strain/split/"$Organism"_"$Strain"_augustus_preds_$GRP""_sp.aa";  
InStringNeg="$InStringNeg gene_pred/$SigpDir/$Organism/$Strain/split/"$Organism"_"$Strain"_augustus_preds_$GRP""_sp_neg.aa";  
InStringTab="$InStringTab gene_pred/$SigpDir/$Organism/$Strain/split/"$Organism"_"$Strain"_augustus_preds_$GRP""_sp.tab";
InStringTxt="$InStringTxt gene_pred/$SigpDir/$Organism/$Strain/split/"$Organism"_"$Strain"_augustus_preds_$GRP""_sp.txt";  
done
cat $InStringAA > gene_pred/$SigpDir/$Organism/$Strain/"$Strain"_aug_sp.aa
cat $InStringNeg > gene_pred/$SigpDir/$Organism/$Strain/"$Strain"_aug_neg_sp.aa
tail -n +2 -q $InStringTab > gene_pred/$SigpDir/$Organism/$Strain/"$Strain"_aug_sp.tab
cat $InStringTxt > gene_pred/$SigpDir/$Organism/$Strain/"$Strain"_aug_sp.txt
done

```







** Blast results of note: **
  * 'Result A'
