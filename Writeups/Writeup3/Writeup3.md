**Writeup 3**

I created remote for google cloud storage under the name 'gcs, and for google drive under the name 'gdrive'. Project ID for BIOS270 on google cloud: lithe-aileron-478007-j2

I am able to create local SQL database with job ID: Submitted batch job 1046071

1.1 There will be three tables: gff_table.py, protein_cluster_table.py, metadata_table.py

1.2 The Slurm job is an array job, where several tasks can hit the database simultaneously. Without the try/except + retry loop, a transient lock would cause the whole job to fail even though retrying a moment later would work.

2. db.index_record_ids() creates an index, so that SQLite can look up rows by record_id using that index instead of scanning the entire table. This change makes the total runtime drops dramatically. Before creating an index, 10 record id took 40 seconds. After creating the index, 10 record id took 1.54 seconds. 

3. Chunk size decides how many rows to read from the database each time. This allows multiple medium size chunks upload instead of uploading a huge chunk at the same time, and if one chunk fails, rest of the data can still be uploaded.

4. With this chunk, the code will read all embedding features for 1000 proteins instead of reading one feature across all proteins. This controls the memory usage and ensures the efficiency of the code.
