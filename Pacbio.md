# Fusarium oxysporum f.sp. pisi (PacBio Sequences)
## Commands used for the analysis of F.oxysporum f.sp pisi sequences from PacBio


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
	cat raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP1/*/*/Analysis_Results/*.subreads.fastq > $OutDir/concatenated_pacbio.fastq
```
*had to change the file structure in last line:
From:  raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP1/*/raw_reads/*/Analysis_Results/*.subreads.fastq
To:  raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP1/*/*/Analysis_Results/*.subreads.fastq


**No fastq files or h5 files in John_Clarkson_UWAR.JC.ENQ-1929_S1.tar.gz **



## Canu Assembly

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

FOP2 assembly

```bash
		Reads=$(ls raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP2/extracted/concatenated_pacbio.fastq)
  	GenomeSz="55m"
  	Strain=$(echo $Reads | rev | cut -f3 -d '/' | rev)
  	Organism=$(echo $Reads | rev | cut -f4 -d '/' | rev)
  	Prefix="$Strain"_canu
  	OutDir="assembly/canu-1.6/$Organism/$Strain"_canu
  	ProgDir=/home/armita/git_repos/emr_repos/tools/seq_tools/assemblers/canu
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



## Assembly stats were collected using quast

FOP2

```bash
	ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/assembly_qc/quast
		for Assembly in $(ls assembly/canu-1.3/*/FOP2_canu/*_canu.contigs.fasta); do
		Strain=$(echo $Assembly | rev | cut -f2 -d '/' | rev)
		Organism=$(echo $Assembly | rev | cut -f3 -d '/' | rev)  
		OutDir=assembly/canu-1.3/$Organism/$Strain/filtered_contigs
		qsub $ProgDir/sub_quast.sh $Assembly $OutDir
	done
```

FOP5

```bash
	ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/assembly_qc/quast
		for Assembly in $(ls assembly/canu-1.3/*/FOP5_canu/*_canu.contigs.fasta); do
		Strain=$(echo $Assembly | rev | cut -f2 -d '/' | rev)
		Organism=$(echo $Assembly | rev | cut -f3 -d '/' | rev)  
		OutDir=assembly/canu-1.3/$Organism/$Strain/filtered_contigs
		qsub $ProgDir/sub_quast.sh $Assembly $OutDir
	done
```

FOP1

```bash
	ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/assembly_qc/quast
		for Assembly in $(ls assembly/canu-1.3/*/FOP1_canu/*_canu.contigs.fasta); do
		Strain=$(echo $Assembly | rev | cut -f2 -d '/' | rev)
		Organism=$(echo $Assembly | rev | cut -f3 -d '/' | rev)  
		OutDir=assembly/canu-1.3/$Organism/$Strain/filtered_contigs
		qsub $ProgDir/sub_quast.sh $Assembly $OutDir
	done
```

LOOK AT ASSEMBLY STATS!

## Assemblies were polished using Pilon

FOP2
```bash
	for Assembly in $(ls assembly/canu-1.3/*/FOP2_canu/FOP2_canu.contigs.fasta); do
		Organism=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
		Strain=$(echo $Assembly | rev | cut -f2 -d '/' | rev)
		IlluminaDir=$(ls -d qc_dna/paired/$Organism/FOP2)
		TrimF1_Read=$(ls $IlluminaDir/F/FOP2_S1_L001_R1_001_trim.fq.gz);
		TrimR1_Read=$(ls $IlluminaDir/R/FOP2_S1_L001_R2_001_trim.fq.gz);
		OutDir=assembly/canu/$Organism/$Strain/polished
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/pilon
		qsub $ProgDir/sub_pilon.sh $Assembly $TrimF1_Read $TrimR1_Read $OutDir
	done
