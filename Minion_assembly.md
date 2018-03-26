# Fusarium oxysporum f.sp. pisi (Minion and Miseq assemblies)
## Commands used for the analysis of F.oxysporum f.sp pisi sequences FOP1 EMR, F81 and R2

Based on Fusarium venenatum git repos

# Copying data Minion and MiSeq

## Minion data

Data was basecalled using Albacore 2.02 on the minion server:



Data was basecalled again using Albacore 2.02 on the minion server:

```bash
Organism="F.oxysporum_fsp_pisi"
for Strain in "FOP1-EMR" "F81" "R2"; do
  echo $Strain
  OutDir=/home/groups/harrisonlab/project_files/fusarium_ex_pea/raw_dna/minion/$Organism/$Strain
  mkdir -p $OutDir
done

ssh nanopore@nanopore
screen -a
# Oxford nanopore 2017-07-11
Organism="F.oxysporum_fsp_pisi"
Strain="FOP1-EMR"
Date="2017-07-11"
FlowCell="FLO-MIN106"
Kit="SQK-LSK108"
RawDatDir=/data/seq_data/minion/2017/20170711_1532_FOP1-EMR/fast5/pass
OutDir=/home/groups/harrisonlab/project_files/fusarium_ex_pea/raw_dna/minion/$Organism/$Strain

WorkDir=/mnt/local_data/nanopore/FoP_warwick/$Date
mkdir -p $WorkDir
cd $WorkDir
~/.local/bin/read_fast5_basecaller.py \
  --flowcell $FlowCell \
  --kit $Kit \
  --input $RawDatDir \
  --recursive \
  --worker_threads 12 \
  --save_path "$Organism"_"$Strain"_"$Date" \
  --output_format fastq,fast5 \
  --reads_per_fastq_batch 4000
  cat "$Organism"_"$Strain"_"$Date"/workspace/pass/*.fastq | gzip -cf > "$Organism"_"$Strain"_"$Date"_albacore_v2.02.fastq.gz
  tar -cz -f "$Organism"_"$Strain"_"$Date".tar.gz "$Organism"_"$Strain"_"$Date"
  # mkdir -p $OutDir
  scp "$Organism"_"$Strain"_"$Date"_albacore_v2.02.fastq.gz armita@192.168.1.200:$OutDir/.
  scp "$Organism"_"$Strain"_"$Date".tar.gz armita@192.168.1.200:$OutDir/.

  #
  mkdir -p /data/scratch/nanopore_tmp_data/Fop/F81
  tar -zxvf /data/seq_data/minion/2017/2017-07-03_FoxysporumF81/20170525_1310_Fus_oxysporum_F81_albacore1.2.4_fast5s.tar.gz -C /data/scratch/nanopore_tmp_data/Fop/F81
  mkdir -p /data/scratch/nanopore_tmp_data/Fop/R2
  tar -zxvf /data/seq_data/minion/2017/2017-07-03_FoxysporumR2/2017-07-03_FoxysporumR2_run2_albacore_1.2.4.fast5s.tar.gz -C /data/scratch/nanopore_tmp_data/Fop/R2


  # Oxford nanopore FoP F81
  Organism="F.oxysporum_fsp_pisi"
  Strain="F81"
  Date="2017-05-17"
  FlowCell="FLO-MIN106"
  Kit="SQK-LSK108"
  RawDatDir=/data/scratch/nanopore_tmp_data/Fop/F81/albacore_output_1.2.4/workspace
  OutDir=/home/groups/harrisonlab/project_files/fusarium_ex_pea/raw_dna/minion/$Organism/$Strain

  WorkDir=/mnt/local_data/nanopore/FoP_warwick/$Date
  mkdir -p $WorkDir
  cd $WorkDir
  ~/.local/bin/read_fast5_basecaller.py \
    --flowcell $FlowCell \
    --kit $Kit \
    --input $RawDatDir \
    --recursive \
    --worker_threads 12 \
    --save_path "$Organism"_"$Strain"_"$Date" \
    --output_format fastq,fast5 \
    --reads_per_fastq_batch 4000
    cat "$Organism"_"$Strain"_"$Date"/workspace/pass/*.fastq | gzip -cf > "$Organism"_"$Strain"_"$Date"_albacore_v2.02.fastq.gz
    tar -cz -f "$Organism"_"$Strain"_"$Date".tar.gz "$Organism"_"$Strain"_"$Date"
    # mkdir -p $OutDir
    scp "$Organism"_"$Strain"_"$Date"_albacore_v2.02.fastq.gz armita@192.168.1.200:$OutDir/.
    scp "$Organism"_"$Strain"_"$Date".tar.gz armita@192.168.1.200:$OutDir/.

    # Oxford nanopore FoP R2
    Organism="F.oxysporum_fsp_pisi"
    Strain="R2"
    Date="2017-05-17"
    FlowCell="FLO-MIN107"
    Kit="SQK-LSK108"
    RawDatDir=/data/scratch/nanopore_tmp_data/Fop/R2
    OutDir=/home/groups/harrisonlab/project_files/fusarium_ex_pea/raw_dna/minion/$Organism/$Strain

    WorkDir=/mnt/local_data/nanopore/FoP_warwick/$Date
    mkdir -p $WorkDir
    cd $WorkDir
    ~/.local/bin/read_fast5_basecaller.py \
      --flowcell $FlowCell \
      --kit $Kit \
      --input $RawDatDir \
      --recursive \
      --worker_threads 12 \
      --save_path "$Organism"_"$Strain"_"$Date" \
      --output_format fastq,fast5 \
      --reads_per_fastq_batch 4000
      cat "$Organism"_"$Strain"_"$Date"/workspace/pass/*.fastq | gzip -cf > "$Organism"_"$Strain"_"$Date"_albacore_v2.02.fastq.gz
      tar -cz -f "$Organism"_"$Strain"_"$Date".tar.gz "$Organism"_"$Strain"_"$Date"
      # mkdir -p $OutDir
      scp "$Organism"_"$Strain"_"$Date"_albacore_v2.02.fastq.gz armita@192.168.1.200:$OutDir/.
      scp "$Organism"_"$Strain"_"$Date".tar.gz armita@192.168.1.200:$OutDir/.
```

