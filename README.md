# GA5 answers - Keely McQuain

------------------------------------------------------------------------

## Part A: Your project dir, Git repo, and protocol

### 1: Fill in the top of the protocol

### 2: Markdown formatting

### 3: Create subdirs, and copy the scripts

```bash
# Your mkdir command:
mkdir -p data/ref scripts results
```

### 4: Initialize a Git repository, and commit your first files

### 5: Download the reference transcriptome

Code to count the number of transcripts:

```bash
# Your command:
zcat data/ref/GCF_016801865.2_TS_CPP_V2_rna.fna.gz | grep -c ">"
```

The output of the command was:

```
28624
```

*Answer*: The number of transcripts in the file: 28,624 

------------------------------------------------------------------------

## Part B: Read trimming

### 6: Run TrimGalore

*Answer*: Whether all batch jobs succeeded, and how you checked that
(and any problems you encountered, and how you solved them):

All batch jobs did succeed. I first used to grep to determine if any jobs did not successfully finish and then I checked the number of HTML files to make sure it was 44. 

```bash
# My commands
grep -c "Successfully finished" slurm-trimgalore*.out
ls results/trim_galore/*html | wc -l
```

------------------------------------------------------------------------

## Part C: Transcript quantification with Salmon

### 7: Create the Salmon index

Code to check the size of the index dir:

```bash
# Your command:
du -sh results/salmon_index
```

The output of the command was:

```
1.1G    results/salmon_index
```

*Answer*: How long it took to create the index, and how you found out: 

It took about a minute to create the index. I looked at the output to see what time it started (8:15:28 PM) and what time it finished (08:16:30 PM). 

*Answer*: The size of the index dir: 1.1 G

### 8: Run Salmon for all samples

### 9: The transcripts with the most reads

*Answer*: What the `NumReads` column contains:

The estimated number of reads originated from each transcript.

```bash
# Your command:
cut -f 1,5 results/salmon_quant/S63/quant.sf | sort -k2,2 -n -r | head -n 3
```

The output of the command was:

```
XM_039591534.2  24004.889
XM_039573735.1  8663.254
XM_039573733.1  4774.749
```

### 10: The gene of the top transcript

```bash
# Your command:
zcat data/ref/GCF_016801865.2_TS_CPP_V2_rna.fna.gz | grep 'XM_039591534.2' 
```

The output of the command was:

```
>XM_039591534.2 PREDICTED: Culex pipiens pallens vitellogenin-A1-like (LOC120426760), mRNA
```

*Answer*: The gene that the top transcript belongs to:

Culex pipiens pallens vitellogenin-A1-like (LOC120426760)

### 11: Mapping rates

```bash
# Your command:
grep 'mapping rate' results/salmon_quant/S*/logs/salmon_quant.log
```

The output of the command was:

```
results/salmon_quant/S63/logs/salmon_quant.log:mapping rate: 82.4560%
results/salmon_quant/S64/logs/salmon_quant.log:mapping rate: 81.9152%
results/salmon_quant/S65/logs/salmon_quant.log:mapping rate: 79.1055%
results/salmon_quant/S66/logs/salmon_quant.log:mapping rate: 81.9350%
results/salmon_quant/S67/logs/salmon_quant.log:mapping rate: 83.0459%
results/salmon_quant/S68/logs/salmon_quant.log:mapping rate: 78.8070%
results/salmon_quant/S69/logs/salmon_quant.log:mapping rate: 82.1824%
results/salmon_quant/S70/logs/salmon_quant.log:mapping rate: 82.3060%
results/salmon_quant/S71/logs/salmon_quant.log:mapping rate: 79.9047%
results/salmon_quant/S74/logs/salmon_quant.log:mapping rate: 78.2753%
results/salmon_quant/S75/logs/salmon_quant.log:mapping rate: 80.3915%
results/salmon_quant/S76/logs/salmon_quant.log:mapping rate: 82.3396%
results/salmon_quant/S77/logs/salmon_quant.log:mapping rate: 82.3281%
results/salmon_quant/S78/logs/salmon_quant.log:mapping rate: 84.2490%
results/salmon_quant/S79/logs/salmon_quant.log:mapping rate: 80.4061%
results/salmon_quant/S80/logs/salmon_quant.log:mapping rate: 82.1721%
results/salmon_quant/S81/logs/salmon_quant.log:mapping rate: 83.6795%
results/salmon_quant/S82/logs/salmon_quant.log:mapping rate: 78.7291%
results/salmon_quant/S83/logs/salmon_quant.log:mapping rate: 76.1504%
results/salmon_quant/S84/logs/salmon_quant.log:mapping rate: 83.2207%
results/salmon_quant/S85/logs/salmon_quant.log:mapping rate: 76.9540%
results/salmon_quant/S86/logs/salmon_quant.log:mapping rate: 83.0532%
```

