##Assembly

    velveth
    velvetg

    velveth cpAssembly31 31 -shortPaired -fastq -separate cp_350PE_01Err.read1.fastq cp_350PE_01Err.read2.fastq 
    velvetg cpAssembly31

    velveth cpAssembly11 21 -shortPaired -fastq -separate cp_350PE_01Err.read1.fastq cp_350PE_01Err.read2.fastq 
    velvetg cpAssembly11

#R

    myStatsTable<-read.table("cpAssembly31/stats.txt",header=TRUE)
    Kmer<-31
    contigs<-rev(sort(myStatsTable$lgth+Kmer-1))
    n50<-contigs[cumsum(contigs) >= sum(contigs)/2][1]

    library(seqinr)

    #Read the assembly
    cp31<-read.fasta("cpAssembly31/contigs.fa")
    nContigs<-length(cp31)
    contigLength<-getLength(cp31)
    contigs<-rev(sort(contigLength))
    n50<-contigs[cumsum(contigs) >= sum(contigs)/2][1]
    sapply(cp31,GC)
    getName(cp31)
    getSequence(cp31)
    max(contigLength)
    Table<-function(x){table(x)}
    Table(cp31[1])

    #plot k-mer distribution in reads
    reads <- read.fasta("cpAssembly31/Sequences")
    Kmer<-function(x,kmer){count(x,kmer)}
    K2<-sapply(reads,Kmer,2)
    hist(K2)
    K4<-sapply(reads,Kmer,4)
    hist(K4)