## MiSeq data

MiSeq data was copied across and new folders made:

```bash
RawDatDir=/data/seq_data/miseq/2017/RAW/171016_M04465_0051_000000000-B43DD/Data/Intensities/BaseCalls
ProjectDir=/home/groups/harrisonlab/project_files/fusarium_ex_pea
OutDir=$ProjectDir/raw_dna/paired/F.oxysporum_fsp_pisi/F81
mkdir -p $OutDir/F
mkdir -p $OutDir/R
cd $OutDir/F
cp -s $RawDatDir/F81_S3_L001_R1_001.fastq.gz .
cd $OutDir/R
cp -s $RawDatDir/F81_S3_L001_R2_001.fastq.gz .

RawDatDir=/data/seq_data/miseq/2017/RAW/171016_M04465_0051_000000000-B43DD/Data/Intensities/BaseCalls
ProjectDir=/home/groups/harrisonlab/project_files/fusarium_ex_pea
OutDir=$ProjectDir/raw_dna/paired/F.oxysporum_fsp_pisi/FOP1-EMR
mkdir -p $OutDir/F
mkdir -p $OutDir/R
cd $OutDir/F
cp -s $RawDatDir/FOP1-EMR_S2_L001_R1_001.fastq.gz .
cd $OutDir/R
cp -s $RawDatDir/FOP1-EMR_S2_L001_R2_001.fastq.gz .
OutDir=$ProjectDir/raw_dna/paired/F.oxysporum_fsp_pisi/R2
mkdir -p $OutDir/F
mkdir -p $OutDir/R
cd $OutDir/F
cp -s $RawDatDir/R2_S1_L001_R1_001.fastq.gz .
cd $OutDir/R
cp -s $RawDatDir/R2_S1_L001_R2_001.fastq.gz .
```


## Run fastqc

mkdir fastqc
cd fastqc

### Run fastqc on an individual file for minion seqs:

```bash
ProgDir=/home/jenkis/git_repos/tools/seq_tools/dna_qc/

qsub ${ProgDir}run_fastqc.sh ../raw_dna/minion/F.oxysporum_fsp_pisi/FOP1-EMR/F.oxysporum_fsp_pisi_FOP1-EMR_2017-07-11_albacore_v2.02.fastq.gz

qsub ${ProgDir}run_fastqc.sh ../raw_dna/minion/F.oxysporum_fsp_pisi/F81/F.oxysporum_fsp_pisi_F81_2017-05-17_albacore_v2.02.fastq.gz

qsub ${ProgDir}run_fastqc.sh ../raw_dna/minion/F.oxysporum_fsp_pisi/R2/F.oxysporum_fsp_pisi_R2_2017-05-17_albacore_v2.02.fastq.gz
```
**NOTE The fastqc step doesn't work properly for long read data so the jobs were cancelled for these


### Illumina:

```bash
qsub ${ProgDir}run_fastqc.sh ../raw_dna/paired/F.oxysporum_fsp_pisi/FOP1-EMR/F/FOP1-EMR_S2_L001_R1_001.fastq.gz

qsub ${ProgDir}run_fastqc.sh ../raw_dna/paired/F.oxysporum_fsp_pisi/FOP1-EMR/F/FOP1-EMR_S2_L001_R2_001.fastq.gz

qsub ${ProgDir}run_fastqc.sh ../raw_dna/paired/F.oxysporum_fsp_pisi/F81/F/F81_S3_L001_R1_001.fastq.gz

qsub ${ProgDir}run_fastqc.sh ../raw_dna/paired/F.oxysporum_fsp_pisi/F81/F/F81_S3_L001_R2_001.fastq.gz

qsub ${ProgDir}run_fastqc.sh ../raw_dna/paired/F.oxysporum_fsp_pisi/R2/F/R2_S1_L001_R1_001.fastq.gz

qsub ${ProgDir}run_fastqc.sh ../raw_dna/paired/F.oxysporum_fsp_pisi/R2/F/R2_S1_L001_R2_001.fastq.gz
```

 


## Identifying read depth

```bash
	for Reads in $(ls raw_dna/minion/*/*/*.fastq.gz); do
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/dna_qc
		qsub $ProgDir/sub_count_nuc.sh 52 $Reads
		done
	for Reads in $(ls raw_dna/paired/*/*/*/*.fastq.gz); do
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/dna_qc
		qsub $ProgDir/sub_count_nuc.sh 52 $Reads
		done
```

The results for this are in fusarium_ex_pea and are *_cov.txt files. To look at all the results from coverage estimation script you can search for the last line and display that:

