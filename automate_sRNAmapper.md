automate the sRNAmapper for all files
Dspecies_automate.sh
```

#!/bin/bash

# Full path to sRNAmapper.pl
sRNAmapper_path="/home/sarah/different_species/sRNAmapper.pl"

# Find the genome file with a .fna extension in the current directory
genome_path=$(find . -maxdepth 1 -type f -name "*.fna" | head -n 1)

# Check if a .fna file was found
if [ -z "$genome_path" ]; then
    echo "No .fna file found in the current directory."
    exit 1
fi

# Iterate through folders ending with _follicle and _ovary
for folder in *_follicle/ *_ovary/; do
    # Check if the folder contains .fastq.gz files
    if [ -n "$(find "$folder" -maxdepth 1 -type f -name "*.fastq.gz")" ]; then
        # Iterate through .fastq.gz files in the folder
        for fastq_file in "$folder"*.fastq.gz; do
            echo "Processing files in folder: $folder"
            echo "Input FASTQ: $fastq_file"
            echo "Genome FNA: $genome_path"

            # Add your sRNAmapper.pl command with absolute path to genome file
            # Example:
            perl "$sRNAmapper_path" -input "$fastq_file" -genome "$genome_path" -alignment best

            echo "------------------------"  # Separator line
        done
    fi
done
```


run it
```
nohup ./automate.sh > automate.log 2>&1 &
```
