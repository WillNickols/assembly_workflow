# assembly_workflow

> This repository contains code for Will's assembly workflow based on Aaron Walsh's pets assembly workflow and Segata et al. 2019. For queries, contact Will Nickols (email: <willnickols@college.harvard.edu>).

# Installation

Install with yml file
```
git clone https://github.com/WillNickols/assembly_workflow
cd assembly_workflow
conda env create -f assembly_environment.yml
conda activate biobakery_assembly
git clone --recursive https://github.com/chklovski/checkm2.git
checkm2/bin/checkm2 database --download --path databases/checkm/
export CHECKM_DATA_PATH=$(pwd)/databases/checkm/
mkdir tmp_to_delete && mkdir -p databases/phylophlan && touch tmp_to_delete/tmp.fa && phylophlan_metagenomic -d SGB.Jul20 --database_folder databases/phylophlan/ -i tmp_to_delete/; rm -r tmp_to_delete/
export PHYLOPHLAN_PATH=$(pwd)/databases/phylophlan/
```

Install necessary R packages
```
R
install.packages(c("docopt", "dplyr", "data.table", "stringr", "doParallel", "tidyr"))
q()
```

Once the conda environment is created and you are in the `assembly_workflow` directory, you can activate the environment with these commands:
```
conda activate biobakery_assembly
export CHECKM_DATA_PATH=$(pwd)/databases/checkm/
export PHYLOPHLAN_PATH=$(pwd)/databases/phylophlan/
```

# Example runs

Run the workflow without taxonomically placing the MAGs (only assemble, bin, and quality check)
```
python assembly_workflow.py \
  -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_inputs/single_end/ \
  -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_outputs/single_end/ \
  --abundance-type by_sample --input-extension fastq.gz --paired unpaired \
  --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/mags_and_sgbs_pipeline_testing/single_end/ \
  --grid-partition 'shared' --grid-jobs 96 --cores 8 --time 10000 --mem 40000 \
  --local-jobs 12 \
  --grid-options="--account=nguyen_lab" \
  --skip-placement y
```

Single end run
```
python assembly_workflow.py \
  -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_inputs/single_end/ \
  -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_outputs/single_end/ \
  --abundance-type by_sample --input-extension fastq.gz --paired unpaired \
  --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/mags_and_sgbs_pipeline_testing/single_end/ \
  --grid-partition 'shared' --grid-jobs 96 --cores 8 --time 10000 --mem 40000 \
  --local-jobs 12 \
  --grid-options="--account=nguyen_lab"
```

Paired end with fastq extension instead of fastq.gz
```
python assembly_workflow.py \
  -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_inputs/paired_end/ \
  -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_outputs/paired_end/ \
  --abundance-type by_sample --input-extension fastq --paired paired \
  --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/mags_and_sgbs_pipeline_testing/paired_end/ \
  --grid-partition 'shared' --grid-jobs 96 --cores 8 --time 10000 --mem 40000 \
  --local-jobs 12 \
  --grid-options="--account=nguyen_lab"
```

Concatenated input and removing intermediate outputs
```
python assembly_workflow.py \
  -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_inputs/concat/ \
  -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_outputs/concat/ \
  --abundance-type by_sample --input-extension fastq.gz --paired concatenated \
  --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/mags_and_sgbs_pipeline_testing/concat/ \
  --grid-partition 'shared' --grid-jobs 96 --cores 8 --time 10000 --mem 40000 \
  --local-jobs 12 \
  --grid-options="--account=nguyen_lab" \
  --remove-intermediate-files y
```

Run the biobakery wmgx assembly and then this pipeline from the assembled contigs
```
biobakery_workflows wmgx \
  --input /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_inputs/contigs_int_kneaddata/ \
  --output /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_outputs/contigs_int/ \
  --bypass-quality-control \
  --threads 8 \
  --bypass-functional-profiling \
  --bypass-strain-profiling \
  --bypass-taxonomic-profiling \
  --run-assembly \
  --grid-jobs 8 \
  --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/mags_and_sgbs_pipeline_testing/contigs_int/ \
  --grid-partition shared \
  --grid-options="--account=nguyen_lab" \
  --input-extension fastq

python assembly_workflow.py \
  -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_inputs/contigs_int_kneaddata/ \
  -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_outputs/contigs_int/ \
  --abundance-type by_sample --input-extension fastq --paired concatenated \
  --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/mags_and_sgbs_pipeline_testing/single_end/ \
  --grid-partition 'shared' --grid-jobs 96 --cores 8 --time 10000 --mem 40000 \
  --local-jobs 12 \
  --grid-options="--account=nguyen_lab" \
  --skip-contigs y
```
