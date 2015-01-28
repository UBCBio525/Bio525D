#Functional annotation and the Gene Ontology

##Sequence classification

QUESTIONS: 

1. A common task in genomics is to determine the source of your sequence samples. Take a sample* of the sequence file and BLAST the sequences online at:
http://www.uniprot.org/blast/.

\* Hint: you can look at the first 3 sequences by typing: `head -21 seqs.faa`, and `tail -22 seqs.faa` will show you the last 3 sequences.

After analyzing the BLAST results, can you determine the source species your samples?

2. What were the evidence codes for the sequences you analyzed?
3. Were they manually curated or computationally predicted?
4. Are there GO terms and publication records for these sequences? Take note of this information, we'll compare the results of different annotation methods to the Uniprot annotations.
5. Did you notice anything odd about the types of BLAST hits?

##Mapping our BLAST hits to GO terms

First, we will get annotations for a small set of sequences using BLAST2GO: 

https://blast2go.com/blast2go-pro/download-b2g

The above link will take you to a page to download the Java web start version. This should work on a Mac or PC.

To begin the analysis,

1. Select "File -> Load Sequences" and select the sequence file: seqs.faa.
2. Select "Blast -> Run NCBI BLAST" and enter an email address
3. Select "Mapping -> Run GO-Mapping Step"
4. Select "Annotation -> InterProScan -> Run InterProScan online" and wait for the results.

Did you see any differences between the results for step 2 and 4 above?

##Mapping Pfam domains to GO terms

For this section we will use HMMER2GO.

Normally we would start with assemblies, or gene models. In that case, we would want to first find ORFs, translate those ORFs and assign protein matches to our sequences. This can be done with two commands using HMMER2GO, for example,

    hmmer2go getorf -i seqs.fasta -o seqs.faa
    hmmer2go run -i seqs.faa -d Pfam-A.hmm

The first command would find ORFs in a multifasta file and create a file of translated ORFs. The second command finds matches to Pfam-A from our sequences. We will pick up the analysis at this step since I already did the domain matching. Now, we will map GO terms to our protein matches.

    hmmer2go mapterms -i seqs_Pfam-A.tblout -o seqs_Pfam-A_GO.tsv --map

The last thing we would want to do is create a [GAF](http://geneontology.org/page/go-annotation-file-gaf-format-20) file for determining enrichment of terms. We need to go this for the whole genome. For example, 

    hmmer2go map2gaf -i genome_Pfam-A_GO_GOterm_mapping.tsv -o genome_Pfam-A_GO_GOterm_mapping.gaf -s 'genus species'

This has already been done since it can take ~20 minutes for a whole genome. The file is called "genome_proteins_Pfam-A_parsed_GOterm_mapping.gaf" and it will be used in the enrichment analysis below.
    
QUESTONS:

1. Did you find any differences in the number of sequences with GO terms between BLAST2GO and HMMER2GO, or the number of GO terms mapped?
2. Try to find the sequence you analyzed with BLAST in the first section in the results. Were the same GO terms associated with the gene from BLAST2GO, HMMER2GO, and Uniprot?
3. Find out the function of these genes. Paste the GO terms at: https://www.ebi.ac.uk/QuickGO
4. Anything interesting or unusual for your species?

The two methods are similar in some ways and the results should agree, though they really are quite different in terms of usage and you'll notice large differences in performance.

##Manually creating GO mapping files (optional)

To find enrichment of GO terms, we need several resources.

1. A background or "population" set of terms to test against. This will be the whole genome.
2. A "study" set of terms for testing, and this will be the subset file we have been using called seqs.faa.
3. We also need a file of term definitions, and the ontology for finding enrichment. These files will be obtained automatically by HMMER2GO but you will need to keep this in mind if you write your own scripts.

First, let's create the background and study set files. Run the `create_GO-mapping_files.sh` script to find out the usage. To run this script we will need to create two directories, one each for the populaiton and study sets, and we will give the name of an output directory and a species name to use in the analysis. For example,

    mkdir population study
    mv seqs_Pfam-A.tblout study
    mv genome_Pfam-A.tblout population
    bash create_GO-mapping_files.sh study population output 'genus species'

##Calculating GO term enrichment

For this section we will be using another program that you can run on your own machine. It is called Ontologizer, and it can be obtained from this link: 

http://compbio.charite.de/contao/index.php/ontologizer2.html

In Ontologizer, follow the process below from the main menu.

1. "New Project"
2. "Ontology -> upload *.obo file" 
    - Get with file with: `wget http://geneontology.org/ontology/go-basic.obo`
3. "Annotations -> upload *.gaf file"
4. When asked for the "population set" paste in the population IDs.
5. When asked for the "study set" paste in the study IDs.
6. Click "Finish" followed by "Ontologize"

NB: You must specify the PATH to the `dot` program. From the main menu, under "Window -> Preferences -> DOT command" and enter "/usr/bin/dot" in place of the default "dot" command.

##Advanced section

For those wanting programmatic access to Uniprot, Pfam, and QuickGo, see the resources below.

* http://www.uniprot.org/help/programmatic_access
* http://pfam.xfam.org/help#tabview=tab10
* https://www.ebi.ac.uk/QuickGO/WebServices.html
* For an introduction to Perl, see the [Perl Maven](http://perlmaven.com/beginner-perl-maven-video-course) site or the free [Modern Perl](http://modernperlbooks.com/books/modern_perl_2014/) book for a good introduction.
