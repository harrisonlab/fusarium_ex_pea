# Fusarium oxysporum f.sp. pisi (PacBio Sequences)
##Commands used for the analysis of F.oxysporum f.sp pisi sequences from PacBio


	cp /home/armita/generic_profiles/2016-07-28/.generic_profile ~/.profile
(change the dates for future updates)


To look at your profile:

	less ~/.profile
OR
	cd /home/armita
	less .profile



You will find your files in:

ls /home/miseq_data/pacbio/2017/John_Clarkson_UWAR.JC.ENQ-1929

and your seqs are:

S1 (FOP1) = Fusarium oxysporum f.sp. pisi
S2 (FOP2) = Fusarium oxysporum f.sp. pisi
S3R1 (FOP5) = Fusarium oxysporum f.sp. pisi


## Building a directory structure

```bash
	RawDatDir=/home/miseq_data/pacbio/2017/John_Clarkson_UWAR.JC.ENQ-1929
	ProjectDir=/home/groups/harrisonlab/project_files/fusarium_ex_pea
	mkdir -p $ProjectDir/raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP1
	mkdir -p $ProjectDir/raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP2
	mkdir -p $ProjectDir/raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP5
```	

## Sequence data was moved into appropriate directories

```bash
	RawDatDir=/home/miseq_data/pacbio/2017/John_Clarkson_UWAR.JC.ENQ-1929
	ProjectDir=/home/groups/harrisonlab/project_files/fusarium_ex_pea
	cp $RawDatDir/John_Clarkson_UWAR.JC.ENQ-1929_S1.tar.gz $ProjectDir/raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP1
	cp $RawDatDir/John_Clarkson_UWAR.JC.ENQ-1929_S1_raw_reads.tar.gz $ProjectDir/raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP1
	cp $RawDatDir/John_Clarkson_UWAR.JC.ENQ-1929_R_S2.tar.gz $ProjectDir/raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP2
	cp $RawDatDir/John_Clarkson_UWAR.JC.ENQ-1929_R_S3R1.tar.gz $ProjectDir/raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP5	
```

## Data was extracted for each isolate and concatenated 

FOP2 extracted 

```bash
	cd raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP2
	tar -zxvf John_Clarkson_UWAR.JC.ENQ-1929_R_S2.tar.gz
```

FOP5 extracted 

```bash
	cd raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP5
	tar -zxvf John_Clarkson_UWAR.JC.ENQ-1929_R_S3R1.tar.gz
```

FOP1 (both files) extracted 

```bash
	cd raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP1
	tar -zxvf John_Clarkson_UWAR.JC.ENQ-1929_S1_raw_reads.tar.gz
	tar -zxvf John_Clarkson_UWAR.JC.ENQ-1929_S1.tar.gz
```

FOP2 concatenated 

```bash
	OutDir=raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP2/extracted
	mkdir -p $OutDir
	for H5_File in $(ls raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP2/*/raw_reads/*/Analysis_Results/*.bas.h5); do
	ReadSet=$(echo $H5_File | rev | cut -f3 -d '/' | rev)
	bash5tools.py $H5_File --outFile $OutDir/FOP2_pacbio_$ReadSet --outType fastq --readType unrolled --minLength 100
	done
	cat raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP2/*/raw_reads/*/Analysis_Results/*.subreads.fastq > $OutDir/concatenated_pacbio.fastq
```

FOP5 concatenated 

```bash
	OutDir=raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP5/extracted
	mkdir -p $OutDir
	for H5_File in $(ls raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP5/*/raw_reads/*/Analysis_Results/*.bas.h5); do
	ReadSet=$(echo $H5_File | rev | cut -f3 -d '/' | rev)
	bash5tools.py $H5_File --outFile $OutDir/FOP5_pacbio_$ReadSet --outType fastq --readType unrolled --minLength 100
	done
	cat raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP5/*/raw_reads/*/Analysis_Results/*.subreads.fastq > $OutDir/concatenated_pacbio.fastq
```

FOP1 concatenated 

```bash
	OutDir=raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP1/extracted
	mkdir -p $OutDir
	for H5_File in $(ls raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP1/*raw_reads/*/Analysis_Results/*.bas.h5); do
	ReadSet=$(echo $H5_File | rev | cut -f3 -d '/' | rev)
	bash5tools.py $H5_File --outFile $OutDir/FOP1_pacbio_$ReadSet --outType fastq --readType unrolled --minLength 100
	done
	cat raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP1/*/raw_reads/*/Analysis_Results/*.subreads.fastq > $OutDir/concatenated_pacbio.fastq
```


**No fastq files or h5 files in John_Clarkson_UWAR.JC.ENQ-1929_S1.tar.gz **

##Canu Assembly 

FOP2 assembly

```bash 
	Reads=$(ls raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP2/extracted/concatenated_pacbio.fastq)
  	GenomeSz="50m"
  	Strain=$(echo $Reads | rev | cut -f3 -d '/' | rev)
  	Organism=$(echo $Reads | rev | cut -f4 -d '/' | rev)
  	Prefix="$Strain"_canu
  	OutDir="assembly/canu-1.3/$Organism/$Strain"_canu
  	ProgDir=~/git_repos/tools/seq_tools/assemblers/canu
  	qsub $ProgDir/submit_canu.sh $Reads $GenomeSz $Prefix $OutDir
```