```


FOP5
```bash
	for Assembly in $(ls assembly/canu-1.3/*/FOP5_canu/FOP5_canu.contigs.fasta); do
		Organism=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
		Strain=$(echo $Assembly | rev | cut -f2 -d '/' | rev)
		IlluminaDir=$(ls -d qc_dna/paired/$Organism/FOP5)
		TrimF1_Read=$(ls $IlluminaDir/F/FOP5_S2_L001_R1_001_trim.fq.gz);
		TrimR1_Read=$(ls $IlluminaDir/R/FOP5_S2_L001_R2_001_trim.fq.gz);
		OutDir=assembly/canu/$Organism/$Strain/polished
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/pilon
		qsub $ProgDir/sub_pilon.sh $Assembly $TrimF1_Read $TrimR1_Read $OutDir
	done
```


FOP1
```bash
	for Assembly in $(ls assembly/canu-1.3/*/FOP1_canu/FOP1_canu.contigs.fasta); do
		Organism=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
		Strain=$(echo $Assembly | rev | cut -f2 -d '/' | rev)
		IlluminaDir=$(ls -d qc_dna/paired/$Organism/FOP1)
		TrimF1_Read=$(ls $IlluminaDir/F/FOP1_S1_L001_R1_001_trim.fq.gz);
		TrimR1_Read=$(ls $IlluminaDir/R/FOP1_S1_L001_R2_001_trim.fq.gz);
		OutDir=assembly/canu/$Organism/$Strain/polished
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/pilon
		qsub $ProgDir/sub_pilon.sh $Assembly $TrimF1_Read $TrimR1_Read $OutDir
	done
```


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


#Contigs shorter than 500bp were removed from the assembly

FOP1

```bash
	for Contigs in $(ls assembly/spades_pacbio/*/FOP1/contigs.fasta); do
		AssemblyDir=$(dirname $Contigs)
		mkdir $AssemblyDir/filtered_contigs
		FilterDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/abyss
		$FilterDir/filter_abyss_contigs.py $Contigs 500 > $AssemblyDir/filtered_contigs/contigs_min_500bp.fasta
	done
```

FOP2

```bash
	for Contigs in $(ls assembly/spades_pacbio/*/FOP2/contigs.fasta); do
		AssemblyDir=$(dirname $Contigs)
		mkdir $AssemblyDir/filtered_contigs
		FilterDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/abyss
		$FilterDir/filter_abyss_contigs.py $Contigs 500 > $AssemblyDir/filtered_contigs/contigs_min_500bp.fasta
	done
```

FOP5

```bash
	for Contigs in $(ls assembly/spades_pacbio/*/FOP5/contigs.fasta); do
		AssemblyDir=$(dirname $Contigs)
		mkdir $AssemblyDir/filtered_contigs
		FilterDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/abyss
		$FilterDir/filter_abyss_contigs.py $Contigs 500 > $AssemblyDir/filtered_contigs/contigs_min_500bp.fasta
	done
```


# Merging pacbio and hybrid assemblies

FOP1

Merging
```bash
	for PacBioAssembly in $(ls assembly/canu/F.oxysporum_fsp_pisi/FOP1_canu/polished/pilon.fasta); do
		Organism=$(echo $PacBioAssembly | rev | cut -f4 -d '/' | rev)
		Strain=FOP1
		HybridAssembly=$(ls assembly/spades_pacbio/$Organism/$Strain/contigs.fasta)
		OutDir=assembly/merged_canu_spades/$Organism/$Strain
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/quickmerge
		qsub $ProgDir/sub_quickmerge.sh $PacBioAssembly $HybridAssembly $OutDir
	done
```



Remove contaminants and renames files
```bash
	touch tmp.csv
		for Assembly in $(ls assembly/merged_canu_spades/F.oxysporum_fsp_pisi/FOP1/merged.fasta); do
		Organism=$(echo $Assembly | rev | cut -f3 -d '/' | rev)  
		Strain=$(echo $Assembly | rev | cut -f2 -d '/' | rev)
		OutDir=$(dirname $Assembly)
		mkdir -p $OutDir
		ProgDir=~/git_repos/tools/seq_tools/assemblers/assembly_qc/remove_contaminants
		$ProgDir/remove_contaminants.py --inp $Assembly --out $OutDir/"$Strain"_contigs_renamed.fasta --coord_file tmp.csv
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/assembly_qc/quast
		qsub $ProgDir/sub_quast.sh $Assembly $OutDir
	done
	rm tmp.csv
```
output of ^
assembly/merged_canu_spades/F.oxysporum_fsp_pisi/FOP1/FOP1_contigs_renamed.fasta


Merged assembly polished using Pilon
```bash
	for Assembly in $(ls assembly/merged_canu_spades/*/FOP1/FOP1_contigs_renamed.fasta); do
		Organism=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
		Strain=$(echo $Assembly | rev | cut -f2 -d '/' | rev)
		IlluminaDir=$(ls -d qc_dna/paired/$Organism/$Strain)
		TrimF1_Read=$(ls $IlluminaDir/F/FOP1_S1_L001_R1_001_trim.fq.gz);
		TrimR1_Read=$(ls $IlluminaDir/R/FOP1_S1_L001_R2_001_trim.fq.gz);
		OutDir=assembly/merged_canu_spades/$Organism/$Strain/polished
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/pilon
		qsub $ProgDir/sub_pilon.sh $Assembly $TrimF1_Read $TrimR1_Read $OutDir
	done
