# VNTRseek (v2.0.3.LabFix)

VNTRseek is a computational pipeline for the detection of Variable Number Tandem Repeats (VNTRs) from next-generation sequencing reads. Given a reference set of Tandem Repeats (TRs) and sequencing reads (e.g., Illumina), it produces a comprehensive SQLite database and a VCF file containing VNTR calls.

## 🛠 Lab-Specific Bug Fixes

This fork includes critical patches to the original version 2.0.3 source code to ensure stability on modern Linux systems (tested on Ubuntu 24 / WSL2):

* **Config Generation Fix**: Fixed a "numeric comparison" bug in `vntrseek.pl` that caused the script to crash when generating a config file with a string-based filename.

* **Flag Standardization**: Corrected internal logic to ensure the `--GEN_CONFIG` flag operates consistently with provided paths.

* **Reference Path Fix**: Fixed `vutil.pm` to use `abs_path()` for the `.leb36` and `.seq` reference files, preventing `redund.exe` from failing to locate them depending on working directory. Always pass `--REFERENCE` as a full absolute path.

* **redund.exe Single-File Fix**: Fixed a bug in `redund2.c` where `filecounter` was never incremented when processing a single file (non-directory) input on the second pass, causing a crash with `filecounter: 0 not equal to FARRAY->size: 1`.

* **Short-Period Profile Filter**: Fixed `vutil.pm` to filter out reference profiles with pattern length < 7 before passing them to `redund.exe`. The original `.leb36` reference files may contain entries with period as low as 1, which caused `psearch.exe` to crash with `pmin(1) must be in 7-5000 range`.

* **run_proclu.pl Argument Fix**: Fixed a missing space before the `$maxerror` argument in the `psearch.exe` command string, which caused arguments to be concatenated and misread (e.g., `050` instead of `0 50`).

* **pcr_dup.pl Premature Disconnect Fix**: Removed a premature `$dbh->disconnect()` call in `pcr_dup.pl` that caused an "attempt to prepare on inactive database handle" error at step 14.

* **vntrseek.pl SQL Interpolation Fix**: Fixed `q{}` to `qq{}` in the final stats migration SQL query at step 20, so the `$sn` variable is correctly interpolated into the column names.

## 🚀 Quick Start (Mandatory Command Structure)

Unlike earlier versions, this pipeline requires a **start and end step** to be provided for *all* operations, including setup tasks.

### 1. Generate a Configuration File

To generate a default template, you must include dummy step numbers (`0 0`):

```bash
./vntrseek.pl 0 0 --GEN_CONFIG ./default.cnf
```

### 2. Initialize the Reference Database

This only needs to be done once per reference set. Always use the **full absolute path** to your reference files:

```bash
./vntrseek.pl 0 0 --REFERENCE /full/path/to/reference/HumanChr5 --REFERENCE_INDIST_PRODUCE 1
```

### 3. Run the Full Pipeline

To run the complete analysis (Step 0 to Step 20):

```bash
./vntrseek.pl 0 20 \
  --REFERENCE /full/path/to/reference/HumanChr5 \
  --RUN_NAME my_analysis \
  --INPUT_DIR /path/to/input/reads \
  --OUTPUT_DIR /path/to/output
```

## 🛠 Input

* **Supported Formats**: FASTA (.fa), FASTQ (.fq), SAM, BAM, and CRAM.
* **Directory Based**: Specify the directory containing your reads using `--INPUT_DIR`.
* **Compression**: Supports gz, bz2, and xz (optionally preceded by tar). All files in a single run directory must use the same compression format.

## 📤 Output

Results are written to `$OUTPUT_DIR/vntr_$RUN_NAME/`:

* `{RUN_NAME}.db` — SQLite database containing complete processing data.
* `{RUN_NAME}_rl{READ_LENGTH}.db` — Final reduced output database.
* `{RUN_NAME}.span2.vcf` — The final VCF file containing called VNTRs.
* `{RUN_NAME}.allwithsupport.span2.vcf` — All ref TRs with any read support.
* `{RUN_NAME}.vs.cnf` — A record of the last settings used for the analysis.
* `data_out/` — Intermediate files from TRF and trf2proclu.

## 🔧 Error Management

VNTRseek stores the last error encountered and will not resume a run until the error is manually cleared.

#### Clear Error:
```bash
./vntrseek.pl 100 --RUN_NAME "my_analysis"
```

#### Clear & Reset Step:
```bash
./vntrseek.pl 100 N --RUN_NAME "my_analysis"
```
(where N is the new start step)