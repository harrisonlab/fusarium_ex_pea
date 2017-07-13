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


##Building a directory structure

```bash
	RawDatDir=/home/miseq_data/pacbio/2017/John_Clarkson_UWAR.JC.ENQ-1929
	ProjectDir=/home/groups/harrisonlab/project_files/fusarium_ex_pea
	mkdir -p $ProjectDir/raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP1
	mkdir -p $ProjectDir/raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP2
	mkdir -p $ProjectDir/raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP5
```	

##Sequence data was moved into appropriate directories

```bash
	RawDatDir=/home/miseq_data/pacbio/2017/John_Clarkson_UWAR.JC.ENQ-1929
	ProjectDir=/home/groups/harrisonlab/project_files/fusarium_ex_pea
	cp $RawDatDir/John_Clarkson_UWAR.JC.ENQ-1929_S1.tar.gz $ProjectDir/raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP1
	cp $RawDatDir/John_Clarkson_UWAR.JC.ENQ-1929_S1_raw_reads.tar.gz $ProjectDir/raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP1
	cp $RawDatDir/John_Clarkson_UWAR.JC.ENQ-1929_R_S2.tar.gz $ProjectDir/raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP2
	cp $RawDatDir/John_Clarkson_UWAR.JC.ENQ-1929_R_S3R1.tar.gz $ProjectDir/raw_dna/pacbio/F.oxysporum_fsp_pisi/FOP5	
```

	
	