```


DO THIS?
Remove contaminants and rename
```bash
ProgDir=~/git_repos/tools/seq_tools/assemblers/assembly_qc/remove_contaminants
  touch tmp.csv
  # for Assembly in $(ls assembly/merged_canu_spades/*/*/polished/pilon.fasta); do
  # for Assembly in $(ls assembly/pacbio_test/F.oxysporum_fsp_cepae/Fus2_pacbio_merged/polished/pilon.fasta); do
    for Assembly in $(ls assembly/merged_canu_spades/F.oxysporum_fsp_cepae/Fus2_edited2/polished/pilon.fasta); do
  # for Assembly in $(ls assembly/merged_canu_spades/F.oxysporum_fsp_cepae/Fus2_edited2/polished/pilon.fasta); do
    Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)  
    Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
    # OutDir=assembly/merged_canu_spades/$Organism/$Strain/filtered_contigs
    OutDir=assembly/merged_canu_spades/$Organism/$Strain/filtered_contigs
    mkdir -p $OutDir
    $ProgDir/remove_contaminants.py --inp $Assembly --out $OutDir/"$Strain"_contigs_renamed.fasta --coord_file tmp.csv
  done
  rm tmp.csv
```



FOP2

Merging
```bash
	for PacBioAssembly in $(ls assembly/canu/F.oxysporum_fsp_pisi/FOP2_canu/polished/pilon.fasta); do
		Organism=$(echo $PacBioAssembly | rev | cut -f4 -d '/' | rev)
		Strain=FOP2
		HybridAssembly=$(ls assembly/spades_pacbio/$Organism/$Strain/contigs.fasta)
		OutDir=assembly/merged_canu_spades/$Organism/$Strain
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/quickmerge
		qsub $ProgDir/sub_quickmerge.sh $PacBioAssembly $HybridAssembly $OutDir
	done
```

Remove contaminants and rename
```bash
	touch tmp.csv
		for Assembly in $(ls assembly/merged_canu_spades/F.oxysporum_fsp_pisi/FOP2/merged.fasta); do
		Organism=$(echo $Assembly | rev | cut -f3 -d '/' | rev)  
		Strain=$(echo $Assembly | rev | cut -f2 -d '/' | rev)
		OutDir=$(dirname $Assembly)
		mkdir -p $OutDir
		ProgDir=~/git_repos/tools/seq_tools/assemblers/assembly_qc/remove_contaminants
		$ProgDir/remove_contaminants.py --inp $Assembly --out $OutDir/"$Strain"_contigs_renamed.fasta --coord_file tmp.csv
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/assembly_qc/quast
		qsub $ProgDir/sub_quast.sh $Assembly $OutDir
	done
	rm tmp.csv