```bash
cd /home/groups/harrisonlab/project_files/fusarium_ex_pea
grep "equates" *_cov.txt
```
illumina: 
F81_S3_L001_R1_001_cov.txt: This equates to an estimated genome coverage of 29.67 .
F81_S3_L001_R2_001_cov.txt: This equates to an estimated genome coverage of 29.67 .
FOP1-EMR_S2_L001_R1_001_cov.txt: This equates to an estimated genome coverage of 41.33 .
FOP1-EMR_S2_L001_R2_001_cov.txt: This equates to an estimated genome coverage of 41.35 .
R2_S1_L001_R1_001_cov.txt: This equates to an estimated genome coverage of 34.04 .
R2_S1_L001_R2_001_cov.txt: This equates to an estimated genome coverage of 34.04 .

Minion:
F.oxysporum_fsp_pisi_F81_2017-05-17_albacore_v2.02_cov.txt: This equates to an estimated genome coverage of 44.07 .
F.oxysporum_fsp_pisi_FOP1-EMR_2017-07-11_albacore_v2.02_cov.txt: This equates to an estimated genome coverage of 62.54 .
F.oxysporum_fsp_pisi_R2_2017-05-17_albacore_v2.02_cov.txt: This equates to an estimated genome coverage of 47.35 .




## Splitting reads and trimming adapters using porechop (minion)

```bash
	for RawReads in $(ls raw_dna/minion/*/*/*.fastq.gz); do
		Strain=$(echo $RawReads | rev | cut -f2 -d '/' | rev)
		Organism=$(echo $RawReads | rev | cut -f3 -d '/' | rev)
		echo "$Organism - $Strain"
		OutDir=qc_dna/minion/$Organism/$Strain
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/dna_qc
		qsub $ProgDir/sub_porechop.sh $RawReads $OutDir
	done
```


## Trimming was aslo performed on all illumina seqs using fastq-mcf

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



## Read coverage was estimated from the trimmed datasets:

```bash
	GenomeSz=52
	for Reads in $(ls qc_dna/minion/*/*/*_trim.fastq.gz); do
		echo $Reads
		OutDir=$(dirname $Reads)
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/dna_qc
		qsub $ProgDir/sub_count_nuc.sh $GenomeSz $Reads $OutDir
	done
	for Reads in $(ls qc_dna/paired/*/*/*/*_trim.fq.gz); do
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/dna_qc
		qsub $ProgDir/sub_count_nuc.sh $GenomeSz $Reads
	done
```

The results for this are in fusarium_ex_pea and are *_cov.txt files. To look at all the results from coverage estimation script you can search for the last line and display that:

```bash
cd /home/groups/harrisonlab/project_files/fusarium_ex_pea
grep "equates" *_cov.txt
```

illumina
F81_S3_L001_R1_001_trim_cov.txt: This equates to an estimated genome coverage of 28.59 .
F81_S3_L001_R2_001_trim_cov.txt: This equates to an estimated genome coverage of 26.83 .
FOP1-EMR_S2_L001_R1_001_trim_cov.txt: This equates to an estimated genome coverage of 39.83 .
FOP1-EMR_S2_L001_R2_001_trim_cov.txt: This equates to an estimated genome coverage of 37.31 .
R2_S1_L001_R1_001_trim_cov.txt: This equates to an estimated genome coverage of 32.79 .
R2_S1_L001_R2_001_trim_cov.txt: This equates to an estimated genome coverage of 30.70 

