# summerInterns

# PacBio HiFi Read Assembly Pipeline

This pipeline converts PacBio BAM files to FASTQ, assembles them with **hifiasm**, and converts the output GFA to FASTA contig files.  
Adjust sample paths and variable names as needed.

---

## Sample BAM Input Files

- schVir — Schiffornis virescens  
  `/n/netscratch/edwards_lab/Lab/kelsielopez/suboscines/1_A01/hifi_reads/m84147_250529_184003_s1.hifi_reads.bc1015.bam`

- plaMys — Platyrinchus mystaceus  
  `/n/netscratch/edwards_lab/Lab/kelsielopez/suboscines/1_A01/hifi_reads/m84147_250529_184003_s1.hifi_reads.bc1017.bam`

- megPar — Megaxenops parnaguae  
  `/n/netscratch/edwards_lab/Lab/kelsielopez/suboscines/2_A01/hifi_reads/m84147_250529_204305_s2.hifi_reads.bc1016.bam`

- neoPal — Neopelma pallescens  
  `/n/netscratch/edwards_lab/Lab/kelsielopez/suboscines/2_A01/hifi_reads/m84147_250529_204305_s2.hifi_reads.bc1022.bam`


---

## 1. Convert BAM to FASTQ

**Install the tool:**

```bash
conda install -c bioconda pbtk
