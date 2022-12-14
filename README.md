# strtoolkit
Toolkit to discover and characterize STRs. This toolkit can discover STR loci with no prior knowledge based on the bam files provided and once discovered, it will compute some statistics at the locus like size, methylation. 

## Dependencies
Developed on Rust and C++17. Dependencies include:
* [HTSlib](https://github.com/samtools/htslib)

## Installation instructions

### Cloning the repository
```
git clone --recursive https://github.com/sabiqali/strtoolkit.git
git submodule init
```

### Installing HTSlib
```
cd ./strtoolkit/cpp/htslib
make
```

### Installing the toolkit
```
cd ../          //should be inside cpp folder before using make
make
```

### Installation notes
* Please make sure ```libdeflate``` has been installed, else there will be a linking error that will be generated. HTSlib depends on this and it might not be installed natively. An easy way to install it if not installed is via conda and then keeping the conda environment loaded while running the toolkit.
* Please make sure that the path of the shared library generated from generated has been linked to ```LD_LIBRARY_PATH```, else there will be linking errors. If you see these errors, please do the following ```export LD_LIBRARY_PATH="/path/to/toolkit/strtoolkit/cpp/htslib"``` 

## Usage
```
Usage: ./strr10toolkit [OPTIONS] --bam input.bam --reference reference_genome.fasta --output_file_name output_file_name.tsv --output_directory /path/to/output/directory
Toolkit to detect and analyse STR loci

  -b, --bam=FILE                       the input bam file
  -r, --reference=FILE                 the reference genome
  -o, --output_file_name=FILE          the output file name without extension
  -d, --output_directory=PATH          the directory where all output files will be generated
      --min_ins_size=NUM               the minimum number of bases in the insert event to call as an STR.(Default: 50bp)
      --is_phased                      is phasing data present in the bam?
  -v, --verbose                        will output the data from intermediate steps. Requires output_file_name and output_directory to be set, if used. will print to stdout if not set
      --min_read_support=NUM           the minimum number of reads that support the insert call.(Default: 3)
      --chromosomes=FILE               the chromosomes to be searched for STRs. Alternatively, you can provide a list of chromosomes in text file separated by a whitespace
  -c, --clean                          cleanup any intermediate files
      --window_size=NUM                the size of the search window.(Default: 5000bp)
      --min_repeat_size=NUM            minimum length of repeat motif.(Default: 3)
      --max_repeat_size=NUM            maximum length of repeat motif. (Default: 6)
      --min_map_quality=NUM            minimum mapping quality of individual reads.(Default: 20)
      --discovery_sensitivity=NUM      minimum number of repeat units in the repeat expansion to be considered, to be used with min_ins_size.(Default: 10)
  -t, --threads=NUM                    use NUM threads.(Default: 1)
```

* It is mandatory to provide the bam file to perform the analysis. Please ensure that an index file exists for the bam file. If you don't have one, it can be generated easily with the command:
```
samtools index input.bam
```
* The default output is to stdout. If you want file output, please set the ```--verbose``` tag and provide and output file_name and directory to get the output in a ```.tsv``` format.
* The tool can provide a haplotype based analysis, the pre-requisite being phasing data should be present in the ```.bam``` in the form of haplotags("HP"). For more information on this visit the Whatshap [README](https://whatshap.readthedocs.io/en/latest/guide.html#whatshap-haplotag). Haplotags can be generated easily using Whatshap using the following command:
```
whatshap haplotag -o haplotagged.bam --reference reference.fasta phased.vcf.gz alignments.bam
```
* The toolkit involves several other configurable units which are mentioned in the usage above.

## Output

The output is in a ```.tsv``` format that will look something like this:
| chromosome | start | end | reference_length | motif | interruption_motif | str_length | in_repeat_methylation | 
| --- | --- | --- | --- | --- | --- | ---| --- |

* chromosome: The chromosome on which the repeat locus has been found.
* start: The reference based start position of the locus.
* end: The reference based end position of the locus.
* reference_length: The reference length spanned.
* motif: The motif sequence discovered at the locus.
* interruption_motif: If an interruption is detected in the repeat expansion, the motif in the interruption shall be shown here. Else, it shall show N/A
* str_length: The estimated size of the repeat expansion. 
* in_repeat_methylation: The average methylation in the repeat expansion at the locus. 

## Contact

[Sabiq Chaudhary](mailto:schaudhary@oicr.on.ca)

## License

```MIT```
