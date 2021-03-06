Overview of ThermoAlign
================================================
ThermoAlign is a genome-aware oligonucleotide design algorithm embedded within a distributable tool designed for targeted resequencing. ThermoAlign's applications range from basic PCR primer pair design to the design of multiplexed primer pairs constituting amplicon tiling paths across a long segment (e.g. 50 kb) in the genome. Every possible primer in the target region is considered. Prior SNP and indel information can be included to facilitate the design of oligonucleotides across monomorphic sequences. 

Using a reference genome sequence, BLAST is used to perform genome-wide searches for sequences similar to each candidate primer. For each off-target hit, the melting temperature (Tm) is estimated. To obtain accurate estimates of the Tm, the local alignment of each BLAST hit is used as a seed to create full-length primer-template alignments&#8212;<i>thermoalignments</i>&#8212; from which the Tm is computed. Oligonucleotides with sufficiently greater melting temperatures than all of its predicted off-target binding sites are identified and reported. A directed graph analysis (shortest path algorithm) is used to identify the minimum number of primer pairs forming an amplicon tiling path providing the greatest coverage across a target region.

Licensing and access
================================================
* ThermoAlign is released under a GNU GPLv3 open source license. 
* The source code is available on GitHub: https://github.com/drmaize/ThermoAlign/tree/master/TA_codes
* No need to install the dependencies; Docker images can be accessed to run ThermoAlign on any platform (see: <a href="#runcase">runcase</a>)
* Run parameters are defined in a parameters.py file: https://github.com/drmaize/ThermoAlign/blob/master/TA_codes/parameters.py
* A shell script can be used to run all components: https://github.com/drmaize/ThermoAlign/blob/master/TA_codes/pipeline.sh
* Alternatively, each component of ThermoAlign can be run separately in the following order:
1) TRS.py; 2) UOD.py; 3) PSE.py; 4) PPS.py

<h1 id="runcase">
Introductory Run Case for a ThermoAlign Docker Image
</h1>
TA_1.0.0_s is a Docker image containing a small set of sample files that can be used to test ThermoAlign. First, you must install <a href="https://docs.docker.com/engine/installation/">Docker</a> on your machine. Once Docker is installed, follow commands 1-8 below to run ThermoAlign.

    # Command 1:
    docker run -t -i --name thermoalign_test drmaize/thermoalign:TA_1.0.0_s bash           