```


Merged assembly polished using Pilon
```bash
	for Assembly in $(ls assembly/merged_canu_spades/*/FOP2/FOP2_contigs_renamed.fasta); do
		Organism=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
		Strain=$(echo $Assembly | rev | cut -f2 -d '/' | rev)
		IlluminaDir=$(ls -d qc_dna/paired/$Organism/$Strain)
		TrimF1_Read=$(ls $IlluminaDir/F/FOP2_S1_L001_R1_001_trim.fq.gz);
		TrimR1_Read=$(ls $IlluminaDir/R/FOP2_S1_L001_R2_001_trim.fq.gz);
		OutDir=assembly/merged_canu_spades/$Organism/$Strain/polished
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/pilon
		qsub $ProgDir/sub_pilon.sh $Assembly $TrimF1_Read $TrimR1_Read $OutDir
	done
```



FOP5

Merging
```bash
	for PacBioAssembly in $(ls assembly/canu/F.oxysporum_fsp_pisi/FOP5_canu/polished/pilon.fasta); do
		Organism=$(echo $PacBioAssembly | rev | cut -f4 -d '/' | rev)
		Strain=FOP5
		HybridAssembly=$(ls assembly/spades_pacbio/$Organism/$Strain/contigs.fasta)
		OutDir=assembly/merged_canu_spades/$Organism/$Strain
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/quickmerge
		qsub $ProgDir/sub_quickmerge.sh $PacBioAssembly $HybridAssembly $OutDir
	done
```

Remove contaminants and rename
```bash
	touch tmp.csv
		for Assembly in $(ls assembly/merged_canu_spades/F.oxysporum_fsp_pisi/FOP5/merged.fasta); do
		Organism=$(echo $Assembly | rev | cut -f3 -d '/' | rev)  
		Strain=$(echo $Assembly | rev | cut -f2 -d '/' | rev)
		OutDir=$(dirname $Assembly)
		mkdir -p $OutDir
		ProgDir=~/git_repos/tools/seq_tools/assemblers/assembly_qc/remove_contaminants
		$ProgDir/remove_contaminants.py --inp $Assembly --out $OutDir/"$Strain"_contigs_renamed.fasta --coord_file tmp.csv
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/assembly_qc/quast
		qsub $ProgDir/sub_quast.sh $Assembly $OutDir
	done
	rm tmp.csv
```


Merged assembly polished using Pilon
```bash
	for Assembly in $(ls assembly/merged_canu_spades/*/FOP5/FOP5_contigs_renamed.fasta); do
		Organism=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
		Strain=$(echo $Assembly | rev | cut -f2 -d '/' | rev)
		IlluminaDir=$(ls -d qc_dna/paired/$Organism/$Strain)
		TrimF1_Read=$(ls $IlluminaDir/F/FOP5_S2_L001_R1_001_trim.fq.gz);
		TrimR1_Read=$(ls $IlluminaDir/R/FOP5_S2_L001_R2_001_trim.fq.gz);
		OutDir=assembly/merged_canu_spades/$Organism/$Strain/polished
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/pilon
		qsub $ProgDir/sub_pilon.sh $Assembly $TrimF1_Read $TrimR1_Read $OutDir
	done
```


# Assembly stats were collected using Quast    REPEAT

```bash
	ProgDir=/home/jenkis/git_repos/tools/seq_tools/assemblers/assembly_qc/quast
		for Assembly in $(ls assembly/merged_canu_spades/*/*/polished/pilon.fasta); do
		Strain=$(echo $Assembly | rev | cut -f3 -d '/' | rev)
		Organism=$(echo $Assembly | rev | cut -f4 -d '/' | rev)  
		OutDir=assembly/merged_canu_spades/$Organism/$Strain/polished
		qsub $ProgDir/sub_quast.sh $Assembly $OutDir
	done
```


## Repeat masking    REPEAT
Repeat masking of canu assemblies (polished)

```bash
	ProgDir=/home/jenkis/git_repos/tools/seq_tools/repeat_masking
		for Assembly in $(ls assembly/canu/*/*/polished/pilon.fasta); do
		qsub $ProgDir/rep_modeling.sh $Assembly
		qsub $ProgDir/transposonPSI.sh $Assembly
	done
```