Minion
These were put into: /home/groups/harrisonlab/project_files/fusarium_ex_pea/qc_dna/minion/F.oxysporum_fsp_pisi/*
F81 trimmed minion: This equates to an estimated genome coverage of 43.93
FOP1 EMR trimmed minion: This equates to an estimated genome coverage of 62.40 
R2 trimmed minion: This equates to an estimated genome coverage of 47.13



## Canu assembly

F81
```bash
	Organism=F.oxysporum_fsp_pisi
	Strain=F81
	Reads1=$(ls qc_dna/minion/$Organism/$Strain/*_trim.fastq.gz)
	GenomeSz="52m"
	Prefix="$Strain"
	OutDir=assembly/canu-1.6/$Organism/$Strain
	ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/canu
	qsub $ProgDir/submit_canu_minion.sh $Reads1 $GenomeSz $Prefix $OutDir
```


FOP1 EMR

```bash
	Organism=F.oxysporum_fsp_pisi
	Strain=FOP1-EMR
	Reads1=$(ls qc_dna/minion/$Organism/$Strain/*_trim.fastq.gz)
	GenomeSz="52m"
	Prefix="$Strain"
	OutDir=assembly/canu-1.6/$Organism/$Strain
	ProgDir=~/git_repos/tools/seq_tools/assemblers/canu
	qsub $ProgDir/submit_canu_minion.sh $Reads1 $GenomeSz $Prefix $OutDir
```

R2

```bash
	Organism=F.oxysporum_fsp_pisi
	Strain=R2
	Reads1=$(ls qc_dna/minion/$Organism/$Strain/*_trim.fastq.gz)
	GenomeSz="52m"
	Prefix="$Strain"
	OutDir=assembly/canu-1.6/$Organism/$Strain
	ProgDir=~/git_repos/tools/seq_tools/assemblers/canu
	qsub $ProgDir/submit_canu_minion.sh $Reads1 $GenomeSz $Prefix $OutDir
```


## Assembly using SMARTdenovo

F81

```bash
	for CorrectedReads in $(ls assembly/canu-1.6/F.oxysporum_fsp_pisi/F81/F81.trimmedReads.fasta.gz); do
		Organism=$(echo $CorrectedReads | rev | cut -f3 -d '/' | rev)
		Strain=$(echo $CorrectedReads | rev | cut -f2 -d '/' | rev)
		Prefix="$Strain"
		OutDir=assembly/SMARTdenovo/$Organism/"$Strain"
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/SMARTdenovo
		qsub $ProgDir/sub_SMARTdenovo.sh $CorrectedReads $Prefix $OutDir
	done
```

FOP1 EMR

```bash
	for CorrectedReads in $(ls assembly/canu-1.6/F.oxysporum_fsp_pisi/FOP1-EMR/FOP1-EMR.trimmedReads.fasta.gz); do
		Organism=$(echo $CorrectedReads | rev | cut -f3 -d '/' | rev)
		Strain=$(echo $CorrectedReads | rev | cut -f2 -d '/' | rev)
		Prefix="$Strain"
		OutDir=assembly/SMARTdenovo/$Organism/"$Strain"
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/SMARTdenovo
		qsub $ProgDir/sub_SMARTdenovo.sh $CorrectedReads $Prefix $OutDir
	done
```

R2  

```bash
	for CorrectedReads in $(ls assembly/canu-1.6/F.oxysporum_fsp_pisi/R2/R2.trimmedReads.fasta.gz); do
		Organism=$(echo $CorrectedReads | rev | cut -f3 -d '/' | rev)
		Strain=$(echo $CorrectedReads | rev | cut -f2 -d '/' | rev)
		Prefix="$Strain"
		OutDir=assembly/SMARTdenovo/$Organism/"$Strain"
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/SMARTdenovo
		qsub $ProgDir/sub_SMARTdenovo.sh $CorrectedReads $Prefix $OutDir
	done
```



### Contigs shorter than 500bp were removed from the assembly 


```bash
	for Contigs in $(ls assembly/SMARTdenovo/*/*/*.dmo.lay.utg); do
		AssemblyDir=$(dirname $Contigs)
		mkdir $AssemblyDir/filtered_contigs
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/abyss
		$ProgDir/filter_abyss_contigs.py $Contigs 500 > $AssemblyDir/contigs_min_500bp.fasta
	done
```

## Quast (canu) 

```bash
	ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/assembly_qc/quast
		for Assembly in $(ls assembly/canu-1.6/*/*/*.contigs.fasta); do
		Strain=$(echo $Assembly | rev | cut -f2 -d '/' | rev)
		Organism=$(echo $Assembly | rev | cut -f3 -d '/' | rev)  
		OutDir=assembly/canu-1.6/$Organism/$Strain/filtered_contigs
		qsub $ProgDir/sub_quast.sh $Assembly $OutDir
	done
```

### Quast (SMARTdenovo)

```bash
	ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/assembly_qc/quast
		for Assembly in $(ls assembly/SMARTdenovo/*/*/contigs_min_500bp.fasta); do
		Strain=$(echo $Assembly | rev | cut -f2 -d '/' | rev)
		Organism=$(echo $Assembly | rev | cut -f3 -d '/' | rev)  
		OutDir=$(dirname $Assembly)
		qsub $ProgDir/sub_quast.sh $Assembly $OutDir
	done
```


## Error correction using Racon (canu)

Didnt actually need to do this as we will only use SMARTdenovo from now on. Only needs ReadsFq1 line and no others, although I actually used all the lines when I ran it and the first qsub line

```bash
	for Assembly in $(ls assembly/canu-1.6/*/*/*.contigs.fasta); do
		Strain=$(echo $Assembly | rev | cut -f2 -d '/' | rev)
		Organism=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
		echo "$Organism - $Strain"
		ReadsFq1=$(ls qc_dna/minion/$Organism/$Strain/*_trim.fastq.gz | head -n1 | tail -n1)
		#ReadsFq2=$(ls qc_dna/minion/$Organism/$Strain/*_trim.fastq.gz | head -n2 | tail -n1)
		#ReadsAppended=qc_dna/minion/$Organism/$Strain/"$Strain"_reads_appended.fastq.gz
		cat $ReadsFq1 $ReadsFq2 > $ReadsAppended
		OutDir=assembly/canu-1.6/$Organism/$Strain/racon
		Iterations=10
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/racon
		#qsub $ProgDir/sub_racon.sh $Assembly $ReadsAppended $Iterations $OutDir
		qsub $ProgDir/sub_racon.sh $Assembly $ReadFq1 $Iterations $OutDir
	done
	rm $ReadsAppended
```


## Error correction using Racon (SMARTdenovo)   


```bash
	for Assembly in $(ls assembly/SMARTdenovo/*/*/contigs_min_500bp.fasta); do
		Strain=$(echo $Assembly | rev | cut -f2 -d '/' | rev)
		Organism=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
		ReadsFq1=$(ls qc_dna/minion/$Organism/$Strain/*_trim.fastq.gz)
		OutDir=$(dirname $Assembly)/racon
		Iterations=10
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/racon
		qsub $ProgDir/sub_racon.sh $Assembly $ReadsFq1 $Iterations $OutDir
	done
```



re-ran using racon output round 10 to do more iterations:   22.2.18

contigs_min_500bp_racon_round_10.fasta

```bash
for Assembly in $(ls assembly/SMARTdenovo/*/*/racon/contigs_min_500bp_racon_round_10.fasta); do
Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)
ReadsFq1=$(ls qc_dna/minion/$Organism/$Strain/*_trim.fastq.gz)
OutDir=$(dirname $Assembly)/racon_11-15
Iterations=5
ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/racon
qsub $ProgDir/sub_racon.sh $Assembly $ReadsFq1 $Iterations $OutDir
done
```





```bash  
	ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/assembly_qc/quast
		for Assembly in $(ls assembly/SMARTdenovo/*/*/racon/contigs_min_500bp_racon_round_10.fasta); do
		OutDir=$(dirname $Assembly)
		echo "" > tmp.txt
		ProgDir=~/git_repos/tools/seq_tools/assemblers/assembly_qc/remove_contaminants
		$ProgDir/remove_contaminants.py --keep_mitochondria --inp $Assembly --out $OutDir/racon_min_500bp_renamed.fasta --coord_file tmp.txt > $OutDir/log.txt
	done
