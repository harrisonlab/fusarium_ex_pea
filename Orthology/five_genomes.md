#For a comparison between 3 Phytophthora clade 1 isolates (P.cac 10300, P.inf T30-4, P.par 310), a clade 2 isolate (P.cap LT1534) & a clade 7 isolate (P.soj 67593)


```bash
ProjDir=/home/groups/harrisonlab/project_files/fusarium_ex_pea
cd $ProjDir
IsolateAbrv=Fop1_Foc1_Foc2_Fon1_FpA8
WorkDir=analysis/orthology/orthomcl/$IsolateAbrv
mkdir -p $WorkDir
mkdir -p $WorkDir/formatted
mkdir -p $WorkDir/goodProteins
mkdir -p $WorkDir/badProteins  
```

##Format fasta files

###For F.oxysporum f.sp pisi PG18
```bash
	Taxon_code=Fop1
	Fasta_file=gene_pred/augustus/F.oxysporum_fsp_pisi/PG18/PG18_EMR_singlestrand_aug_out.aa
	Id_field=1
	orthomclAdjustFasta $Taxon_code $Fasta_file $Id_field
	mv "$Taxon_code".fasta $WorkDir/formatted/"$Taxon_code".fasta
```

###For F.oxysporum f.sp cepae FUS2 (pathogenic)
```bash
	Taxon_code=Foc1
	Fasta_file=../fusarium/gene_pred/augustus/F.oxysporum_fsp_cepae/Fus2/Fus2_augustus_preds.aa
	Id_field=1
	orthomclAdjustFasta $Taxon_code $Fasta_file $Id_field
	mv "$Taxon_code".fasta $WorkDir/formatted/"$Taxon_code".fasta
```

###For F.oxysporum f.sp cepae A28 (non-pathogenic)
```bash
	Taxon_code=Foc2
	Fasta_file=../fusarium/gene_pred/augustus/F.oxysporum_fsp_cepae/A28/A28_augustus_preds.aa
	Id_field=1
	orthomclAdjustFasta $Taxon_code $Fasta_file $Id_field
	mv "$Taxon_code".fasta $WorkDir/formatted/"$Taxon_code".fasta
```

###For F.oxysporum f.sp narcissi N139
```bash
Taxon_code=Fon1
	Fasta_file=../fusarium/gene_pred/augustus/F.oxysporum_fsp_narcissi/N139/N139_augustus_preds.aa
	Id_field=1
	orthomclAdjustFasta $Taxon_code $Fasta_file $Id_field
	mv "$Taxon_code".fasta $WorkDir/formatted/"$Taxon_code".fasta
```

###For F.proliferatum A8 
```bash
	Taxon_code=FpA8
	Fasta_file=../fusarium/gene_pred/augustus/F.proliferatum/A8/A8_augustus_preds.aa
	Id_field=1
	orthomclAdjustFasta $Taxon_code $Fasta_file $Id_field
	mv "$Taxon_code".fasta $WorkDir/formatted/"$Taxon_code".fasta
```


## Filter proteins into good and poor sets.

```bash
	Input_dir=$WorkDir/formatted
	Min_length=10
	Max_percent_stops=20
	Good_proteins_file=$WorkDir/goodProteins/goodProteins.fasta
	Poor_proteins_file=$WorkDir/badProteins/poorProteins.fasta
	orthomclFilterFasta $Input_dir $Min_length $Max_percent_stops $Good_proteins_file $Poor_proteins_file
```

## Perform an all-vs-all blast of the proteins

