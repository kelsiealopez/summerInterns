# summerInterns

# PacBio HiFi Read Assembly Pipeline (manual)

This pipeline converts PacBio BAM files to FASTQ, assembles them with **hifiasm**, and converts the output GFA to FASTA contig files.  
Adjust sample paths and variable names as needed.

---

## Useful Links

- [bam2fastq GitHub](https://github.com/PacificBiosciences/bam2fastx)
- [pbtk on Bioconda](https://anaconda.org/bioconda/pbtk)
- [hifiasm GitHub](https://github.com/chhylp123/hifiasm)
- [hifiasm documentation (FAQ)](https://hifiasm.readthedocs.io/en/latest/faq.html#how-do-i-get-contigs-in-fasta)

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

# test that it is working by running
hifiasm

# a menu with different options should come up
```

**Run assembly pipeline (Example for schVir)**
```bash
#!/bin/bash
#SBATCH -p shared # submit to shared partition because you can run jobs for a long time #https://docs.rc.fas.harvard.edu/kb/running-jobs/
#SBATCH -c 32
#SBATCH -t 2-00:00 # submit 2 days. I don't think it will take more than 12-24 hours but it is better to over request just in case
#SBATCH -o hifiasm_schVir_%j.out
#SBATCH -e hifiasm_schVir_%j.err 
#SBATCH --mem=150000 # maximum for shared partition is 184Gb so i requested 150 gb
#SBATCH --mail-type=END

# make sure to load python environment 

outdir="/n/netscratch/edwards_lab/Lab/kelsielopez/suboscines/schVir"
input_name="/n/netscratch/edwards_lab/Lab/kelsielopez/suboscines/1_A01/hifi_reads/m84147_250529_184003_s1.hifi_reads.bc1015"
sample_name="schVir"
mkdir -p ${outdir}
hifiasm -o ${outdir}/${sample_name} -t 32 ${input_name}.fastq.gz


```

## 3. Convert gfa to fa
```bash
#!/bin/bash
#SBATCH -p test # this shouldn't take too logn so you can submit to test partition. test partition is for quick jobs taht take less than 12 hours
#SBATCH -c 8
#SBATCH -t 0-03:00 # submit for 3 hours which is probably more than enough but still over requesting is better than under requesting and having your job time out 
#SBATCH -o gfa_to_fa_%j.out
#SBATCH -e gfa_to_fa_%j.err 
#SBATCH --mem=32000 # don't need too much memory
#SBATCH --mail-type=END

indir="/n/netscratch/edwards_lab/Lab/kelsielopez/suboscines/schVir"
sample_name="schVir"
awk '/^S/{print ">"\$2;print \$3}' \
  ${indir}/${sample_name}.bp.hap1.p_ctg.gfa \
  > ${indir}/${sample_name}.bp.hap1.p_ctg.fa

```


## 4. Run QUAST to test assembly quality


```bash
# First install quast https://github.com/ablab/quast

mkdir /n/netscratch/edwards_lab/Lab/aporrasvargas/suboscines/schVir/03_QC/00_quast

```

```bash
#!/bin/bash
#SBATCH -p test # doesn't take too long to run 
#SBATCH -c 8
#SBATCH -t 0-12:00
#SBATCH -o output_quast_schVir_%j.out
#SBATCH -e errors_quast_schVir_%j.err 
#SBATCH --mem=64G
#SBATCH --mail-type=END

indir="/n/netscratch/edwards_lab/Lab/aporrasvargas/suboscines/schVir/02_assembly/01_fa" # these are old directories and different files but you know what to do for the suboscines
outdir="/n/netscratch/edwards_lab/Lab/aporrasvargas/suboscines/schVir/03_QC/00_quast"
sample="schVir"

quast.py \
${indir}/${sample}.hap1.p_ctg.fa --large \ # set large because it is a big genome
${indir}/${sample}.hap2.p_ctg.fa --large \ # '\' means to continue the code onto the next line 
${indir}/${sample}.p_ctg.fa --large \
-o ${outdir} # specify the output directory where all the files are written to 

```



## 4. Run BUSCO to test assembly completeness


```bash
# First install busco

mkdir /n/netscratch/edwards_lab/Lab/aporrasvargas/suboscines/schVir/03_QC/01_busco

```

```bash
#!/bin/bash
#SBATCH -p test 
#SBATCH -c 8
#SBATCH -t 0-12:00
#SBATCH -o output_busco_schVir_%j.out
#SBATCH -e errors_busco_schVir_%j.err 
#SBATCH --mem=64G
#SBATCH --mail-type=END

indir="/n/netscratch/edwards_lab/Lab/aporrasvargas/suboscines/schVir/02_assembly/01_fa"
outdir="/n/netscratch/edwards_lab/Lab/aporrasvargas/suboscines/schVir/03_QC/01_busco"
sample="schVir"

# example code 
```



