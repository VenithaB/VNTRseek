# VNTRseek (v2.0.3.LabFix)

[cite_start]VNTRseek is a computational pipeline for the detection of Variable Number Tandem Repeats (VNTRs) from next-generation sequencing reads[cite: 3]. [cite_start]Given a reference set of Tandem Repeats (TRs) and sequencing reads (e.g., Illumina), it produces a comprehensive SQLite database and a VCF file containing VNTR calls[cite: 3, 104].

## 🛠 Lab-Specific Bug Fixes
This fork includes critical patches to the original version 2.0.3 source code to ensure stability:
* **Config Generation Fix**: Fixed a "numeric comparison" bug in `vntrseek.pl` that caused the script to crash when generating a config file with a string-based filename.
* **Flag Standardization**: Corrected internal logic to ensure the `--GEN_CONFIG` flag operates consistently with provided paths.

## 🚀 Quick Start (Mandatory Command Structure)
[cite_start]Unlike earlier versions, this pipeline requires a **start and end step** to be provided for *all* operations, including setup tasks[cite: 24, 25].

### 1. Generate a Configuration File
To generate a default template, you must include dummy step numbers (`0 0`):
```bash
./vntrseek.pl 0 0 --GEN_CONFIG ./default.cnf 
```

### 2. Initialize the Reference Database
[cite_start] This only needs to be done once per reference set. Provide the base name of your reference files: 

```bash
./vntrseek.pl 0 0 --REFERENCE /path/to/reference/basename
```

### 3. Run the Full Pipeline

[cite_start] To run the complete analysis (Step 0 to Step 20):

```bash

./vntrseek.pl 0 20 --CONFIG ./my_config.cnf --RUN_NAME "my_analysis"
```

## 🛠 Input:

    * Supported Formats: FASTA (.fa), FASTQ (.fq), SAM, BAM, and CRAM .

    * Directory Based: Specify the directory containing your reads using --INPUT_DIR.

    * Compression: Supports gz, bz2, and xz (optionally preceded by tar). All files in a single run directory must use the same compression format.
	
##  Output:

#### Results are written to $OUTPUT_DIR/vntr_$RUN_NAME/

    * {RUN_NAME}.db: SQLite database containing complete processing data.

    * {RUN_NAME}.span2.vcf: The final VCF file containing called VNTRs.

    * {RUN_NAME}.vs.cnf: A record of the last settings used for the analysis.

    * data_out/: Intermediate files from TRF and trf2proclu.

## 🔧 Error Management

	VNTRseek stores the last error encountered and will not resume a run until the error is manually cleared.

    #### Clear Error: 
	``` bash
	./vntrseek.pl 100 --RUN_NAME "my_analysis".
	```
	
    #### Clear & Reset Step: 
	``` bash ./vntrseek.pl 100 N --RUN_NAME "my_analysis" 
	(where N is the new start step)
	```