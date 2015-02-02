###Variant calling
## call SNPs with samtools:

    samtools view -bS cp_350PE_01Err_bowtie2.sam > cp_350PE_01Err_bowtie2.bam # also for bwa
    samtools faidx cp.fasta #index fasta
    samtools fixmate cp_350PE_01Err_bowtie2.bam cp_350PE_01Err_bowtie2.fix.bam # # also for bwa
    samtools rmdup cp_350PE_01Err_bowtie2.fix.bam cp_350PE_01Err_bowtie2.fix.rmdup.bam # # also for bwa
    samtools sort cp_350PE_01Err_bowtie2.fix.rmdup.bam cp_350PE_01Err_bowtie2.fix.rmdup.sorted # # also for bwa
    samtools mpileup -uf cp.fasta cp_350PE_01Err_bowtie2.fix.rmdup.sorted.bam cp_350PE_01Err_bwa.fix.rmdup.sorted.bam | bcftools view -bvcg - > var.01Err.bcf 
    bcftools view var.01Err.bcf > var.01Err.vcf  

## check vcf

Open vcf file with command 'less' and search with '/string'

Questions:

1. What do the 3 values in the GL FORMAT tag correspond to?
2. What sort of event is at position 73251?
3. What is the total depth at position 80756?
4. What is the root mean squared mapping quality at position 18255?

##R

    library(VariantAnnotation)
    vcf01Err<-readVcf('var.01Err.vcf',"cp")
    vcf01Err
    header(vcf01Err)
    ref(vcf01Err)
    qual(vcf01Err)

#prepare SNP table

    SNP <-geno(vcf01Err)$GT
    SNP.table<-t(SNP)
    colnames(SNP.table)<-c(1:length(SNP[,1]))
    View(SNP.table)

#compare aligners

    GenoQ <-geno(vcf01Err)$GQ

#how many SNPs in each

    isSNV(vcf01Err)
    SNV<-vcf01Err[isSNV(vcf01Err)==TRUE]
    SNV


#how many pass filter of depth

    HighQ.bowtie<-GenoQ[,2][GenoQ[,2]>=80]
    HighQ.bwa<-GenoQ[,1][GenoQ[,1]>=80]

#compare

    hist(GenoQ[,2])
    hist(GenoQ[,1])
    mean(GenoQ[,1])
    mean(GenoQ[,2])
