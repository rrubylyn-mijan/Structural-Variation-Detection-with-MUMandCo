# Structural Variation Detection with MUM&Co (MUMmer4 + Samtools)

This repository documents how to install and run **MUM&Co** for whole-genome and chromosome-level structural variation detection in wheat genomes using **MUMmer4** and **Samtools** on HPC systems (e.g., Atlas/CCAST).

---

## 1. Installation & Setup
```bash
cd /your/directory
git clone https://github.com/SAMtoBAM/MUMandCo.git
cd MUMandCo

chmod +x mumandco__v3.8.sh

# Add MUM&Co to PATH
export PATH=/directory/this/saved/MUMandCo:$PATH
echo 'export PATH=/directory/this/saved/MUMandCo:$PATH' >> ~/.bashrc
source ~/.bashrc

# Load required modules
ml mummer4/4.0.0rc1
ml samtools/1.19.2

# Load help to know if it is working
./mumandco_v3.8.sh --help
```

## 2. Example Run: Whole Genome
```bash
bash /directory/this/saved/MUMandCo/mumandco_v*.sh \
  -r /directory/this/saved/Chinese_spring_v2.1.fa \
  -q /directory/this/saved/Sumai3_pm_v2.fasta \
  -g 12500000 \
  -o /directory/this/saved/mummer4_chunks_subjectwheat_referencewheat/mumandco_subjectwheat_referencewheat_output \
  -t 4 \
  -b
```

## 3. SLURM Batch Script: Whole Genome
```bash
#!/bin/bash
#SBATCH -N 1
#SBATCH -n 48
#SBATCH -p bigmem
#SBATCH --mem=1000GB
#SBATCH -J mum_co_subjectwheat_referencewheat
#SBATCH -A genolabswheatphg

module load mummer4/4.0.0rc1
module load samtools/1.19.2

bash /directory/this/saved/MUMandCo/mumandco_v*.sh \
  -r /directory/this/saved/subjectwheat.fa \
  -q /directory/this/saved/referencewheat.fasta \
  -g 12500000 \
  -o /directory/this/saved/mummer4_chunks_subjectwheat_referencewheat/1mumandco_subjectwheat_referencewheat \
  -t 48 \
  -b
```

## 4. Example Run: Single Chunk (10 Mb)
```bash
bash /directory/this/saved/MUMandCo/mumandco_v*.sh \
  -r /directory/this/saved/chunk_10Mb_subjectwheat/Chr1A_500000001_510000000.fa \
  -q /directory/this/saved/chunk_10Mb_referencewheat/chr1A_500000001_510000000.fasta \
  -g 10000000 \
  -o chr1A_500000001_510000000 \
  -t 48
```

## 5. SLURM Script: Chromosome 1A
```bash
#!/bin/bash
#SBATCH -N 1
#SBATCH -n 48
#SBATCH -p bigmem
#SBATCH --mem=1000GB
#SBATCH -J chr1A
#SBATCH -A genolabswheatphg

module load mummer4/4.0.0rc1
module load samtools/1.19.2

REF_GENOME="/directory/this/saved/split_subjectwheat_by_chr/Chr1A.fa"
QUERY_GENOME="/directory/this/saved/split_referencewheat_by_chr/chr1A.fasta"
OUTPUT_DIR="/directory/this/saved/mummer4_chr_subjectwheat_referencewheat/ch1A_alignments"
GENOME_SIZE="603896264"
THREADS=48

mkdir -p "$OUTPUT_DIR"

MUMANDCO_SCRIPT=$(ls /directory/this/saved/MUMandCo/mumandco_v*.sh | head -n 1)

bash "$MUMANDCO_SCRIPT" \
  -r "$REF_GENOME" \
  -q "$QUERY_GENOME" \
  -g "$GENOME_SIZE" \
  -o "$OUTPUT_DIR/ch1A" \
  -t "$THREADS"
```

## 6. SLURM Loop: Chromosomes 1B–7D
```bash
#!/bin/bash
#SBATCH -N 1
#SBATCH -n 48
#SBATCH -p bigmem
#SBATCH --mem=1000GB
#SBATCH -J MUMandCo_Loop
#SBATCH -A genolabswheatphg

module load mummer4/4.0.0rc1
module load samtools/1.19.2

declare -A GENOME_SIZES
GENOME_SIZES["1B"]="sizeinmb"
GENOME_SIZES["1D"]="sizeinmb"
GENOME_SIZES["2A"]="sizeinmb"
GENOME_SIZES["2B"]="sizeinmb"
GENOME_SIZES["2D"]="sizeinmb"
GENOME_SIZES["3A"]="sizeinmb"
GENOME_SIZES["3B"]="sizeinmb"
GENOME_SIZES["3D"]="sizeinmb"
GENOME_SIZES["4A"]="sizeinmb"
GENOME_SIZES["4B"]="sizeinmb"
GENOME_SIZES["4D"]="sizeinmb"
GENOME_SIZES["5A"]="sizeinmb"
GENOME_SIZES["5B"]="sizeinmb"
GENOME_SIZES["5D"]="sizeinmb"
GENOME_SIZES["6A"]="sizeinmb"
GENOME_SIZES["6B"]="sizeinmb"
GENOME_SIZES["6D"]="sizeinmb"
GENOME_SIZES["7A"]="sizeinmb"
GENOME_SIZES["7B"]="sizeinmb"
GENOME_SIZES["7D"]="sizeinmb"

MUMANDCO_SCRIPT=$(ls /directory/this/saved/MUMandCo/mumandco_v*.sh | head -n 1)

for CHR in {1..7}; do
  for SUB in A B D; do
    CHR_SUB="${CHR}${SUB}"
    if [[ "$CHR_SUB" == "1A" ]]; then continue; fi
    REF_GENOME="/directory/this/saved/split_subjectwheat_by_chr/Chr${CHR_SUB}.fa"
    QUERY_GENOME="/directory/this/saved/split_referencewheat_by_chr/chr${CHR_SUB}.fasta"
    OUTPUT_DIR="/directory/this/saved/mummer4_chr_subjectwheat_referencewheat/${CHR_SUB}_alignments"
    GENOME_SIZE="${GENOME_SIZES[$CHR_SUB]}"

    mkdir -p "$OUTPUT_DIR"

    bash "$MUMANDCO_SCRIPT" \
      -r "$REF_GENOME" \
      -q "$QUERY_GENOME" \
      -g "$GENOME_SIZE" \
      -o "$OUTPUT_DIR/${CHR_SUB}" \
      -t 48
  done
done
```

Maintainer:

Ruby Mijan