```bash
	BlastDB=$WorkDir/blastall/$IsolateAbrv.db

	makeblastdb -in $Good_proteins_file -dbtype prot -out $BlastDB
	BlastOut=$WorkDir/all-vs-all_results.tsv
	mkdir -p $WorkDir/splitfiles

	SplitDir=/home/armita/git_repos/emr_repos/tools/seq_tools/feature_annotation/signal_peptides
	$SplitDir/splitfile_500.py --inp_fasta $Good_proteins_file --out_dir $WorkDir/splitfiles --out_base goodProteins

	ProgDir=/home/armita/git_repos/emr_repos/scripts/phytophthora/pathogen/orthology  
	for File in $(find $WorkDir/splitfiles); do
	Jobs=$(qstat | grep 'blast_500' | grep 'qw' | wc -l)
	while [ $Jobs -gt 1 ]; do
	sleep 10
	printf "."
	Jobs=$(qstat | grep 'blast_500' | grep 'qw' | wc -l)
	done
	printf "\n"
	echo $File
	BlastOut=$(echo $File | sed 's/.fa/.tab/g')
	qsub $ProgDir/blast_500.sh $BlastDB $File $BlastOut
	done
```

RUN ALL OF THESE WHEN THE ABOVE IS DONE
Have to set the isolate abbreviations again if you have logged out. Should have remembered if use same screen

# Merge the all-vs-all blast results  
```bash  
  MergeHits="$IsolateAbrv"_blast.tab
  printf "" > $MergeHits
  for Num in $(ls $WorkDir/splitfiles/*.tab | rev | cut -f1 -d '_' | rev | sort -n); do
    File=$(ls $WorkDir/splitfiles/*_$Num)
    cat $File
  done > $MergeHits
```

## Perform ortholog identification

```bash
  ProgDir=~/git_repos/emr_repos/tools/pathogen/orthology/orthoMCL
  MergeHits="$IsolateAbrv"_blast.tab
  GoodProts=$WorkDir/goodProteins/goodProteins.fasta
  qsub $ProgDir/qsub_orthomcl.sh $MergeHits $GoodProts
```

## Plot venn diagrams:

```bash
  ProgDir=~/git_repos/emr_repos/tools/pathogen/orthology/venn_diagrams
  $ProgDir/ven_diag_5_way.R --inp $WorkDir/"$IsolateAbrv"_orthogroups.tab --out $WorkDir/"$IsolateAbrv"_orthogroups.pdf
```

Output was a pdf file of the venn diagram.

The following additional information was also provided. The format of the
following lines is as follows:

Isolate name (total number of orthogroups)
number of unique singleton genes
number of unique groups of inparalogs

```
  [1] "Pcac (8494)"
  [1] 586
  [1] 126
  [1] "Pcap (7393)"
  [1] 348
  [1] 59
  [1] "Pinf (8079)"
  [1] 601
  [1] 107
  [1] "Ppar (8687)"
  [1] 732
  [1] 95
  [1] "Psoj (7592)"
  [1] 642
  [1] 153
  NULL
```

# Downstream analysis

Particular orthogroups were analysed for expansion in isolates.

This section details the commands used and the results observed.


### P. cactotum unique gene families

The genes unique to P.cactorum were identified within the orthology analysis.

First variables were set:
```bash
  WorkDir=analysis/orthology/orthomcl/Pcac_Pinf_Ppar_Pcap_Psoj
  PcacUniqDir=$WorkDir/Pcac_unique
  Orthogroups=$WorkDir/Pcac_Pinf_Ppar_Pcap_Psoj_orthogroups.txt
  GoodProts=$WorkDir/goodProteins/goodProteins.fasta
  Braker_genes=gene_pred/braker/P.cactorum/10300/P.cactorum/augustus.aa
  Uniq_Pcac_groups=$PcacUniqDir/Pcac_uniq_orthogroups.txt
  mkdir -p $PcacUniqDir
```

Orthologroups only containing P.cactorum 10300 genes were extracted:

```bash
  cat $Orthogroups | grep -v 'Pinf' | grep -v 'Ppar' | grep -v 'Pcap' | grep -v 'Psoj' > $Uniq_Pcac_groups
  echo "The number of orthogroups unique to P. cactorum are:"
  cat $Uniq_Pcac_groups | wc -l
  echo "The following number genes are contained in these orthogorups:"
  cat $Uniq_Pcac_groups | grep -o 'Pcac|' | wc -l  
```

```
  The number of orthogroups unique to P. cactorum are:
  126
  The following number genes are contained in these orthogorups:
  347
```