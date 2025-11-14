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

  ```
***Container***
Step 3 Code:

Step 5 & 6 Code:
```python
export PORT=27342      # pick any free port 20000–30000
export SIF="$SCRATCH/bioinformatics_latest.sif"

apptainer run -B "$SCRATCH",/farmshare/home/classes/bios/270 "$SIF" \
  code-server --bind-addr 127.0.0.1:$PORT --auth none
```



  
