# Step by step instructions to benchmark baseline (bwa-mem) and OpenOmics BWA-MEM (bwa-mem2) on c5.24xlarge and m6i.16xlarge instances of AWS
## Step 1: Download datasets
Download reference genome: Homo_sapiens_assembly38.fasta
```sh
wget https://storage.googleapis.com/genomics-public-data/resources/broad/hg38/v0/Homo_sapiens_assembly38.fasta
```

Download dataset #1: ERR194147 (Paired-End) from 
s3://sra-pub-run-odp/sra/ERR194147/ERR194147

Download dataset #2: ERR1955529 (Paired-End) from 
s3://sra-pub-run-odp/sra/ERR1955529/ERR1955529

Download dataset #3: ERR3239276 (Paired-End) from 
s3://sra-pub-run-odp/sra/ERR3239276/ERR3239276

## Step 2: Download and compile baseline (BWA v0.7.17) and create index
```sh
curl -L https://github.com/lh3/bwa/releases/download/v0.7.17/bwa-0.7.17.tar.bz2 | tar jxf -
cd bwa-0.7.17 && make
./bwa index hs_asm38/Homo_sapiens_assembly38.fasta
cd ..
```

## Step 3: Download OpenOmics BWA-MEM (BWA-MEM2 v2.2.1) and create index
```sh
curl -L https://github.com/bwa-mem2/bwa-mem2/releases/download/v2.2.1/bwa-mem2-2.2.1_x64-linux.tar.bz2 | tar jxf -
cd bwa-mem2-2.2.1_x86-linux && ./bwa-mem2 index hs_asm38/Homo_sapiens_assembly38.fasta
cd ..
```

## Step 4: Running baseline and OpenOmics BWA-MEM
The c5.24xlarge instance has 48 cores. The memory available on the instance only allows for using 1 thread per core.
On the other hand, the m6i.16xlarge instance has 32 cores. The memory available on the instance allows for using both the threads on each core.

### Run baseline BWA-MEM
```sh
cd bwa-0.7.17
```
Sample commands shown below:\
For c5.24xlarge:
```sh
./bwa mem -t 48 hs_asm38/Homo_sapiens_assembly38.fasta ERR194147_1.fastq ERR194147_2.fastq > ERR194147.out.sam
```
For m6i.16xlarge:
```sh
./bwa mem -t 64 hs_asm38/Homo_sapiens_assembly38.fasta ERR194147_1.fastq ERR194147_2.fastq > ERR194147.out.sam
```

### Run OpenOmics BWA-MEM
```sh
cd ../bwa-mem2-2.2.1_x86-linux
```
Sample commands shown below:\
For c5.24xlarge:
```sh
./bwa-mem2 mem -t 48 hs_asm38/Homo_sapiens_assembly38.fasta ERR194147_1.fastq ERR194147_2.fastq > ERR194147.out.sam
```
For m6i.16xlarge:
```sh
./bwa-mem2 mem -t 64 hs_asm38/Homo_sapiens_assembly38.fasta ERR194147_1.fastq ERR194147_2.fastq > ERR194147.out.sam
```
