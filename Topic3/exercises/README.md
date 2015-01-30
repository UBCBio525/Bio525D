##Alignments

#bowtie2

    bowtie2-build cp.fasta cp # build index for bowtie2
    time bowtie2 -x cp -1 cp_350PE_01Err.read1.fastq -2 cp_350PE_01Err.read2.fastq -S cp_350PE_01Err_bowtie2.sam

#bwa mem

    bwa index cp.fasta # build index for bwa
    time bwa mem cp.fasta cp_350PE_01Err.read1.fastq cp_350PE_01Err.read2.fastq > cp_350PE_01Err_bwa.sam

#take a look

    less cp_350PE_01Err_bwa.sam
    q

#samtools

    samtools view -bS cp_350PE_01Err_bowtie2.sam > cp_350PE_01Err_bowtie2.bam
    samtools sort cp_350PE_01Err_bowtie2.bam cp_350PE_01Err_bowtie2_sorted
    samtools flagstat cp_350PE_01Err_bowtie2_sorted.bam
    samtools index cp_350PE_01Err_bowtie2_sorted.bam
    samtools tview cp_350PE_01Err_bowtie2_sorted.bam

#R

    library(rbamtools)
    setwd('/path/to/dir')
    reader <- bamReader("cp_350PE_01Err_bowtie2_sorted.bam", idx=TRUE)
    getRefData(reader)

    align <- getNextAlign(reader)
    failedQC(align)
    pcrORopt_duplicate(align)
    name(align)
    flag(align)
    refID(align)
    position(align)
    mapQuality(align)
    cigarData(align)
    nCigar(align)
    mateRefID(align)

    count <- bamCountAll(reader, verbose=TRUE)
    count
    coords <- c(0, 0, 15000)
    range <- bamRange(reader, coords)
    countNucs(range)
    ncs <- nucStats(reader)
    ncs

    xlim <- c(1, 100000)
    coords <- c(0,xlim[1], xlim[2])
    range <- bamRange(reader, coords)
    ad <- alignDepth(range)
    mean(getDepth(ad))
    plotAlignDepth(ad)

    reader2fastq(reader, "out.fastq")

