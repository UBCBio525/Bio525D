
You should have two VCF files in the directory called "snps.gbs.vcf" and "snps.seqcap.vcf". These are in the GATK output format, which is similar to the mpileup VCF format, but has small differences. Entries are either "./." when there is no information there (akin to NA) or something that looks like this:

1/1:0,1:1:3:24,3,0

This format is ":" separated, and the entries mean:

GT:AD:DP:GQ:PL

Which are described in the header of the file (the part that has "#" in front of each line):

    ##FORMAT=<ID=AD,Number=.,Type=Integer,Description="Allelic depths for the ref and alt alleles in the order listed">
    ##FORMAT=<ID=DP,Number=1,Type=Integer,Description="Approximate read depth (reads with MQ=255 or with bad mates are filtered)">
    ##FORMAT=<ID=GQ,Number=1,Type=Integer,Description="Genotype Quality">
    ##FORMAT=<ID=GT,Number=1,Type=String,Description="Genotype">
    ##FORMAT=<ID=PL,Number=G,Type=Integer,Description="Normalized, Phred-scaled likelihoods for genotypes as defined in the VCF specification">

There are many ways of dealing with VCF files, and there are R-packages designed specifically for reading them in, as you used yesterday in VariantAnnotation. Usually, R gives the advantage of being comparatively user friendly, but at a cost of speed of processing. When dealing with large datasets, I usually prefer to extract information from the VCF and manipulate data using awk and perl scripts, and only import it into R for actual statistical analysis. Here, we will use the VariantAnnotation package to read and filter our VCF files and then make some preliminary statistical analyses to see what might be going on.

Open the file marked "genomescans.tbz" by typing:

    tar -xjvf genomescans.tbz

The data here are from GBS and Sequence capture sets from lodgepole pine. There is a separate file that indicates some population criteria (latitude, longitude, etc.). Because of the limitations of time, we will just analyze the data to look for patterns in heterozygosity (nucleotide diveristy) and signatures of reduced diversity in different contigs. There are a wealth of different programs that you can use to analyze your data, most of which require some annoying massaging to get your data into the right format. Here, we are aiming to learn what the data looks like and become familiar with its structure and how to play with it in R, rather than do an exhaustive study.

Using the R-script called "process_VCF_scans.R" as a reference, read in the data and try to answer the following questions. Wherever possible, try to do the code on your own, and if you get stuck, refer to the script for help:

- How many individuals are there in the sequence capture vs. GBS datasets? 

- How does filtering for depth and quality score affect the number of good genotypes that you have called?

- How many SNPs are there and how is this number affected by how you filter for # of individuals with good genotype calls and allele frequency?

- Calculate expected heterozygosity for each SNP and plot it for a sample contig, as shown in the R-script. What does this indicate about the coverage of your data? Can you add a line showing the depth of sequencing coverage for this contig? How would you calculate the average expected heterozygosity per base pair for this contig? Can you think of a good way to calculate whether this is an abnormally diverse/non-diverse contig (if you had more data! file sizes are constrained for this exercise).

- Can you calculate the expected heterozygosity for each individual?

- Read in the geographic information file. Does the expected heterozygosity correlate to the geographic location of the individual?

####

The last part of this exercise is to get a feeling for genome-scale data and some preliminary ways that it can be analyzed. The file "xtx_table.txt" contains the genomic contig, position, and XTX value for several hundred thousand SNPs in lodgepole pine. As mentioned in the lecture, XTX is an FST-like output of Bayenv that represents whether a given locus has a pattern of variation in allele frequency among populations that is substantially different from the genome-wide average. This could be an indicator of selection, and high values indicate a more extreme departure. These are Bayesian estimates, and are relative measures, unlike FST.

- If you extract the top 1% of SNPs by XTX value, how are they distributed among contigs? Are there more outliers per contig than expected at random? Can you make a simple permutation test of this? (hint: if you draw SNPs randomly from contigs, how many will be on the same contig?). Can you make improvements to this test? Should it be considered strong evidence that selection is operating? Why or why not?

- Which contig has the most outliers? Can you plot the XTX values along the contig? How might you test whether this contig is biologically interesting? What other information would you like to have to make this approach more rigourous?





