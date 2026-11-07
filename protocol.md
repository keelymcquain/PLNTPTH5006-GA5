# RNA-seq analysis of _Culex pipiens_

- Author: Keely McQuain
- Date created: 2026-11-4
- Date last modified: 2026-11-4
- This was run at the Pitzer cluster of the Ohio Supercomputer Center (<https://www.osc.edu>)
- OSC working dir: /fs/ess/PAS3493/people/mcquain7/GA5
- GitHub repository: <???>

## Prerequisites

- **Input files**:
  - Paired-end RNA-seq reads are in `../garrigos/fastq`

- **Software**:
  - The scripts run programs using Apptainer containers,
    with links contained inside the scripts.
  - If running at OSC, no software installations are needed.
  - If you're not at OSC, you need to have Apptainer and Slurm installed.

- Run all code from the working dir listed above.
  You don't need to create the output dirs, because the scripts will do so.

- Each script writes a Slurm log file (`slurm-<script>-<jobID>.out`) to the working dir.
  After the jobs of a step have finished, check these logs for errors,
  then move them into a `logs/` dir within that step's output dir.

## Set up: Download the reference transcriptome FASTA file from NCBI

```bash
# Downloaded on <???>
URL=https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/016/801/865/GCF_016801865.2_TS_CPP_V2/GCF_016801865.2_TS_CPP_V2_rna.fna.gz
wget -P data/ref "$URL"
```

## Set up: Set file paths

```bash
# Inputs:
fastq_dir=../garrigos/fastq/
ref_transcripts=data/ref/

# Base output dir:
outdir=results
```

## Step 1: Read trimming with TrimGalore

```bash 
# Loop to run TrimGalore once per sample
for fastq_r1 in ../garrigos/fastq/*_R1.fastq.gz; do 
    fastq_r2="${fastq_r1/_R1/_R2}"
    sbatch scripts/trimgalore.sh "$fastq_r1" "$fastq_r2" results/trim_galore
done    
```

## Step 2: Creating a transcriptome index with Salmon

```bash
bash scripts/salmon_index.sh data/ref/GCF_016801865.2_TS_CPP_V2_rna.fna.gz results/salmon_index 
```

## Step 3: Pseudo-alignment and quantification with Salmon

```bash
for R1 in results/trim_galore/S*_R1.fastq.gz; do
    R2="${R1/_R1/_R2}"
    sbatch scripts/salmon_quant.sh "$R1" "$R2" results/salmon_index/ results/salmon_quant/
done    
```

## Step 4: Summarizing the results of all steps with MultiQC

<???>