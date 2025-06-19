# summerInterns

# PacBio HiFi Read Assembly Pipeline (manual)

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
```

**Convert (Example for schVir)**
```bash
input_name="/n/netscratch/edwards_lab/Lab/kelsielopez/suboscines/1_A01/hifi_reads/m84147_250529_184003_s1.hifi_reads.bc1015"
bam2fastq -o ${input_name} ${input_name}.bam
# Produces: ${input_name}.fastq.gz
```


## 2. Run hifiasm Assembly

**Install Hifiasm:**
```bash
conda install -c bioconda hifiasm
```

**Run assembly pipeline (Example for schVir)**
```bash
outdir="/n/netscratch/edwards_lab/Lab/kelsielopez/suboscines/schVir"
sample_name="schVir"
mkdir -p ${outdir}
hifiasm -o ${outdir}/${sample_name} -t 32 ${input_name}.fastq.gz
```

## 3. Convert gfa to fa
```bash
indir="/n/netscratch/edwards_lab/Lab/kelsielopez/suboscines/schVir"
sample_name="schVir"
awk '/^S/{print ">"\$2;print \$3}' \
  ${indir}/${sample_name}.bp.hap1.p_ctg.gfa \
  > ${indir}/${sample_name}.bp.hap1.p_ctg.fa
```






