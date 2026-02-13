# DNA SEQUENCING AND MAPPING READS

# checking location
pwd

cd ~/resources
ls

# the reference genome taken was of Cgatti 
cd ~/resources/Cgattii_DNAseq
ls

# IND107 was an isolate of Cgatti 
cd IND107
ls

# it will display 2 subsets or reads
# cleaning the seqs/fragments using fastp

cd ~/resources/workshops/ws2-alignments
ls

mkdir IND107 
cd IND107

fastp \
  --in1 ~/resources/Cgattii_DNAseq/IND107/IND107-subset_1.fq \
  --in2 ~/resources/Cgattii_DNAseq/IND107/IND107-subset_2.fq \
  --out1 IND107-subset_1_FASTP.fq \
  --out2 IND107-subset_2_FASTP.fq \
  --low_complexity_filter \
  2> IND107_fastp.txt



ls


# after cleaning modified clean fastp files would appear

# indexing the ref genome

bwa index ~/resources/reference_sequence/Cryp_gatt_R265.genome.fa


# aligning the fragments

bwa mem \
  ~/resources/reference_sequence/Cryp_gatt_R265.genome.fa \
  IND107-subset_1_FASTP.fq \
  IND107-subset_2_FASTP.fq \
  > IND107-bwa-mem.sam


# converting to bam from sam and indexing

samtools view -S -b -u IND107-bwa-mem.sam > IND107-bwa-mem.bam
samtools sort IND107-bwa-mem.bam -o IND107-bwa-mem.sorted.bam
samtools index IND107-bwa-mem.sorted.bam

# checking

samtools flagstat IND107-bwa-mem.sorted.bam

cd ~/resources/workshops/ws2-alignments/IND107
pwd


# samtools idxstats

samtools idxstats IND107-bwa-mem.sorted.bam > IND107-bwa-mem.sorted.bam.idxstats

# inspect it 

cat IND107-bwa-mem.sorted.bam.idxstats


# Launch R (in the terminal)

R 

# load directory and data


setwd("~/resources/workshops/ws2-alignments/IND107/")

getwd()


# reading the table and add column names

idxstats <- read.table("IND107-bwa-mem.sorted.bam.idxstats")

colnames(idxstats) <- c("chr","chrLength","mapped_reads","unmapped_reads")

# remove the final row

idxstats <- idxstats[-nrow(idxstats), ]

# final table

idxstats  

# correlation test

cor.test(idxstats$chrLength, idxstats$mapped_reads)

# text based plot

install.packages("txtplot")
require(txtplot)
txtplot(
  x = idxstats$chrLength,
  y = idxstats$mapped_reads,
  pch = gsub("scaffold3.","", idxstats$chr),
  xlab = "Chromosome length (bp)",
  ylab = "Number of mapped reads"
)


# Exiting R     
q()



# in ASCII

cd ~/resources/workshops/ws2-alignments/IND107

ASCIIGenome IND107-bwa-mem.sorted.bam \
~/resources/reference_sequence/Cryp_gatt_R265.annotation.gff3 \
-fa ~/resources/reference_sequence/Cryp_gatt_R265.genome.fa


# jump to the next gene

next

# to show genome

show genome











