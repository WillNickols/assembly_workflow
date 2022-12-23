conda activate simulate
python /n/holystore01/LABS/huttenhower_lab/Users/wnickols/CAMISIM/metagenomesimulation.py /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/example/configs/config.ini

mv /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/example/output/... /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_inputs/example/raw/sample_1.fastq.gz

conda activate bbmap
hutlab load centos7/python3/biobakery_workflows/3.0.0-beta-devel-dependsUpdate
cd /n/holystore01/LABS/huttenhower_lab/Users/wnickols/code/simulations/

python split_inputs_workflow.py -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_inputs/example/raw/ -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_inputs/example/split/ --input-extension fastq.gz --local-jobs 1

cd ..
python kneaddata_workflow_updated.py -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_inputs/example/split/ \
  -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_inputs/example/kneaddata/ \
  --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/mags_and_sgbs_pipeline_testing/kneaddata \
  --grid-partition 'shared' \
  --grid-jobs 1 --cores 8 \
  --mem 10000 \
  --input-extension fastq.gz \
  --paired paired \
  --grid-options="--account=nguyen_lab"

conda activate biobakery_assembly
export CHECKM_DATA_PATH=$(pwd)/databases/checkm/
export PHYLOPHLAN_PATH=$(pwd)/databases/phylophlan/

python assembly_workflow.py \
  -i /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_inputs/example/kneaddata/ \
  -o /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_outputs/example/ \
  --abundance-type by_sample --input-extension fastq.gz --paired paired \
  --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/mags_and_sgbs_pipeline_testing/assembly/ \
  --grid-partition 'shared' --grid-jobs 8 --cores 8 --mem 10000 \
  --local-jobs 4

biobakery_workflows wmgx \
  --input /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_inputs/example/kneaddata/contigs_int/ \
  --output /n/holylfs05/LABS/nguyen_lab/Everyone/wnickols/mags_and_sgbs_pipeline_testing/test_inputs/example/assembly_example/ \
  --bypass-quality-control \
  --threads 8 \
  --bypass-functional-profiling \
  --bypass-strain-profiling \
  --bypass-taxonomic-profiling \
  --run-assembly \
  --grid-jobs 8 \
  --grid-scratch /n/holyscratch01/nguyen_lab/wnickols/mags_and_sgbs_pipeline_testing/example/assembly_example/ \
  --grid-partition shared \
  --input-extension fastq.gz \
  --grid-options="--account=nguyen_lab"