```




## Quast and busco were run to assess the effects of racon on assembly quality:

```bash
	ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/assembly_qc/quast
		for Assembly in $(ls assembly/SMARTdenovo/*/*/racon/contigs_min_500bp_racon_round_10.fasta); do
		Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
		Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)  
		OutDir=$(dirname $Assembly)
		qsub $ProgDir/sub_quast.sh $Assembly $OutDir
	done
```

```bash   
for Assembly in $(ls assembly/SMARTdenovo/*/R2/racon/contigs_min_500bp_racon_round_9.fasta); do
Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)
echo "$Organism - $Strain"
ProgDir=/home/jenkis/git_repos/tools/gene_prediction/busco
BuscoDB=$(ls -d /home/groups/harrisonlab/dbBusco/sordariomyceta_odb9)
OutDir=gene_pred/busco/$Organism/$Strain/assembly
qsub $ProgDir/sub_busco3.sh $Assembly $BuscoDB $OutDir
done
```

for writing to scratch... also run from scratch   20.2.18   Did busco for all rounds on all isolates (stored on /data/scratch)

for Assembly in $(ls /home/groups/harrisonlab/project_files/fusarium_ex_pea/assembly/SMARTdenovo/*/R2/racon/contigs_min_500bp_racon_round_9.fasta); do
Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)
echo "$Organism - $Strain"
ProgDir=/home/jenkis/git_repos/tools/gene_prediction/busco
BuscoDB=$(ls -d /home/groups/harrisonlab/dbBusco/sordariomyceta_odb9)
OutDir=R2
qsub $ProgDir/sub_busco3.sh $Assembly $BuscoDB $OutDir
done






# Assembly correction using nanopolish 

Fast5 files are very large and need to be stored as gzipped tarballs. These needed temporarily unpacking but must be deleted after nanpolish has finished running.

Raw reads were moved onto the cluster scratch space for this step and unpacked:

```bash
ScratchDir=/data/scratch/nanopore_tmp_data/Fven
mkdir -p $ScratchDir
cp raw_dna/minion/F.oxysporum_fsp_pisi/*/*.tar.gz $ScratchDir/.
for Tar in $(ls $ScratchDir/*.tar.gz); do
tar -zxvf $Tar -C $ScratchDir
done
```

ScratchDir=/data/scratch/nanopore_tmp_data/Fven
for Tar in $(ls $ScratchDir/F.oxysporum_fsp_pisi_R2_2017-05-17.tar.gz); do
tar -zxvf $Tar -C $ScratchDir
done

Needed to update generic profile and seq tools to get these to work.


FOP1 EMR
```bash
for Assembly in $(ls assembly/SMARTdenovo/*/FOP1-EMR/racon/racon_min_500bp_renamed.fasta); do
Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)
echo "$Organism - $Strain"
ReadsFq1=$(ls raw_dna/minion/*/FOP1-EMR/*.fastq.gz)
ScratchDir=/data/scratch/nanopore_tmp_data/Fven
Fast5Dir1=$ScratchDir/F.oxysporum_fsp_pisi_FOP1-EMR_2017-07-11/workspace/pass
#nanopolish index -d $Fast5Dir1 $ReadsFq1
OutDir=$(dirname $Assembly)
mkdir -p $OutDir
ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/nanopolish
#ProgDir=/home/armita/git_repos/emr_repos/tools/seq_tools/assemblers/nanopolish
qsub $ProgDir/sub_bwa_nanopolish.sh $Assembly $ReadsFq1 $OutDir/nanopolish
done
```



F81
```bash
for Assembly in $(ls assembly/SMARTdenovo/*/F81/racon/racon_min_500bp_renamed.fasta); do
Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)
echo "$Organism - $Strain"
ReadsFq1=$(ls raw_dna/minion/*/F81/*.fastq.gz)
ScratchDir=/data/scratch/nanopore_tmp_data/Fven
Fast5Dir1=$ScratchDir/F.oxysporum_fsp_pisi_F81_2017-05-17/workspace/pass
#nanopolish index -d $Fast5Dir1 $ReadsFq1
OutDir=$(dirname $Assembly)
mkdir -p $OutDir
ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/nanopolish
qsub $ProgDir/sub_bwa_nanopolish.sh $Assembly $ReadsFq1 $OutDir/nanopolish
done
```


R2
```bash
for Assembly in $(ls assembly/SMARTdenovo/*/R2/racon/racon_min_500bp_renamed.fasta); do
Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)
echo "$Organism - $Strain"
ReadsFq1=$(ls raw_dna/minion/*/R2/*.fastq.gz)
ScratchDir=/data/scratch/nanopore_tmp_data/Fven
Fast5Dir1=$ScratchDir/F.oxysporum_fsp_pisi_R2_2017-05-17/workspace/pass
#nanopolish index -d $Fast5Dir1 $ReadsFq1
OutDir=$(dirname $Assembly)
mkdir -p $OutDir
ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/nanopolish
qsub $ProgDir/sub_bwa_nanopolish.sh $Assembly $ReadsFq1 $OutDir/nanopolish
done
```




