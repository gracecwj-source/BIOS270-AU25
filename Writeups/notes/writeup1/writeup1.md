**Set-up**
1. One job (1044827) is done, with three tasks.
2. If divides the tasks, so that one line shows only one task
3. There will be two outputs for each task. For task 0: 12 and 8. For task 1: 7 and 27. For task 2: 91 and 30.

The following code is used:
```python
mkdir -p logs
cat > data.txt << 'EOF'
12
7
91
8
27
30
EOF


cat > warmup.sh <<'EOF'
#!/usr/bin/env bash
#SBATCH --job-name=warmup
#SBATCH --output=logs/%x_%A_%a.out
#SBATCH --error=logs/%x_%A_%a.err
#SBATCH --array=0-2
#SBATCH --cpus-per-task=1
#SBATCH --mem=2G
#SBATCH --time=00:10:00

i=0
while read -r value; do
  if (( i % SLURM_ARRAY_TASK_COUNT == SLURM_ARRAY_TASK_ID )); then
    echo "$i: $value"
  fi
  ((i++))
done < data.txt
EOF

mkdir -p logs
chmod +x warmup.sh

sbatch warmup.sh

cat logs/warmup_1044827_0.out
cat logs/warmup_1044827_1.out
cat logs/warmup_1044827_2.out

```
**Micromamba**
1. The new YAML has only the packages that I installed, whereas old YAML is longer and contains everything when it is initially created.
2. To list all packages in a specific environment:
   ```python
   micromamba list -n bioinfo_example
   micromamba list
   ```
3. To remove a package:
   ```python
   micromamba remove -n bioinfo_example <package>
   micromamba remove -n bioinfo_example rpy2
   ```
4. To install a package from a specific channel
   ```bash
   micromamba install -n bioinfo_example -c bioconda <package>
   micromamba install -n bioinfo_example -c conda-forge rpy2
   ```
5. Remove an environment
   ```bash
   micromamba env remove -n bioinfo_example
   micromamba remove -n bioinfo_example --all
   ```
6.   Name         Version  Build             Channel    
───────────────────────────────────────────────────────
  r-base       4.3.3    h2fbd60f_20       conda-forge
  r-base64enc  0.1_3    r43hb1dbf0f_1007  conda-forge
List of packages in environment: "/home/users/gracecwj/micromamba/envs/bioinfo_example"

  Name                 Version  Build          Channel 
─────────────────────────────────────────────────────────
  bioconductor-deseq2  1.42.0   r43hf17093f_2  bioconda

  



