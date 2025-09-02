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

## 2. Example Run: Per chromosome
```bash
bash /directory/this/saved/MUMandCo/mumandco_v3.8.sh -r /directory/this/saved/split_referencewheat_by_chr/Chr1A.fa -q /directory/this/saved/split_querywheat_by_chr/chr1A.fasta -g sizeinmb -o chr1A -t 48
```

## 5. SLURM Script: Chromosome 1A
```bash
#!/bin/bash
#SBATCH -N 1
#SBATCH -n 48
#SBATCH -p bigmem
#SBATCH --mem=1000GB
#SBATCH --time=72:00:00
#SBATCH -J chr1A-querywheat
#SBATCH -A genolabswheatphg

# Load required modules
module load mummer4/4.0.0rc1
module load samtools/1.19.2

#Run
bash /directory/this/saved/MUMandCo/mumandco_v3.8.sh -r /directory/this/saved/split_referencewheat_by_chr/Chr1A.fa -q /directory/this/saved/split_querywheat_by_chr/chr1A.fasta -g sizeinmb -o chr1A -t 48
```

Maintainer:

Ruby Mijan





