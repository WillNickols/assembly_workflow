# assembly_workflow

> This repository contains code for Will's assembly workflow based on Aaron Walsh's pets assembly workflow and Segata et al. 2019. For queries, contact Will Nickols (email: <willnickols@college.harvard.edu>).

# Installation

Install everything manually
```
conda create --name biobakery_assembly -c biobakery biobakery_workflows
conda activate biobakery_assembly
git clone --recursive https://github.com/chklovski/checkm2.git && cd checkm2 && conda env update --file checkm2.yml
conda activate biobakery_assembly
conda install -c bioconda megahit metabat2 assembly-stats fastani
conda install -c r r
conda install -c conda-forge python-leveldb r-stringi multiprocess
cd ..
#export PATH="/home/$USER/bin:$PATH"
checkm2/bin/checkm2 database --download --path /custom/checkm/path/
export CHECKM_DATA_PATH=/custom/checkm/path/
#echo '{"dataRoot": "/custom/checkm/path/", "remoteManifestURL": "https://data.ace.uq.edu.au/public/CheckM_databases/", "manifestType": "CheckM", "localManifestName": ".dmanifest", "remoteManifestName": ".dmanifest"}' > DATA_CONFIG
mkdir tmp_to_delete && mkdir -p /custom/phylophlan/path/ && touch tmp_to_delete/tmp.fa && phylophlan_metagenomic -d SGB.Jul20 --database_folder /custom/phylophlan/path/ -i tmp_to_delete/; rm -r tmp_to_delete/
export PHYLOPHLAN_PATH=/custom/phylophlan/path/
```

Install with yml file
```
conda env create -f assembly_environment.yml
```

```
R
install.packages(c("docopt", "dplyr", "data.table", "stringr", "doParallel"))
```

To run once the conda environment is running:
```
conda activate biobakery_assembly
export CHECKM_DATA_PATH=/custom/checkm/path/
export PHYLOPHLAN_PATH=/custom/phylophlan/path/
```

No placement
```
python assembly_workflow.py \
  -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_inputs/single_end/ \
  -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_outputs/single_end/ \
  --abundance-type by_sample --input-extension fastq.gz --paired unpaired \
  --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/mags_and_sgbs_pipeline_testing/single_end/ \
  --grid-partition 'shared' --grid-jobs 96 --cores 8 --time 10000 --mem 40000 \
  --local-jobs 12 \
  --grid-options="--account=nguyen_lab" \
  --skip-placement y \
  --dry-run
```

Single end
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

Paired end with fastq instead of fastq.gz
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

From concatenated file with remove intermediate files
```
python assembly_workflow.py \
  -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_inputs/concat/ \
  -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_outputs/concat/ \
  --abundance-type by_sample --input-extension fastq.gz --paired unpaired \
  --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/mags_and_sgbs_pipeline_testing/concat/ \
  --grid-partition 'shared' --grid-jobs 96 --cores 8 --time 10000 --mem 40000 \
  --local-jobs 12 \
  --grid-options="--account=nguyen_lab"
  --remove-intermediate-files
```

From biobakery assembly output
```
biobakery_workflows wmgx \
  --input /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_inputs/paired_end/ \
  --output /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_inputs/contigs_int/ \
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
  -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_inputs/single_end/ \
  -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_outputs/single_end/ \
  --abundance-type by_sample --input-extension fastq.gz --paired unpaired \
  --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/mags_and_sgbs_pipeline_testing/single_end/ \
  --grid-partition 'shared' --grid-jobs 96 --cores 8 --time 10000 --mem 40000 \
  --local-jobs 12 \
  --grid-options="--account=nguyen_lab" \
  --skip-contigs
```