FOP5 assembly

```bash
	Reads=$(ls raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP5/extracted/concatenated_pacbio.fastq)
 	GenomeSz="50m"
  	Strain=$(echo $Reads | rev | cut -f3 -d '/' | rev)
  	Organism=$(echo $Reads | rev | cut -f4 -d '/' | rev)
  	Prefix="$Strain"_canu
  	OutDir="assembly/canu-1.3/$Organism/$Strain"_canu
  	ProgDir=~/git_repos/tools/seq_tools/assemblers/canu
  	qsub $ProgDir/submit_canu.sh $Reads $GenomeSz $Prefix $OutDir
``` 
  
FOP1 assembly

```bash
	Reads=$(ls raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP1/extracted/concatenated_pacbio.fastq)
  	GenomeSz="50m"
  	Strain=$(echo $Reads | rev | cut -f3 -d '/' | rev)
  	Organism=$(echo $Reads | rev | cut -f4 -d '/' | rev)
  	Prefix="$Strain"_canu
  	OutDir="assembly/canu-1.3/$Organism/$Strain"_canu
  	ProgDir=~/git_repos/tools/seq_tools/assemblers/canu
  	qsub $ProgDir/submit_canu.sh $Reads $GenomeSz $Prefix $OutDir
```



NEED TO DO-- CHANGE
Assemblies were polished using Pilon- for all of them

  for Assembly in $(ls assembly/canu/*/Fus2/edited_contigs/*_canu_contigs_modified.fasta); do
    Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)
    Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
    IlluminaDir=$(ls -d qc_dna/paired/$Organism/$Strain)
    TrimF1_Read=$(ls $IlluminaDir/F/s_6_1_sequence_trim.fq.gz);
    TrimR1_Read=$(ls $IlluminaDir/R/s_6_2_sequence_trim.fq.gz);
    OutDir=assembly/canu/$Organism/$Strain/edited_contigs
    ProgDir=/home/armita/git_repos/emr_repos/tools/seq_tools/assemblers/pilon
    qsub $ProgDir/sub_pilon.sh $Assembly $TrimF1_Read $TrimR1_Read $OutDir
  done




## Spades Assembly 

FOP2  

```bash
	for PacBioDat in $(ls raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP2/extracted/concatenated_pacbio.fastq); do
		Organism=$(echo $PacBioDat | rev | cut -f4 -d '/' | rev)
		Strain=$(echo $PacBioDat | rev | cut -f3 -d '/' | rev)
		IlluminaDir=$(ls -d qc_dna/paired/$Organism/$Strain)
		TrimF1_Read=$(ls $IlluminaDir/F/FOP2_S1_L001_R1_001_trim.fq.gz);
		TrimR1_Read=$(ls $IlluminaDir/R/FOP2_S1_L001_R2_001_trim.fq.gz);
		OutDir=assembly/spades_pacbio/$Organism/"$Strain"
		echo $TrimR1_Read
		echo $TrimR1_Read
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/spades
		qsub $ProgDir/sub_spades_pacbio.sh $PacBioDat $TrimF1_Read $TrimR1_Read $OutDir 20
	done
``` 

FOP5

```bash 
	for PacBioDat in $(ls raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP5/extracted/concatenated_pacbio.fastq); do
		Organism=$(echo $PacBioDat | rev | cut -f4 -d '/' | rev)
		Strain=$(echo $PacBioDat | rev | cut -f3 -d '/' | rev)
		IlluminaDir=$(ls -d qc_dna/paired/$Organism/$Strain)
		TrimF1_Read=$(ls $IlluminaDir/F/FOP5_S2_L001_R1_001_trim.fq.gz);
		TrimR1_Read=$(ls $IlluminaDir/R/FOP5_S2_L001_R2_001_trim.fq.gz);
		OutDir=assembly/spades_pacbio/$Organism/"$Strain"
		echo $TrimR1_Read
		echo $TrimR1_Read
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/spades
		qsub $ProgDir/sub_spades_pacbio.sh $PacBioDat $TrimF1_Read $TrimR1_Read $OutDir 20
	done
```


FOP1

```bash 
	for PacBioDat in $(ls raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP1/extracted/concatenated_pacbio.fastq); do
		Organism=$(echo $PacBioDat | rev | cut -f4 -d '/' | rev)
		Strain=$(echo $PacBioDat | rev | cut -f3 -d '/' | rev)
		IlluminaDir=$(ls -d qc_dna/paired/$Organism/$Strain)
		TrimF1_Read=$(ls $IlluminaDir/F/FOP1_S1_L001_R1_001_trim.fq.gz);
		TrimR1_Read=$(ls $IlluminaDir/R/FOP1_S1_L001_R2_001_trim.fq.gz);
		OutDir=assembly/spades_pacbio/$Organism/"$Strain"
		echo $TrimR1_Read
		echo $TrimR1_Read
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/spades
		qsub $ProgDir/sub_spades_pacbio.sh $PacBioDat $TrimF1_Read $TrimR1_Read $OutDir 20
	done
```



	