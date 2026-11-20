

## Environment
---
We recommend using Conda to manage project dependencies for ensuring environment consistency.



1.  **Use Conda to create an environment and install the dependencies.**
   
    We provide an `environment.yml` file for quick environment setup. Run the following command, and Conda will automatically create a new environment named `DynamicSD-ATAC` and install all required packages.
    
    Download Conda: [https://repo.anaconda.com/miniconda/Miniconda3-py39_23.11.0-1-Linux-x86_64.sh](https://repo.anaconda.com/miniconda/Miniconda3-py39_23.11.0-1-Linux-x86_64.sh)
   
    ```bash
    conda env create -f environment.yml -n DynamicSD-ATAC
    ```

3.  **Activate the environment using the following command**
    ```bash
    conda activate DynamicSD-ATAC
    ```

4.  **Software installation**
   
      Dowload DynamicSD-ATAC from [here](https://handover.obs.cn-jssz1.ctyun.cn:443/software%2FDynamicSD-ATAC_v1.0.3.tar.gz?AccessKeyId=ZQMMROORLBRHRFUORXSZ&Expires=1803541831&Signature=3z8kmuGNPNCsnhVSGKwnuAY0%2F8A%3D).
    
      ```bash
      mkdir DynamicSD-ATAC
      tar -zxf DynamicSD-ATAC_v1.0.3.tar.gz -C DynamicSD-ATAC
      Prepend the DynamicSD-ATAC/DynamicSD-ATAC_v1.0.3/bin directory to your $PATH. This will allow you to invoke the DynamicSD-ATAC command.
      
      echo "export PATH=\$PATH:$(pwd)/DynamicSD-ATAC/DynamicSD-ATAC_v1.0.3/bin" >> ~/.bashrc
      source ~/.bashrc
      ```
  

   
   
### Quick start
---
- mkref

```shell

For human：

wget ftp://ftp.ensembl.org/pub/release-99/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz
wget ftp://ftp.ensembl.org/pub/release-99/gtf/homo_sapiens/Homo_sapiens.GRCh38.99.gtf.gz

gunzip Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz
gunzip Homo_sapiens.GRCh38.99.gtf.gz

DynamicSD-ATAC mkref \
 --genome_name Homo_sapiens_GRCh38 \
 --fasta Homo_sapiens.GRCh38.dna.primary_assembly.fa \
 --gtf Homo_sapiens.GRCh38.99.gtf


For mouse：

wget ftp://ftp.ensembl.org/pub/release-99/fasta/mus_musculus/dna/Mus_musculus.GRCm38.dna.primary_assembly.fa.gz
wget ftp://ftp.ensembl.org/pub/release-99/gtf/mus_musculus/Mus_musculus.GRCm38.99.gtf.gz

gunzip Mus_musculus.GRCm38.dna.primary_assembly.fa.gz
gunzip Mus_musculus.GRCm38.99.gtf.gz

DynamicSD-ATAC mkref \
 --genome_name Mus_musculus.GRCm38 \
 --fasta Mus_musculus.GRCm38.dna.primary_assembly.fa \
 --gtf Mus_musculus.GRCm38.99.gtf

```


- count

```shell
 DynamicSD-ATAC count \
 --sample samplename \
 --R1 samplename_R1.fastq.gz \
 --R2 samplename_R2.fastq.gz \
 --species human \
 --index genome_name/genome.fa \
 --whitelist-fastq  2D250806020_C2.fq.gz \
 --image DAPI.tif \
 --chemistry ATAC \
 --chiptype SD_5_13 \
 --cellbin \
 --micrometer-pixel 0.37 \
 --cores 32
```


**Note**: The probe-set file resides in the db subdirectory of the software installation directory, while the whitelist-fastq file is provided by the company.
## Usage
---

## Analysis
### Inputs

---

**Input Parameters for DynamicSD count**
|  **Argument**  |  **Brief Description**  |
|  :--------: |  :-----:  |
|    --sample   |   Sample name for the batch   |
|    --R1   |   Path to the first read (R1) FASTQ file (gzipped).   |
|    --R2   |   Path to the second read (R2) FASTQ file (gzipped).   |
|  --species  |  Reference genome used in archr (mm10 for mouse, hg38 for human)  |
|--index|Path to BWA reference genome|
|--whitelist|Path to the whitelist FASTQ file|
|--image|Path to the image file|
|--chemistry|Assay chemistry type. Choose "ATAC" for ATAC-seq or "Cuttag" for CUT&Tag.|
|--chiptype|Chip specification: "SD_5_13" (5mm*13mm) or "SD_10_10" (10mm*10mm)|
|--cellbin|Perform Cellbin analysis|
|--manual-registration|Perform manual registration|
|--alignment|Path to pre-aligned json file from manual registration|
|--fragments|Path to pre-existing fragments file|
|--bin-list|Three bin sizes for analysis (default: 16 30 50)|
|--micrometer-pixel|Physical size of one pixel in micrometers (µm). Example: 0.37 (i.e., 1 pixel = 0.37 µm). Required for spatial scale conversion.|
|--outputdir|Output directory path (default: current directory)|
|--cores|Maximum CPU cores to use (default: 16)|
|--memory|Memory per thread (in GB) for BWA-MEM sorting (default: 8)|

**DynamicSD-ATAC Image Recommendations**
 ```
    Pixel Accuracy: ≤ 0.65 µm/pixel

    Pixel Dimensions (X, Y): ≤ 30,000

    Tissue Area Percentage: ≥ 80%
```

**Manual Alignment for DynamicSD-ATAC**

In most cases, DynamicSD-ATAC will automatically align microscope images with nFrags images. If manual alignment is necessary, choose the option that best fits your experiment.
[https://github.com/DynamicBiosystems/DynamicSDAssist](https://github.com/DynamicBiosystems/DynamicSD-Assist)

**Cell Segmentation**

DynamicSD-ATAC performs cell segmentation only on DAPI stained images. regulated by the `--cellbin` parameter. 

### Outputs
#### Overview of output structure
---
As an example, a `DynamicSD-ATAC count` analysis will display a message similar to the following after completion:
```
├── 16um
│   └── spatial
├── 30um
│   └── spatial
├── 50um
│   └── spatial
├── bin_summary.csv
├── CellBin
│   └── spatial
├── cellbin_summary.csv
├── SampleName_16um_filtered_peak_bc_matrix
│   ├── barcodes.tsv.gz
│   ├── features.tsv.gz
│   └── matrix.mtx.gz
├── SampleName_16um_fragments.tsv.gz
├── SampleName_16um_fragments.tsv.gz.tbi
├── SampleName_16um_peak_annotation.tsv
├── SampleName_30um_filtered_peak_bc_matrix
│   ├── barcodes.tsv.gz
│   ├── features.tsv.gz
│   └── matrix.mtx.gz
├── SampleName_30um_fragments.tsv.gz
├── SampleName_30um_fragments.tsv.gz.tbi
├── SampleName_30um_peak_annotation.tsv
├── SampleName_50um_filtered_peak_bc_matrix
│   ├── barcodes.tsv.gz
│   ├── features.tsv.gz
│   └── matrix.mtx.gz
├── SampleName_50um_fragments.tsv.gz
├── SampleName_50um_fragments.tsv.gz.tbi
├── SampleName_50um_peak_annotation.tsv
├── SampleName_cellbin_filtered_peak_bc_matrix
│   ├── barcodes.tsv.gz
│   ├── features.tsv.gz
│   └── matrix.mtx.gz
├── SampleName_cellbin_fragments.tsv.gz
├── SampleName_cellbin_fragments.tsv.gz.tbi
├── SampleName_cellbin_peak_annotation.tsv
├── SampleName_web_summary.html
└── metrics_summary.csv

```


















  













