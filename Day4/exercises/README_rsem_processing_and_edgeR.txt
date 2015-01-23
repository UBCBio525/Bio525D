
Steps for running RSEM:

see documentation here for more info:

http://deweylab.biostat.wisc.edu/rsem/

also, typing in "rsem-calculate-expression" or any of the other commands without any arguments will bring up a help screen. In all the RSEM commands below, I have put the syntax for the command after "##command", including terms that look like "<something>". When you see this, the "something" will tell you what should go in that argument, and the "<>" characters are there to indicate to you that it's an argument, and should not be included when you actually run it. There is also an example command, indicated by "##example"


1. Build the transcript-to-gene map:

##command:
extract-transcript-to-gene-map-from-trinity <fasta_reference> <output_map_name>

##example:
extract-transcript-to-gene-map-from-trinity Pine_reference_rnaseq_reduced.fa Pine_reference_rnaseq_reduced_map


2. Prepare the reference, so that RSEM can use it:

##command:
rsem-prepare-reference --transcript-to-gene-map <map_name_from_step1> <fasta_reference> <outputname>

##example:
rsem-prepare-reference --transcript-to-gene-map Pine_reference_rnaseq_reduced_map Pine_reference_rnaseq_reduced.fa Pine_reference_rnaseq_reduced_ref


3. Prepare a bowtie2 index of the fasta file with the same output_name as the reference output in step #2:

##command:
bowtie2-build -f <fasta_reference> <output_name>

##example:
bowtie2-build -f Pine_reference_rnaseq_reduced.fa Pine_reference_rnaseq_reduced_ref


4. Calculate expression. Previous versions of RSEM specified the "--no-polyA" flag to be used if the data have already been cleaned to remove polyA tails, but this is now the default, so poly-A tails are not added unless you specify. Substitute the directory where you have the .fq files for PATH_HERE (use "pwd" to list what directory you are in).


##command:
rsem-calculate-expression --bowtie2 --paired-end <fastq_R1> <fastq_R2> <name_of_prepared_ref_step1> <output_name>

##example:
rsem-calculate-expression --bowtie2 --paired-end PATH_HERE/PmdT_147_100k_R1.fq PATH_HERE/PmdT_147_100k_R2.fq Pine_reference_rnaseq_reduced_ref PmdT_147_rsem

Examine the output of RSEM, both the output to the screen and the files that it created. You should be able to see the percentage of reads that were aligned correctly, among other summary statistics. Use "ls" to discover what the output is called, and use "less" or "head" to see what the files look like.

Now rerun this command on the other two paired .fq libraries, generating three RSEM alignments.

5. To plot some quality statistics, run the following command, replacing the <NAME> with the appropriate filename prefix, without the .stat ending (and removing the <> characters):

##command:
rsem-plot-model <name_of_aligned_output_from_step4> outputname.pdf

##example:
rsem-plot-model PmdT_147_rsem PmdT_147_rsem.pdf


This pdf output contains the following plots:

RSPD: Read Start Position Distribution. x-axis is bin number, y-axis is the probability of each bin. RSPD can be used as an indicator of 3’ bias

Quality score vs. observed quality given a reference base: x-axis is Phred quality scores associated with data, y-axis is the “observed quality”, Phred quality scores learned by RSEM from the data. Q = -10log_10(P), where Q is Phred quality score and P is the probability of sequencing error for a particular base

Position vs. percentage sequencing error given a reference base: x-axis is position and y-axis is percentage sequencing error

Histogram of reads with different number of alignments: x-axis is the number of alignments a read has and y-axis is the number of such reads. The inf in x-axis means number of reads filtered due to too many alignments


6. To make a table out of the individual library expression files that you have created, use the custom perls script called "add_RSEM_data_to_table.pl". To run this, you will first have to create a list of the contig names that were in your reference, along with a list of the .genes.results files created by RSEM that you want to put together. The list of names in the reference will have to match the names that you see in the RSEM output files that end in ".genes.results". See if you can figure out how to do these operations using simple bash commands. If you can't figure it out, the commands to do so are listed at the bottom of this document. Then run the perl script:


##command:
perl add_RSEM_data_to_table.pl <list_to_add> <gene_names> <suffix>

##example:
perl add_RSEM_data_to_table.pl list_to_add.txt gene_names.txt _expression_table.txt

You can see that this outputs a table that is readable in R, with one row for each gene and one column for each individual, with the expected counts from the 5th column printed out. If you wish to modify what is printed to the file, it can be done by editing line 81.



####################################################################################
###### RNAseq analysis exercise
####################################################################################

There are two data files:
cold_hot_expression.txt
cold_hot_mwh_expression.txt

They have been created by using RSEM to align libraries to a lodgepole pine reference and represent a subset of the data published in Yeaman et al. (2014; New Phytologist). Individuals were grown in a common garden and then exposed to conditions that were either hot (H), cool (C), or mild and wet with a heat treatment (MWH; see paper for details). The reference dataset has been trimmed so that it is smaller and easier to work with for this workshop.

The script "process_hot_cold_expression.R" will show you some of the basic steps for analyzing expression data. For more details, the EdgeR user guide (included in the folder here) provides an excellent resource with well worked examples. Use these examples to play around with the data and try to answer the following questions:

1. How many genes are differentially expressed by treatment in the simple contrast of C vs H (using "cold_hot_expression.txt")? How does the choice of FDR cutoff or p-value affect this number? Can you think of a good way to decide on a cutoff? 

2. How many genes are differentially expressed in the three-way contrast (using "cold_hot_mwh_expression.txt")? Which treatment is driving differential expression here? How do you know?

3. Can you make contrasts between pairs of treatments (e.g. H vs. MWH)? See EdgeR manual for details, starting page 24.

4. How much does model fitting with common dispersion vs. tagwise dispersion affect the answers you get from the data? (think in terms of the number of DE genes, the evidence for a single gene, etc.)

5. How would you compare expression levels between two different genes? Is it appropriate to compare the raw expression counts? Can you get more appropriate data from RSEM? (hint = YES).






















##### STEPS for bash commands to prepare input files for add_RSEM_data_to_table.pl 

List all files with .genes.results in their name (you may want to delete some if you've made more copies than you should have during testing, or chain multiple "| grep" commands together)

ls | grep genes.results > list_to_add.txt


Find all lines that have ">" in them, which are the contig names. Then pass these to sed and strip off the ">" character, and save it to a file:

grep ">" Pine_reference_rnaseq_reduced.fa | sed 's/>//' > gene_names.txt






 