![alt tag](https://github.com/drmaize/ThermoAlign/blob/master/images/docker_screen_shot.png)

This will pull TA_1.0.0_s from docker hub, generate a new ThermoAlign container in your local machine and open the container where commands can be executed. Here we used the --name command to name the container "thermoalign_test", which is helpful for tracking your containers. If the image is already present in your local system and you run the same command, Docker will create a new container (but it won't let you do this if the name is identical). Re-entering an existing container is described in the section below called <a href="#dockerized">Going Further: ThermoAlign is Dockerized</a>.

    # Command 2: move to TA_codes directory
    cd TA_codes/
    
    # Command 3: perform one-time preprocessing of vcf files in "../sample_vcf" directory
    # This is used for the example to demonstrate the option of using a vcf file to filter primers at variant sites
    # Later on, when using your real data, skip this step if you do not use a vcf file  
    python vcf_conversion.py
    
Standard default parameters are preset, but users can modify the parameters.py file to adjust design parameters in each module of ThermoAlign. The Docker images include a vim text editor so that users may modify the parameters file. Have a look at the parameters file using the following command:

    # Command 4 (optional): the vim (or nano) editor can be used to modify design parameters
    # for vim users: type "i" to insert/modify values; use the "Esc" keyboard button followed by ":x" to save and quit.
    vim parameters.py
    
    # Alternatively, nano users may start editting the file directly after opening the file using nano. (nano parameters.py)
    
Once you've exited from vim/nano, ThermoAlign can be run from the TA_codes directory.
    
    # Command 5: run ThermoAlign
    ./pipeline.sh
    

![alt tag](https://github.com/drmaize/ThermoAlign/blob/master/images/docker_screen_shot_2.png)

    # Command 6: view what's inside of the TA_codes folder
    # This now includes a folder with the prefix "TA_" followed job id information (time stamp info).
    # The TA-pre-fixed folder contains all of the output from the ThermoAlign run.
    ls
    
Hopefully the run was a success! The output files from each modules are explained <a href="#output">here</a>. If you are having trouble or something seems wrong, please feel free to contact <a href="mailto:ffrancis@udel.edu">Felix Francis</a> or <a href="mailto:rjw@udel.edu">Randy Wisser</a>.

The next steps show you how to copy the ThermoAlign results from the Docker container to a folder on your machine. For this you will need the container id and the folder name of the ThermoAlign output. The container id is the id following "root@" in your terminal. For example, if your terminal shows "root@44a8a1fe2bff:", 44a8a1fe2bff is your container id. Command 6 showed us the name of the folder containing the ThermoAlign output.

    # Command 7: exit Docker
    exit

    # Command 8: copy output files from the container to your desired local directory
    # This command has the following structure; the name or container ID can be used
    docker cp <name|containerId>:/TA_codes/<resultfolder> /local/path/target
    
    # Here is an example of the actual command, which needs to be edited to reflect details on your machine. 
    docker cp thermoalign_test:/TA_codes/TA_2016-09-16T16_24_05_130340 /Users/ffx/Documents/ThermoAlign_results
    # replace "thermoalign_test" with either the unique name you used for --name or the docker container id on your machine; 
    # replace "TA_2016-09-16T16_24_05_130340" with the directory name of the output folder in the container;
    # replace "/Users/ffx/Documents/TA_results" with the local path where you want to copy the output to.

<h1 id="output">
Output Files
</h1>

All output files will be saved in a single directory, named with a timestamp from when the run was executed: TA_year-month-date-time (for example :TA_2016-09-13T09_05_00_545957). Each output file name from a given run will have the same timestamp. 

###  _The Target Region Selection (TRS) module:_

Output file with detailed summary statistics from the run:

>TA_2016-09-13T09_05_00_545957_run_summary.txt

Sequence in fasta format with masked variant sites:

>TA_2016-09-13T09_05_00_545957_3_33000_2001_VariantMasked.fasta

###  _The Unique Oligo Design (UOD) module:_

<i>.fasta</i> file of all possible primers (+ and - strands) for the given UOD paramters:

>TA_2016-09-13T09_05_00_545957_UOD_out1_1.fasta

<i>.txt</i> file of all possible primers (+ and - strands) for the given UOD paramters:

>TA_2016-09-13T09_05_00_545957_UOD_out1_2.txt

<i>.fasta</i> file of + strand primers:

>TA_2016-09-13T09_05_00_545957_UOD_out2_1.fasta

<i>.fasta</i> file of - strand primers:

>TA_2016-09-13T09_05_00_545957_UOD_out2_2.fasta

<i>.fasta</i> file of + strand primers, with primer location, length and Tm information:

>TA_2016-09-13T09_05_00_545957_UOD_out3_1.fasta

<i>.fasta</i> file of - strand primers, with primer location, length and Tm information:

>TA_2016-09-13T09_05_00_545957_UOD_out3_2.fasta

<i>.fasta</i> file of + and - strand primers:

>TA_2016-09-13T09_05_00_545957_UOD_out4_1.fasta

###  _The Primer Specificity Evaluation (PSE) module:_

<i>.csv</i> file with thermoalignment information for + strand primers:

>TA_2016-09-13T09_05_00_545957_PSE_out1_1.csv

<i>.csv</i> file with thermoalignment information for - strand primers

>TA_2016-09-13T09_05_00_545957_PSE_out2_1.csv

<i>.csv</i> file with the Max_misprime_Tm and primer feature information for + and - strand primers:

>TA_2016-09-13T09_05_00_545957_PSE_out3_1.csv

###  _The Primer Pair Selection (PPS) module:_

<i>.txt</i> file of all possible primer pairs for the user defined parameters and their relevant information:

>TA_2016-09-13T09_05_00_545957_primer_pairs_info.txt

Primer names, sequences and their Tm for ordering:

>TA_2016-09-13T09_05_00_545957_primer_pairs_order.txt

Non-overlapping primer pair combinations from minimal tiling paths. These are used to check multiplex compatibility:

>TA_2016-09-13T09_05_00_545957_multiplx_input_set1_1.txt
>TA_2016-09-13T09_05_00_545957_multiplx_input_set2_1.txt 

<i>.txt</i> file listing names of multiplex compatible groups

>TA_2016-09-13T09_05_00_545957_multiplex_groups_set1_1.txt, TA_2016-09-13T09_05_00_545957_multiplex_groups_set2_1.txt

<i>.bed</i> formatted files of the primers for further analysis and visualization:

>TA_2016-09-13T09_05_00_545957_bed_separate_tracks_selected_oligos.bed

<i>.txt</i> file with the list of primer pairs grouped as multiplex compatible sets. All primers in each set may be used in a single reaction:

>TA_2016-09-13T09_05_00_545957_multiplx_pooled_output.txt


<h1 id="dockerized">
Going Further: ThermoAlign is Dockerized
</h1>
<a href="https://www.docker.com/">Docker</a> is an efficient way to port ThermoAlign across systems and operating systems. If you stepped through the above use case you already have a good idea on how to use ThermoAlign. The following docker images are available for deployment.

* TA_1.0.0_s is a sample run version containing a small set of sample files that can be used to test ThermoAlign
* TA_1.0.0_d is a general distributable version which requires user supplied files (reference sequence and variant data)
* TA_1.0.0_Zm3 is a maize-ready version for the B73 v3 release (includes HapMap3 with B73 v3 coordinates)
* TA_1.0.0_Zm4 is a maize-ready version for the B73 v4 release (includes HapMap3 with B73 v4 coordinates)
* TA_1.0.0_Human_GRCh38 is a human-ready version for the GRCh38 release

TA_1.0.0_d is the version most users will probably want, which requires inputting of the <a href="#refgenome">reference genome</a> and (optionally) <a href="#variants">variant data</a>. This is described below.

###  _Opening an existing container_

Containers existing on your machine are in either an open or closed state. This can be determined with the following command:

    # the -a flag will list all (open and closed) containers and their names
    docker ps -a
    
If the container is in an open state (does not say "exited" in the status column), use the following command with the desired name or container id specified:

    docker exec -it <name|container_id> bash
    
If the container is in an exited status, use the following command with the desired name or container id specified:

    docker start <name|container_id>
    
    # followed by 
    
    docker exec -it <name|container_id> bash

A container may be deleted using the name or container ID:
    
    # if the container is open it needs to be closed first
    docker stop <name|container_id>
    # now it can be deleted
    docker rm <name|container_id>

Further details on Docker commands can be found at the following sites:
>https://docs.docker.com/engine/reference/commandline/

>https://sites.google.com/site/felixfranciersite/blogs/docker

###  _Running ThermoAlign natively_

For optimum performance with large and highly repetitive genomes such as maize, it may be better to run the source code natively, with each of the required dependencies installed on your local machine or cluster. See the end of this readme for a list of the dependencies required to run ThermoAlign natively.

Be aware that the available memory for running ThermoAlign should be greater than the combined size of the whole genome and variant files. The number of processors requested (ppn) should match or not exceed the number of threads indicated for BLAST in the parameters.py file.

<h1 id="refgenome">
Inputting a custom reference genome for TA_1.0.0_d
</h1>

The reference sequence pseudomolecule of each chromosome needs to be separated into separate files, named as follows: 
>chr1.fasta, chr2.fasta, etc.

For each sequence, the fasta header should have the following format:
> &#62;chromosome:assembly_ver:chr#:start_pos:end_pos:#sequences

For example:
> &#62;chromosome:AGPv3:13:1:7261561:1

<h1 id="variants">
Inputting prior variant info for TA_1.0.0_d
</h1>

A <i>.vcf</i> file (v4.0 or v4.1) based on the same coordinate system as the reference genome sequence may be optionally used for polymorphism-aware primer design. This will result in the exclusion of candidate primers that co-localize with variant sites.

The <i>.vcf</i> file needs to be provided as separate files for each chromosome, named as follows:
>chr1.vcf, chr2.vcf, etc.


<h1 id="add files">
Adding external files to your container
</h1>

A file may be copied from your local directory to a docker container by:
    
    docker cp /yourlocaldirectory/filename.txt <name|container_id>:/filename.txt

Advanced Use
================================================
### _Dependencies for ThermoAlign_ 

ThermoAlign can be executed via Python on a local cluster independent of Docker. This requires installation of the following components. The versions listed are those distributed in the Docker container. Other versions of these components have not been tested.
* Linux/Unix
* <a href="http://python.org/">Python 2.7</a>
* <a href="http://www.numpy.org/">NumPy 1.9.2</a>
* <a href="http://pandas.pydata.org/">pandas 0.18.1</a>
* <a href="http://cython.org/">Cython 0.24</a>
* <a href="https://pypi.python.org/pypi/primer3-py">primer3-py 0.5.1</a>
* <a href="http://blast.ncbi.nlm.nih.gov/Blast.cgi">BLAST 2.2.31+</a>
* <a href="http://bioinfo.ut.ee/download/dl.php?file=24">MultiPLX 2.0</a>
* <a href="https://networkx.github.io/">networkx 1.11</a>

### _Running on a cluster with qsub_    

Docker containers may be run in interactive mode:
    
    qsub -I -V -N intrctv -l nodes=1:ppn=5
    
and then,
    
    docker run -t -i drmaize/thermoalign:TA_1.0.0_d bash

<br>



### _UPDATES_ 
Santalucia_NN_Tm.py:	right_tmm definition modified (Updated: version-1.05 06/29/2017)


                                        ##### END OF README #####