*Answer*: The sample with the lowest mapping rate: S83

------------------------------------------------------------------------

## Part D: Summarizing all steps with MultiQC

### 12: Update your MultiQC script

### 13: Run MultiQC

*Answer*: For each program, the number of reports that MultiQC found,
and why it found this number:

Salmon has 1 report, TrimGalore has 4 reports, and FastQC has 12 reports. 


### 14: The General Statistics table

*Answer*: The sample with the lowest Salmon mapping rate,
and whether this matches your answer to question 11:

The sample with the lowest mapping rate is S83, which does match my answer to question 11. 

### 15: Adapter lengths

*Answer*: The most common adapter lengths, whether this surprised you,
and why you think these lengths are the most common:

The most common adapter lengths are 1-4 bp. This did not surprise me because adapters are several types of short sequences that are added to ends of the DNA fragments.   

### 16: Ask Claude about the MultiQC report

*Answer*: What you asked, and what you learned from Claude's answer (in your own words):

I asked Claude why are the top overrepresented sequences and why they matter. I learned that if a library has a single sequence making up a large portion of the reads then it usually means that something went wrong, such as adapter contamination. However, the overrepresented sequences in this report did not make up a very large portion of all of the reads. 

------------------------------------------------------------------------

## Part E: Improving the workflow with genAI

### 17: Claude's review of your protocol and scripts

*Answer*: At least three of Claude's suggestions,
and whether you agree or disagree with each one, and why:

1. Add a line in protocol.md for each step: "Wait until all Step N jobs show COMPLETED in squeue/sacct before starting Step N+1." This is the single most common beginner mistake (submitting dependent jobs too early).

I agree with this one as I think it makes it more reproducible since each of the subsequent commands rely on the output of the one that comes before it. 

2. Document the sample naming convention once, near the top of protocol.md (e.g., "Samples are named S<number>_R1.fastq.gz / S<number>_R2.fastq.gz"), so the S* glob in Step 3 isn't a mystery.

I agree with this one as it will help others understand what the S was used for in the command, but I could also just remove the "S". 

3. Fix the multiqc.sh usage comment to say sbatch, not bash, matching how it's actually invoked.

I agree with this one since we did add a line to submit it as a batch job. I did not realize that the usage line used bash instead of sbatch.

### 18: Address a suggestion

*Answer*: Which suggestion(s) you addressed, and what you changed:

I addressed suggestions 2 and 3 because I felt that those were more important for enhancing reproducibility. For suggestion 2, I changed bash to sbatch in the usage line. For suggestion 3, instead of including a sample naming convention, I just removed the "S" from the command code. 
------------------------------------------------------------------------

## Part F: Publish your repo on GitHub

### 19: Create a GitHub repository and push

### 20: Get a (test) DOI with Zenodo

*Answer*: Your Zenodo Sandbox DOI: [![DOI](https://sandbox.zenodo.org/badge/1450660429.svg)](https://handle.test.datacite.org/10.5072/zenodo.637491)
