Splicing pipeline
--------------------------------------

The pipeline contains three major analysis steps: analyzing the order of
splicing per sample, looking for recursive splicing events per sample and
summarizing predicted recursive splicing events for all analyzed sample (it is
recommended to use more samples for higher reliability). The first two steps are
performed individually on each sample and the last step looks at the overlap
in all samples. However, the analysis can be run on one sample as well.

All scripts and commands are called from a single `run.sh` script. You have to
create it yourself from `run_template.sh` (please go to the [USAGE](https://github.com/pulyakhina/splicing_analysis_pipeline/blob/master/README.md#usage)
section for more details). Before running `run.sh` you have to specify the
locations of the scripts and the input files within it (please check the
[USAGE](https://github.com/pulyakhina/splicing_analysis_pipeline/blob/master/README.md#usage)
section for more details).

The pipeline is designed to analyze captured RNA-Seq data, however, in principle
it could be run on any RNA-Seq dataset. Please keep in mind that coverage is
essential and especially the coverage of introns will largely influence the
reliability of the predictions.

### Requirements

You have to install the following packages:

1. [samtools](http://samtools.sourceforge.net/)
2. [piletools](https://pypi.python.org/pypi/piletools)
3. [wiggelen](https://pypi.python.org/pypi/wiggelen)
4. [R](http://www.r-project.org/)

You also need to have the following [scripts] (https://git.lumc.nl/i.pulyakhina/pipeline_paper/blob/master/Source/Scripts.tar.gz)
on your computer: download the
archive containing the scripts and extract the scripts to your folder of
preference:

    tar xzf -C folder/of/preference scripts.tar.gz

### Input files

You can first have a look at the [example](LINK)
input files and use them in your test
run of the pipeline. Download the archive with the example text data files and
extract the files to yur folder of preference:

    tar xzf -C folder/of/preference data.tar.gz

You will have to specify the full path both to the scripts and to the files when
running the pipeline. You do not have to do it manually, if you placed all
scripts in one location and all data files in one location, you can run the
following commands on `run_template.sh` to create a new `run.sh` script that
will contain full pathes to your data files and scripts. The only thing that
you still need to specify manually is the full path to your reference sequence:

    cat run_template.sh | sed 's/PATH_TO_SCRIPTS/path_to_the_folder_with_scripts/g' | sed 's/PATH_TO_DATA/path_to_the_folder_with_data/g' > run.sh
    

If you want to start directly with the analysis of your own data, or you want to
know more about the exmaple data, here is a brief explanation of every file:

- `example_annotation.bed`: an annotation file of your gene of interest in a [GTF](http://www.ensembl.org/info/website/upload/gff.html) format
- `example_alignment1.bam`:  a bam file of sample 1 containing reads mapped to the region of interest
- `example_alignment2.bam`:  a bam file of sample 2 containing reads mapped to the region of interest
- `example_alignment1.bam.bai`: an index of `example_alignment1.bam`
- `example_alignment2.bam.bai`: an index of `example_alignment2.bam`
- `example_region.bed`: a bed file containing regions to be excluded (using [bedtools](http://bedtools.readthedocs.org/en/latest/)
you can exclude/include certain regions, such as promoters, pseudogenes, UTRs, etc.

### Usage

In order to run the pipeline you need to execute `run.sh`, however, some
pre-processing steps are required.

You have to specify the full pathes to the *SCRIPTS* that you just downloaded
to your folder of preference.

Please make sure that you sepcify the full path:

- `CLASSIFICATION`="/folder/of/preference/class_plus.pl"
- `MAPPED_READS`="/folder/of/preference/mapped_reads_from_sam_file.pl"
- `SSI_SCRIPT`="/folder/of/preference/ssi.pl"
- `ZERO_TO_WIG`="/folder/of/preference/add_zero_to_wig.pl"
- `AVERAGE_WIG_COV`="/folder/of/preference/wig_average_exonic_coverage.pl"
- `WIG_COV_CHANGE`="/folder/of/preference/wig_change_cov.pl"
- `R_PROCESS`="/folder/of/preference/process_SSI.R"
- `MED_COV_PROBES`="/folder/of/preference/med_cov_per_region.pl"
- `SAM_READS_FROM_REGION`="/folder/of/preference/get_sam_reads_within_region.pl"
- `GET_EXEX_READS_PER_INTRON`="/folder/of/preference/get_exex_i_PER_INTRON_reads_from_sam.pl"
- `GET_EXEX_READS_PER_JUNCTION`="/folder/of/preference/get_exex_i_PER_JUNCTION_from_sam.pl"
- `REMOVE_EX_EX`="/folder/of/preference/remove_exex_from_sam.pl" 
- `PARSE_ANN`="/folder/of/preference/parse_annotation_gtf2txt.pl"
- `GET_SPLIT_READ_FOR_WIG`="/folder/of/preference/get_split_reads_for_wigg.pl"
- `RECURS_MATRIX`="/folder/of/preference/recursive_splicing_matrix.pl"
- `PARSE_RECURS_OUTPUT`="/folder/of/preference/parse_recursive_output.pl"
- `WIG_PEAKS`="/folder/of/preference/wig_peaks_sign.pl"

You also need to specify the full pathes to the following input files:

1) `ANNOTATION`="/folder/of/interest/example_annotation.txt"

2) `CUTOFF_COV`="5000"

This is the cutoff of the coverage to normalize the wiggle files. Wiggle file
with the total gene coverage will be scaled and the average coverage will equal
`CUTOFF_COV`.

3) `PROBES_TO_INCLUDE`="/folder/of/preference/example_region.bed"

4) `REFSEQ`="/full/path/to/the/reference.fa"

Full path to the reference sequence that was used to generate the alignment.
Here we do not provide the reference sequence for the sake of space. For our
example alignments we used Homo Sapiens reference of build hg19.


--------------------------------------

When everything listed above is specified in your `run.sh` file, you can run
it.

    chmod 755 run.sh
    ./run.sh input_1.bam input_2.bam ...

All bam files should be indexed (and a `bam.bai` files should be placed in the
same folder).


In order to verify that the pipeline works properly on your machine, please
check the [list] (LINK)
of the names and the sizes of the results files.


### About

This pipeline was developed by Irina Pulyakhina (i.pulyakhina@lumc.nl), Jeroen
Laros (j.f.j.laros@lumc.nl) and Peter-Bram 't Hoen (p.a.c._t_hoen@lumc.nl) with
the help of Martijn Vermaat and Michel Villerius.


