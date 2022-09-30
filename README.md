# assembly_workflow

> This repository contains code for Will's assembly workflow based on Aaron Walsh's pets assembly workflow and Segata et al. 2019. For queries, contact Will Nickols (email: <willnickols@college.harvard.edu>).

### Dependencies
- Run `git clone --recursive https://github.com/WillNickols/assembly_workflow`
- Move into the directory: `cd assembly_workflow`
- The conda environment to run this workflow can be created with `conda env create -f environment.yml`
- Additionally, some equivalent to `hutlab load centos7/python3/biobakery_workflows/3.0.0-beta-devel` will be necessary.  Namely, PhyloPhlAn 3, MEGAHIT, and Mash (and possibly others but I'm not sure) will need to be installed and added to the path.
- If you're using `hutlab load centos7/python3/biobakery_workflows/3.0.0-beta-devel`, you should run `export SETUPTOOLS_USE_DISTUTILS=stdlib` to avoid path Python path issues
- You should also run `PATH=$PATH_TO_CONDA_ENVS/mags_and_sgbs2/lib:$PATH_TO_CONDA_ENVS/mags_and_sgbs2/bin:$PATH` to make sure the PATH is in the right order (where `$PATH_TO_CONDA_ENVS` is something like `/n/home08/wnickols/.conda/envs`)
- Likewise, run `PYTHONPATH=$PATH_TO_CONDA_ENVS/mags_and_sgbs2/lib/python3.7/site-packages:$PYTHONPATH`
- Checkm requires its own database which is available [here](https://data.ace.uq.edu.au/public/CheckM_databases/).  Once installed, run `export CHECKM_DATA_PATH=$LOCATION_OF_CHECKM_DATABASE`.
- Last I checked, Checkm2 still only supports installing from source, so follow the instructions [here](https://github.com/chklovski/CheckM2) to do that
- Follow the instructions [here](https://github.com/biobakery/phylophlan/wiki) to install the latest PhyloPhlAn 3 database.

### Running
You can run the workflow with the following form:

```
mag_workflow.py \
  -i [path to kneaddata directory] \
  -o [output directory] \
  --abundance-type [by_sample/by_dataset, (by_sample gets abundances by just aligning a sample's reads to its contigs, by_dataset aligns a sample's reads to any contigs)]
  --input-extension fastq.gz --paired [paired/unpaired/concatenated (paired expects 4 files per sample: _paired_1, _paired_2, _unmatched_1, _unmatched_2; unpaired and concatenated expect one per sample)] \
  --grid-scratch [scratch location] \
  --grid-partition [partition] --grid-jobs [jobs] --cores [threads] --time [max grid time] --mem [max grid memory] \
  --local-jobs [jobs] \
  --phylophlan-database [name of database] \
  --checkm-path [path to checkm2] \
  --phylophlan-database-folder [path to phylophlan database folder] \
```

An example of something I've run would be the following:

```
/n/holystore01/LABS/huttenhower_lab/Users/wnickols/code/mag_sgb_workflow/mag_workflow.py \
  -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/coastal_new/kneaddata/ \
  -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/coastal_new/assembly/ \
  --abundance-type by_sample --input-extension fastq.gz --paired paired \
  --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/coastal/assembly/ \
  --grid-partition 'shared' --grid-jobs 96 --cores 8 --time 10000 --mem 40000 \
  --local-jobs 12 \
  --phylophlan-database SGB.Jul20 \
  --checkm-path /n/holystore01/LABS/huttenhower_lab/Users/wnickols/checkm2/ \
  --phylophlan-database-folder /n/holystore01/LABS/huttenhower_lab/Users/wnickols/phylophlan/phylophlan_databases \
```

### Interpretation
The file `20220921 MAG Workflow.docx` has a description of what each step in the workflow does.  The main output (`final_profile_by_sample.tsv`) will look like a MetaPhlAn output file with samples as columns and taxa as rows.  The assigned taxa will be either known (name looks normal) or unknown (taxa name contains a genome bin (GB) ID like CGB, FGB, GGB, or SGB).  At the bottom of the file, you might get some new species unplaced by PhyloPhlAn 3 (sgb_XX).  The last line is an estimation of how much of the sample is unknown (this does not include sgb_XX type material but is instead based on the proportion of reads not aligning to contigs, the proportion of unbinned contigs, and the proportion of low quality bins).  All the numbers are the percent abundances (so columns should sum to 100).

### Commands I've run:
`python /n/holystore01/LABS/huttenhower_lab/Users/wnickols/code/mag_sgb_workflow/mag_workflow.py -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/coastal_new/kneaddata/ -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/coastal_new/assembly/ --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/coastal/assembly/ --grid-partition 'shared' --grid-jobs 96 --cores 8 --time 10000 --mem 40000 --input-extension fastq.gz --paired paired --local-jobs 12 --phylophlan-database SGB.Jul20 --checkm-path /n/holystore01/LABS/huttenhower_lab/Users/wnickols/checkm2/ --phylophlan-database-folder /n/holystore01/LABS/huttenhower_lab/Users/wnickols/phylophlan/phylophlan_databases --abundance-type by_sample`

`python /n/holystore01/LABS/huttenhower_lab/Users/wnickols/code/mag_sgb_workflow/mag_workflow.py -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/gators_new/kneaddata/ -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/gators_new/assembly/ --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/gators/assembly/ --grid-partition 'shared' --grid-jobs 96 --cores 8 --time 10000 --mem 40000 --input-extension fastq.gz --paired unpaired --local-jobs 12 --phylophlan-database SGB.Jul20 --checkm-path /n/holystore01/LABS/huttenhower_lab/Users/wnickols/checkm2/ --phylophlan-database-folder /n/holystore01/LABS/huttenhower_lab/Users/wnickols/phylophlan/phylophlan_databases --grid-options="--account=nguyen_lab" --abundance-type by_sample`

`python /n/holystore01/LABS/huttenhower_lab/Users/wnickols/code/mag_sgb_workflow/mag_workflow.py -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mine_new/kneaddata/ -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mine_new/assembly/ --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/mine/ --grid-partition 'shared' --grid-jobs 96 --cores 8 --time 10000 --mem 50000 --input-extension fastq.gz --paired paired --local-jobs 12 --phylophlan-database SGB.Jul20 --checkm-path /n/holystore01/LABS/huttenhower_lab/Users/wnickols/checkm2/ --phylophlan-database-folder /n/holystore01/LABS/huttenhower_lab/Users/wnickols/phylophlan/phylophlan_databases --abundance-type by_sample`

`python /n/holystore01/LABS/huttenhower_lab/Users/wnickols/code/mag_sgb_workflow/mag_workflow.py -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/delignification_new/kneaddata/ -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/delignification_new/assembly/ --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/delignification/ --grid-partition 'shared' --grid-jobs 96 --cores 8 --time 10000 --mem 100000 --input-extension fastq.gz --paired paired --local-jobs 12 --phylophlan-database SGB.Jul20 --checkm-path /n/holystore01/LABS/huttenhower_lab/Users/wnickols/checkm2/ --phylophlan-database-folder /n/holystore01/LABS/huttenhower_lab/Users/wnickols/phylophlan/phylophlan_databases --abundance-type by_sample`

`python /n/holystore01/LABS/huttenhower_lab/Users/wnickols/code/mag_sgb_workflow/mag_workflow.py -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/tara_polar_new/kneaddata/ -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/tara_polar_new/assembly/ --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/tara_polar/ --grid-partition 'shared' --grid-jobs 96 --cores 8 --time 10000 --mem 120000 --input-extension fastq.gz --paired paired --local-jobs 12 --phylophlan-database SGB.Jul20 --checkm-path /n/holystore01/LABS/huttenhower_lab/Users/wnickols/checkm2/ --phylophlan-database-folder /n/holystore01/LABS/huttenhower_lab/Users/wnickols/phylophlan/phylophlan_databases --abundance-type by_sample`

`python /n/holystore01/LABS/huttenhower_lab/Users/wnickols/code/mag_sgb_workflow/mag_workflow.py -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/wild_gut/kneaddata2/ -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/wild_gut/assembly/ --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/wild_gut/ --grid-partition 'shared' --grid-jobs 96 --cores 8 --time 10000 --mem 40000 --input-extension fastq.gz --paired paired --local-jobs 12 --phylophlan-database SGB.Jul20 --checkm-path /n/holystore01/LABS/huttenhower_lab/Users/wnickols/checkm2/ --phylophlan-database-folder /n/holystore01/LABS/huttenhower_lab/Users/wnickols/phylophlan/phylophlan_databases --abundance-type by_sample`

`python /n/holystore01/LABS/huttenhower_lab/Users/wnickols/code/mag_sgb_workflow/mag_workflow.py -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/saltmarsh/kneaddata/ -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/saltmarsh/assembly/ --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/saltmarsh/ --grid-partition 'shared' --grid-jobs 96 --cores 8 --time 10000 --mem 40000 --input-extension fastq.gz --paired paired --local-jobs 12 --phylophlan-database SGB.Jul20 --checkm-path /n/holystore01/LABS/huttenhower_lab/Users/wnickols/checkm2/ --phylophlan-database-folder /n/holystore01/LABS/huttenhower_lab/Users/wnickols/phylophlan/phylophlan_databases --abundance-type by_sample`

`python /n/holystore01/LABS/huttenhower_lab/Users/wnickols/code/mag_sgb_workflow/mag_workflow.py -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/dogs_out/kneaddata/ -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/dogs_out/assembly/ --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/dogs_out/ --grid-partition 'shared' --grid-jobs 96 --cores 8 --time 10000 --mem 40000 --input-extension fastq.gz --paired paired --local-jobs 12 --phylophlan-database SGB.Jul20 --checkm-path /n/holystore01/LABS/huttenhower_lab/Users/wnickols/checkm2/ --phylophlan-database-folder /n/holystore01/LABS/huttenhower_lab/Users/wnickols/phylophlan/phylophlan_databases --abundance-type by_sample`

`python /n/holystore01/LABS/huttenhower_lab/Users/wnickols/code/mag_sgb_workflow/mag_workflow.py -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/cats_out/kneaddata/ -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/cats_out/assembly/ --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/cats_out/ --grid-partition 'shared' --grid-jobs 96 --cores 8 --time 10000 --mem 40000 --input-extension fastq.gz --paired paired --local-jobs 12 --phylophlan-database SGB.Jul20 --checkm-path /n/holystore01/LABS/huttenhower_lab/Users/wnickols/checkm2/ --phylophlan-database-folder /n/holystore01/LABS/huttenhower_lab/Users/wnickols/phylophlan/phylophlan_databases --abundance-type by_sample`

`python /n/holystore01/LABS/huttenhower_lab/Users/wnickols/code/mag_sgb_workflow/mag_workflow.py -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/human/input/ -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/human/assembly/ --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/human/assembly/ --grid-partition 'shared' --grid-jobs 100 --cores 8 --time 10000 --mem 40000 --input-extension fastq.gz --paired concatenated --local-jobs 12 --phylophlan-database SGB.Jul20 --phylophlan-database-folder /n/holystore01/LABS/huttenhower_lab/Users/wnickols/phylophlan/phylophlan_databases --checkm-path /n/holystore01/LABS/huttenhower_lab/Users/wnickols/checkm2/ --abundance-type by_sample`

`python /n/holystore01/LABS/huttenhower_lab/Users/wnickols/code/mag_sgb_workflow/mag_workflow.py -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/forest_soil_2/kneaddata/ -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/forest_soil_2/assembly/ --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/forest_soil_2/assembly/ --grid-partition 'shared' --grid-jobs 100 --cores 8 --time 10000 --mem 40000 --input-extension fastq.gz --paired paired --local-jobs 12 --phylophlan-database SGB.Jul20 --phylophlan-database-folder /n/holystore01/LABS/huttenhower_lab/Users/wnickols/phylophlan/phylophlan_databases --abundance-type by_sample --checkm-path /n/holystore01/LABS/huttenhower_lab/Users/wnickols/checkm2/`
