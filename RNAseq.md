# RNAseq data- analysis and gene prediction of Minion genomes

## Renaming RNAseq data and creating softlinks

```bash
cat sample_names.txt | while read Line; do
Prefix=$(echo $Line | cut -d ' ' -f 1)
SampNm=$(echo ${Line} | cut -d ' ' -f 2)
SampNm=${SampNm/Sample:/}
StrainName=$(echo ${SampNm} | sed -r '/^.._(.*).$/ s//\1/')
echo ${StrainName} 
#cp /data/scratch/jenkis/RNAseq/F/${Prefix}_1.fastq.gz /data/scratch/jenkis/RNAseq/renamed/F/${Prefix}_${SampNm}_1.fastq.gz
#cp /data/scratch/jenkis/RNAseq/R/${Prefix}_2.fastq.gz /data/scratch/jenkis/RNAseq/renamed/R/${Prefix}_${SampNm}_2.fastq.gz
#mkdir /home/groups/harrisonlab/project_files/fusarium_ex_pea/raw_rna/paired/F.oxysporum_fsp_pisi/${StrainName}
#mkdir /home/groups/harrisonlab/project_files/fusarium_ex_pea/raw_rna/paired/F.oxysporum_fsp_pisi/${StrainName}/F/
#mkdir /home/groups/harrisonlab/project_files/fusarium_ex_pea/raw_rna/paired/F.oxysporum_fsp_pisi/${StrainName}/R/
ln -s -f /data/scratch/jenkis/RNAseq/F/${Prefix}_1.fastq.gz /home/groups/harrisonlab/project_files/fusarium_ex_pea/raw_rna/paired/F.oxysporum_fsp_pisi/${StrainName}/F/${Prefix}_${SampNm}_1.fastq.gz
ln -s -f /data/scratch/jenkis/RNAseq/R/${Prefix}_2.fastq.gz /home/groups/harrisonlab/project_files/fusarium_ex_pea/raw_rna/paired/F.oxysporum_fsp_pisi/${StrainName}/R/${Prefix}_${SampNm}_2.fastq.gz
done
```

## Fastqc was performed on RNAseq data (using softlinks from above)

```bash    
for FilePath in $(ls -d raw_rna/paired/F.oxysporum_fsp_pisi/*); do
		echo $FilePath
		FileNum=$(ls $FilePath/F/*.gz | wc -l)
		for num in $(seq 1 $FileNum); do
		FileF=$(ls $FilePath/F/*.gz | head -n $num | tail -n1)
		FileR=$(ls $FilePath/R/*.gz | head -n $num | tail -n1)
		echo $FileF
		echo $FileR
		IlluminaAdapters=/home/jenkis/git_repos/tools/seq_tools/ncbi_adapters.fa
		ProgDir=/home/jenkis/git_repos/tools/seq_tools/rna_qc
		qsub $ProgDir/rna_qc_fastq-mcf.sh $FileF $FileR $IlluminaAdapters RNA
	done
done
```  


## Cegma

Cegma was run on the Minion genomes for gene prediction before incorporating the RNAseq data to improve gene pred  

```bash
for Assembly in $(ls assembly/SMARTdenovo/*/*/nanopolish/*pilon_min_500bp_renamed.fasta); do
	ProgDir=/home/jenkis/git_repos/tools/gene_prediction/cegma
	qsub $ProgDir/sub_cegma.sh $Assembly dna
done
```