### Split the assembly into 50Kb fragments and submit each to the cluster for nanopolish correction

#### FOP1-EMR
```bash
for Assembly in $(ls assembly/SMARTdenovo/*/FOP1-EMR/racon/racon_min_500bp_renamed.fasta); do
Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)
echo "$Organism - $Strain"
OutDir=$(dirname $Assembly)
RawReads=$(ls raw_dna/minion/*/FOP1-EMR/*.fastq.gz)
AlignedReads=$(ls $OutDir/nanopolish/reads.sorted.bam)

NanoPolishDir=/home/armita/prog/nanopolish/nanopolish/scripts
python $NanoPolishDir/nanopolish_makerange.py $Assembly > $OutDir/nanopolish/nanopolish_range.txt

Ploidy=1
echo "nanopolish log:" > nanopolish_log.txt
for Region in $(cat $OutDir/nanopolish/nanopolish_range.txt); do
Jobs=$(qstat | grep 'sub_nanopo' | grep 'qw' | wc -l)
while [ $Jobs -gt 1 ]; do
sleep 1m
printf "."
Jobs=$(qstat | grep 'sub_nanopo' | grep 'qw' | wc -l)
done		
printf "\n"
echo $Region
echo $Region >> nanopolish_log.txt
#ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/nanopolish
ProgDir=/home/armita/git_repos/emr_repos/tools/seq_tools/assemblers/nanopolish
qsub $ProgDir/sub_nanopolish_variants.sh $Assembly $RawReads $AlignedReads $Ploidy $Region $OutDir/$Region
done
done
```

to test with 1 region set Region to:
Region=$(head -n1 $OutDir/nanopolish/nanopolish_range.txt) 



Some regions didn't run properly so they needed re-running with a different script:

```bash
for Assembly in $(ls assembly/SMARTdenovo/*/FOP1-EMR/racon/racon_min_500bp_renamed.fasta); do
Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)
echo "$Organism - $Strain"
OutDir=$(dirname $Assembly)/nanopolish
RawReads=$(ls raw_dna/minion/*/FOP1-EMR/*.fastq.gz)
AlignedReads=$(ls $OutDir/reads.sorted.bam)

NanoPolishDir=/home/armita/prog/nanopolish/nanopolish/scripts

Ploidy=1
echo "nanopolish log:" > $OutDir/nanopolish_log.txt
for Region in $(cat $OutDir/nanopolish_range.txt | grep -e 'contig_14:200000-250200' -e 'contig_38:50000-100200' -e 'contig_39:0-50200' -e 'contig_45:0-50200'); do
echo $Region
echo $Region >> $OutDir/nanopolish_log.txt
ProgDir=/home/armita/git_repos/emr_repos/tools/seq_tools/assemblers/nanopolish
qsub $ProgDir/sub_nanopolish_variants_high_mem.sh $Assembly $RawReads $AlignedReads $Ploidy $Region $OutDir/$Region
done
done
```


FILES THAT NEEDED Re-submitting with sub_nanopolish_variants_high_mem.sh
contig_14:200000-250200      job number 8479545

contig_38:50000-100200                  8479898 

contig_39:0-50200                       8479905

contig_45:0-50200                       8479945

contig_38:50040    reran contig_38:50000-100200 due to being missing jn:8485043



```bash
for Assembly in $(ls assembly/SMARTdenovo/*/FOP1-EMR/racon/racon_min_500bp_renamed.fasta); do
Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)
OutDir=assembly/SMARTdenovo/$Organism/$Strain/nanopolish
# cat "" > $OutDir/"$Strain"_nanopolish.fa
InDir=$(dirname $Assembly)/nanopolish
NanoPolishDir=/home/armita/prog/nanopolish/nanopolish/scripts
python $NanoPolishDir/nanopolish_merge.py $InDir/*:*-*/*.fa > $OutDir/"$Strain"_nanopolish.fa

echo "" > tmp.txt
ProgDir=~/git_repos/tools/seq_tools/assemblers/assembly_qc/remove_contaminants
$ProgDir/remove_contaminants.py --keep_mitochondria --inp $OutDir/"$Strain"_nanopolish.fa --out $OutDir/"$Strain"_nanopolish_min_500bp_renamed.fasta --coord_file tmp.txt > $OutDir/log.txt
done
```






#### F81
```bash
for Assembly in $(ls assembly/SMARTdenovo/*/F81/racon/racon_min_500bp_renamed.fasta); do
Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)
echo "$Organism - $Strain"
OutDir=$(dirname $Assembly)/nanopolish
RawReads=$(ls raw_dna/minion/*/F81/*.fastq.gz)
AlignedReads=$(ls $OutDir/reads.sorted.bam)

NanoPolishDir=/home/armita/prog/nanopolish/nanopolish/scripts
python $NanoPolishDir/nanopolish_makerange.py $Assembly > $OutDir/nanopolish_range.txt

Ploidy=1
echo "nanopolish log:" > nanopolish_log.txt
for Region in $(cat $OutDir/nanopolish_range.txt); do
Jobs=$(qstat | grep 'sub_nanopo' | grep 'qw' | wc -l)
while [ $Jobs -gt 1 ]; do
sleep 1m
printf "."
Jobs=$(qstat | grep 'sub_nanopo' | grep 'qw' | wc -l)
done		
printf "\n"
echo $Region
echo $Region >> nanopolish_log.txt
#ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/nanopolish
ProgDir=/home/armita/git_repos/emr_repos/tools/seq_tools/assemblers/nanopolish
qsub $ProgDir/sub_nanopolish_variants.sh $Assembly $RawReads $AlignedReads $Ploidy $Region $OutDir/$Region
done
done
```
still running: contig_65:0-50200



