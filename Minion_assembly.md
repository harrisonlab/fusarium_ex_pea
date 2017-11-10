# Fusarium oxysporum f.sp. pisi (Minion and Miseq assemblies)
## Commands used for the analysis of F.oxysporum f.sp pisi sequences FOP1 EMR, F81 and R2



Data was basecalled again using Albacore 2.02 on the minion server:

```bash
Organism="F.oxysporum_fsp_pisi"
for Strain in "FOP1-EMR" "F81" "R2"; do
  echo $Strain
  OutDir=/home/groups/harrisonlab/project_files/fusarium_ex_pea/raw_dna/minion/$Organism/$Strain
  mkdir -p $OutDir
done

ssh nanopore@nanopore
mkdir FoP_warwick
cd FoP_warwick
screen -a
# Oxford nanopore 2017-07-11
Organism="F.oxysporum_fsp_pisi"
Strain="FOP1-EMR"
Date="2017-07-11"
FlowCell="FLO-MIN106"
Kit="SQK-LSK108"
RawDatDir=/data/seq_data/minion/2017/20170711_1532_FOP1-EMR/fast5/pass
OutDir=/home/groups/harrisonlab/project_files/fusarium_ex_pea/raw_dna/minion/$Organism/$Strain

mkdir -p ~/FoP_warwick/$Date
cd ~/FoP_warwick/$Date
~/.local/bin/read_fast5_basecaller.py \
  --flowcell $FlowCell \
  --kit $Kit \
  --input $RawDatDir \
  --recursive \
  --worker_threads 24 \
  --save_path "$Organism"_"$Strain"_"$Date" \
  --output_format fastq,fast5 \
  --reads_per_fastq_batch 4000
  cat "$Organism"_"$Strain"_"$Date"/workspace/pass/*.fastq | gzip -cf > "$Organism"_"$Strain"_"$Date"_albacore_v2.02.fastq.gz
  scp "$Organism"_"$Strain"_"$Date"_albacore_v2.02.fastq.gz armita@192.168.1.200:$OutDir/.
  tar -cz -f "$Organism"_"$Strain"_"$Date".tar.gz "$Organism"_"$Strain"_"$Date"
  mkdir -p $OutDir
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

  mkdir -p ~/FoP_warwick/$Date
  cd ~/FoP_warwick/$Date
  ~/.local/bin/read_fast5_basecaller.py \
    --flowcell $FlowCell \
    --kit $Kit \
    --input $RawDatDir \
    --recursive \
    --worker_threads 24 \
    --save_path "$Organism"_"$Strain"_"$Date" \
    --output_format fastq,fast5 \
    --reads_per_fastq_batch 4000
    cat "$Organism"_"$Strain"_"$Date"/workspace/pass/*.fastq | gzip -cf > "$Organism"_"$Strain"_"$Date"_albacore_v2.02.fastq.gz
    scp "$Organism"_"$Strain"_"$Date"_albacore_v2.02.fastq.gz armita@192.168.1.200:$OutDir/.
    tar -cz -f "$Organism"_"$Strain"_"$Date".tar.gz "$Organism"_"$Strain"_"$Date"
    mkdir -p $OutDir
    scp "$Organism"_"$Strain"_"$Date".tar.gz armita@192.168.1.200:$OutDir/.

    # Oxford nanopore FoP R2
    Organism="F.oxysporum_fsp_pisi"
    Strain="R2"
    Date="2017-05-17"
    FlowCell="FLO-MIN107"
    Kit="SQK-LSK108"
    RawDatDir=/data/scratch/nanopore_tmp_data/Fop/R2/albacore_output_1.2.4/workspace
    OutDir=/home/groups/harrisonlab/project_files/fusarium_ex_pea/raw_dna/minion/$Organism/$Strain

    mkdir -p ~/FoP_warwick/$Date
    cd ~/FoP_warwick/$Date
    ~/.local/bin/read_fast5_basecaller.py \
      --flowcell $FlowCell \
      --kit $Kit \
      --input $RawDatDir \
      --recursive \
      --worker_threads 24 \
      --save_path "$Organism"_"$Strain"_"$Date" \
      --output_format fastq,fast5 \
      --reads_per_fastq_batch 4000
      cat "$Organism"_"$Strain"_"$Date"/workspace/pass/*.fastq | gzip -cf > "$Organism"_"$Strain"_"$Date"_albacore_v2.02.fastq.gz
      scp "$Organism"_"$Strain"_"$Date"_albacore_v2.02.fastq.gz armita@192.168.1.200:$OutDir/.
      tar -cz -f "$Organism"_"$Strain"_"$Date".tar.gz "$Organism"_"$Strain"_"$Date"
      mkdir -p $OutDir
      scp "$Organism"_"$Strain"_"$Date".tar.gz armita@192.168.1.200:$OutDir/.
```
