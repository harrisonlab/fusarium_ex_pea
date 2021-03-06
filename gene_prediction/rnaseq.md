#Gene prediction


##1) Data Organisation

Data was copied across from the raw sequence area of the cluster. RNAseq data was copied from the Fusarium area to my directory.

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
```

## Align RNAseq reads to the genome:

RNAseq data from FpC isolate Fus2 grown under different culture conditions were qc'd before aligning to the genome.

#1) QC

This is to trim all RNAseq data to trim adapters to get good quality data.

 ```bash
	for StrainPath in $(ls -d raw_rna/paired/*/*); do
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/rna_qc
		IlluminaAdapters=/home/jenkis/git_repos/tools/seq_tools/ncbi_adapters.fa
		ReadsF=$(ls $StrainPath/F/*.fastq*)
		ReadsR=$(ls $StrainPath/R/*.fastq*)
		echo $ReadsF
		echo $ReadsR
		qsub $ProgDir/rna_qc_fastq-mcf.sh $ReadsF $ReadsR $IlluminaAdapters RNA
	done
```


##Align RNAseq reads against genomes

Alignments of RNAseq reads were made against my sequenced genomes using tophat:

```bash
	for Genome in $(ls assembly/spades/*/*/filtered_contigs/*_500bp_renamed.fasta); do
		Strain=$(echo $Genome | rev | cut -d '/' -f3 | rev)
		Organism=$(echo $Genome | rev | cut -d '/' -f4 | rev)
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/RNAseq
		for RnaDir in $(ls -d qc_rna/paired/F.oxysporum_fsp_cepae/Fus2_*); do
			LibRNA=$(echo $RnaDir | rev | cut -d '/' -f1 | rev)
			FileF=$(ls $RnaDir/F/*.fq.gz)
	    FileR=$(ls $RnaDir/R/*.fq.gz)
	    OutDir=alignment/$Organism/$Strain/$LibRNA
	    qsub $ProgDir/tophat_alignment.sh $Genome $FileF $FileR $OutDir
	done
	done
```  

Can test this first by substituting the qsub with an echo. Can't run script above until have trimmed data back but you can test it with echo.

```bash
for Genome in $(ls assembly/spades/*/*/filtered_contigs/*_500bp_renamed.fasta); do
	Strain=$(echo $Genome | rev | cut -d '/' -f3 | rev)
	Organism=$(echo $Genome | rev | cut -d '/' -f4 | rev)
	ProgDir=/home/jenkis/git_repos/tools/seq_tools/RNAseq
	for RnaDir in $(ls -d raw_rna/paired/F.oxysporum_fsp_cepae/Fus2_*); do
		LibRNA=$(echo $RnaDir | rev | cut -d '/' -f1 | rev)
	FileF=$(ls $RnaDir/F/*.fastq.gz)
	FileR=$(ls $RnaDir/R/*.fastq.gz)
	OutDir=alignment/$Organism/$Strain/$LibRNA
	echo "$ProgDir/tophat_alignment.sh $Genome $FileF $FileR $OutDir"
done
done  
```

##Following alignment you need to merge alignment files into a single dataset

merge .bam files into a single alignment file

```bash
	for StrainDir in $(ls -d alignment/*/*); do
		Strain=$(echo $StrainDir | rev | cut -d '/' -f1 | rev)
		ls alignment/*/$Strain/*/accepted_hits.bam > bamlist.txt
		mkdir -p $StrainDir/merged
		bamtools merge -list bamlist.txt -out $StrainDir/merged/accepted_hits_merged.bam
  done
```

#Run Braker 1


```bash
  ProgDir=/home/jenkis/git_repos/tools/gene_prediction/braker1
  for Assembly in $(ls assembly/spades/*/*/filtered_contigs/contigs_min_500bp_renamed.fasta); do
    printf "\n"
    Strain=$(echo $Assembly| rev | cut -d '/' -f3 | rev)
    Organism=$(echo $Assembly | rev | cut -d '/' -f4 | rev)
    OutDir=gene_pred/braker/$Organism/$Strain
    AcceptedHits=alignment/$Organism/$Strain/merged/accepted_hits_merged.bam
    GeneModelName="$Organism"_"$Strain"_braker
    qsub $ProgDir/sub_braker.sh $Assembly $OutDir $AcceptedHits $GeneModelName
  done
```

# Extract gff and amino acid sequences

```bash
  for File in $(ls gene_pred/braker/*/*/*_braker/augustus.gff); do
    getAnnoFasta.pl $File
    OutDir=$(dirname $File)
    echo "##gff-version 3" > $OutDir/augustus_extracted.gff
    cat $File | grep -v '#' >> $OutDir/augustus_extracted.gff
  done
```