```bash
for Assembly in $(ls assembly/SMARTdenovo/*/F81/racon/racon_min_500bp_renamed.fasta); do
Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)
OutDir=assembly/SMARTdenovo/$Organism/$Strain/nanopolish
# cat "" > $OutDir/"$Strain"_nanopolish.fa
InDir=$(dirname $Assembly)/nanopolish
NanoPolishDir=/home/armita/prog/nanopolish/nanopolish/scripts
python $NanoPolishDir/nanopolish_merge.py $InDir/*:*-*/*.fa > $OutDir/"$Strain"_nanopolish.fa

echo "" > tmp.txt
ProgDir=~/git_repos/tools/seq_tools/assemblers/assembly_qc/remove_contaminants
$ProgDir/remove_contaminants.py --keep_mitochondria --inp $OutDir/"$Strain"_nanopolish.fa --out $OutDir/"$Strain"_nanopolish_min_500bp_renamed.fasta --coord_file tmp.txt > $OutDir/log.txt
done
```



#### R2
```bash
for Assembly in $(ls assembly/SMARTdenovo/*/R2/racon/racon_min_500bp_renamed.fasta); do
Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)
echo "$Organism - $Strain"
OutDir=$(dirname $Assembly)/nanopolish
RawReads=$(ls raw_dna/minion/*/R2/*.fastq.gz)
AlignedReads=$(ls $OutDir/reads.sorted.bam)

NanoPolishDir=/home/armita/prog/nanopolish/nanopolish/scripts
python $NanoPolishDir/nanopolish_makerange.py $Assembly > $OutDir/nanopolish_range.txt

Ploidy=1
echo "nanopolish log:" > nanopolish_log.txt
for Region in $(cat $OutDir/nanopolish_range.txt); do
Jobs=$(qstat | grep 'sub_nanopo' | grep 'qw' | wc -l)
while [ $Jobs -gt 1 ]; do
sleep 30s
printf "."
Jobs=$(qstat | grep 'sub_nanopo' | grep 'qw' | wc -l)
done		
printf "\n"
echo $Region
echo $Region >> nanopolish_log.txt
#ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/nanopolish
ProgDir=/home/armita/git_repos/emr_repos/tools/seq_tools/assemblers/nanopolish
qsub $ProgDir/sub_nanopolish_variants.sh $Assembly $RawReads $AlignedReads $Ploidy $Region $OutDir/$Region
done
done
```


```bash
for Assembly in $(ls assembly/SMARTdenovo/*/R2/racon/racon_min_500bp_renamed.fasta); do
Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)
OutDir=assembly/SMARTdenovo/$Organism/$Strain/nanopolish
# cat "" > $OutDir/"$Strain"_nanopolish.fa
InDir=$(dirname $Assembly)/nanopolish
NanoPolishDir=/home/armita/prog/nanopolish/nanopolish/scripts
python $NanoPolishDir/nanopolish_merge.py $InDir/*:*-*/*.fa > $OutDir/"$Strain"_nanopolish.fa

echo "" > tmp.txt
ProgDir=~/git_repos/tools/seq_tools/assemblers/assembly_qc/remove_contaminants
$ProgDir/remove_contaminants.py --keep_mitochondria --inp $OutDir/"$Strain"_nanopolish.fa --out $OutDir/"$Strain"_nanopolish_min_500bp_renamed.fasta --coord_file tmp.txt > $OutDir/log.txt
done
```



### Quast and busco were run to assess the effects of nanopolish on assembly quality:

```bash
for Assembly in $(ls assembly/SMARTdenovo/*/*/nanopolish/*_nanopolish_min_500bp_renamed.fasta); do
  Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
  Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)  
	# Quast
  OutDir=$(dirname $Assembly)
	ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/assembly_qc/quast
  qsub $ProgDir/sub_quast.sh $Assembly $OutDir
	# Busco
	BuscoDB=$(ls -d /home/groups/harrisonlab/dbBusco/sordariomyceta_odb9)
	OutDir=gene_pred/busco/$Organism/$Strain/nanopolish
	ProgDir=/home/jenkis/git_repos/tools/gene_prediction/busco
	qsub $ProgDir/sub_busco3.sh $Assembly $BuscoDB $OutDir
done
```



# Assemblies were polished using Pilon

#### FOP1-EMR

```bash
for Assembly in $(ls assembly/SMARTdenovo/*/FOP1-EMR/nanopolish/*_nanopolish_min_500bp_renamed.fasta); do
Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)
Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
IlluminaDir=$(ls -d qc_dna/paired/$Organism/$Strain)
echo $Strain
echo $Organism
TrimF1_Read=$(ls $IlluminaDir/F/*_trim.fq.gz);
TrimR1_Read=$(ls $IlluminaDir/R/*_trim.fq.gz);
echo $TrimF1_Read
echo $TrimR1_Read
OutDir=$(dirname $Assembly)
Iterations=10
ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/pilon
qsub $ProgDir/sub_pilon.sh $Assembly $TrimF1_Read $TrimR1_Read $OutDir $Iterations
done
```

#### F81

