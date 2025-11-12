**Answer to Write-up Questions**
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