```bash
for Assembly in $(ls assembly/SMARTdenovo/*/F81/nanopolish/*_nanopolish_min_500bp_renamed.fasta); do
Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)
Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
IlluminaDir=$(ls -d qc_dna/paired/$Organism/$Strain)
echo $Strain
echo $Organism
TrimF1_Read=$(ls $IlluminaDir/F/*_trim.fq.gz);
TrimR1_Read=$(ls $IlluminaDir/R/*_trim.fq.gz);
echo $TrimF1_Read
echo $TrimR1_Read
OutDir=$(dirname $Assembly)
Iterations=10
ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/pilon
qsub $ProgDir/sub_pilon.sh $Assembly $TrimF1_Read $TrimR1_Read $OutDir $Iterations
done
```


#### R2

```bash
for Assembly in $(ls assembly/SMARTdenovo/*/R2/nanopolish/*_nanopolish_min_500bp_renamed.fasta); do
Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)
Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
IlluminaDir=$(ls -d qc_dna/paired/$Organism/$Strain)
echo $Strain
echo $Organism
TrimF1_Read=$(ls $IlluminaDir/F/*_trim.fq.gz);
TrimR1_Read=$(ls $IlluminaDir/R/*_trim.fq.gz);
echo $TrimF1_Read
echo $TrimR1_Read
OutDir=$(dirname $Assembly)
Iterations=10
ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/pilon
qsub $ProgDir/sub_pilon.sh $Assembly $TrimF1_Read $TrimR1_Read $OutDir $Iterations
done
```


## Summarising assemblies using quast:

```bash
ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/assembly_qc/quast
for Assembly in $(ls assembly/SMARTdenovo/*/*/nanopolish/pilon_*.fasta | grep 'pilon_10'); do
Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)  
echo "$Organism - $Strain"
OutDir=$(dirname $Assembly)
qsub $ProgDir/sub_quast.sh $Assembly $OutDir
done
```


### Checking using busco

```bash
for Assembly in $(ls assembly/SMARTdenovo/*/*/nanopolish/pilon_*.fasta); do
  Strain=$(echo $Assembly| rev | cut -d '/' -f3 | rev)
  Organism=$(echo $Assembly | rev | cut -d '/' -f4 | rev)
  echo "$Organism - $Strain"
  ProgDir=/home/jenkis/git_repos/tools/gene_prediction/busco
  BuscoDB=$(ls -d /home/groups/harrisonlab/dbBusco/sordariomyceta_odb9)
  OutDir=gene_pred/busco/$Organism/"$Strain"_pilon/assembly
  qsub $ProgDir/sub_busco3.sh $Assembly $BuscoDB $OutDir
done
```

### remove contaminants from pilon_10
```bash
for Assembly in $(ls assembly/SMARTdenovo/*/*/nanopolish/pilon_*.fasta | grep 'pilon_10'); do
Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)
OutDir=$(dirname $Assembly)
echo "" > tmp.txt
ProgDir=~/git_repos/tools/seq_tools/assemblers/assembly_qc/remove_contaminants
$ProgDir/remove_contaminants.py --keep_mitochondria --inp $Assembly --out $OutDir/"$Strain"_pilon_min_500bp_renamed.fasta --coord_file tmp.txt > $OutDir/log.txt
done
```








This script is on andrews page (Fv_minion_assembly.md) if you look at the raw file on github. Dont run unless SMARTdenovo isnt as good

many steps above this need doing

Hybrid assembly: Spades Assembly
```bash
Reads1=$(ls qc_dna/minion/F.venenatum/WT/F.venenatum_WT_07-03-17_albacore_v2.02_trim.fastq.gz)
  Reads2=$(ls qc_dna/minion/F.venenatum/WT/F.venenatum_WT_18-07-17_albacore_v2.02_trim.fastq.gz)
  Organism=$(echo $Reads1 | head -n1 | rev | cut -f3 -d '/' | rev)
  Strain=$(echo $Reads1 | head -n1 | rev | cut -f2 -d '/' | rev)
  IlluminaDir=$(ls -d qc_dna/paired/$Organism/$Strain)
  echo $Strain
  echo $Organism
  TrimF1_Read=$(ls $IlluminaDir/F/*_trim.fq.gz | head -n1 | tail -n1);
  TrimR1_Read=$(ls $IlluminaDir/R/*_trim.fq.gz | head -n1 | tail -n1);
  TrimF2_Read=$(ls $IlluminaDir/F/*_trim.fq.gz | head -n2 | tail -n1);
  TrimR2_Read=$(ls $IlluminaDir/R/*_trim.fq.gz | head -n2 | tail -n1);
  TrimF3_Read=$(ls $IlluminaDir/F/*_trim.fq.gz | head -n3 | tail -n1);
  TrimR3_Read=$(ls $IlluminaDir/R/*_trim.fq.gz | head -n3 | tail -n1);
  echo $TrimF1_Read
  echo $TrimR1_Read
  echo $TrimF2_Read
  echo $TrimR2_Read
  echo $TrimF3_Read
  echo $TrimR3_Read
  OutDir=assembly/spades_minion/$Organism/"$Strain"_albacore_v2
  # ProgDir=/home/armita/git_repos/emr_repos/tools/seq_tools/assemblers/spades/multiple_libraries
  # qsub $ProgDir/subSpades_3lib_minion.sh tmp_assembly/miniondat.fastq.gz $TrimF1_Read $TrimR1_Read $TrimF2_Read $TrimR2_Read $TrimF3_Read $TrimR3_Read $OutDir
  ProgDir=/home/armita/git_repos/emr_repos/scripts/fusarium_venenatum/assembly
  qsub $ProgDir/Fv_spades_hybrid.sh $Reads1 $Reads2 $TrimF1_Read $TrimR1_Read $TrimF2_Read $TrimR2_Read $TrimF3_Read $TrimR3_Read $OutDir
  # done
  rm -r tmp_assembly
```
