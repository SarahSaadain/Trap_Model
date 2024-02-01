```
#!/bin/bash

# Full path to proTRAC_2.3.1.pl
proTRAC_path="/home/sarah/different_species/proTRAC_2.3.1.pl"

# Find the genome file with a .fna extension in the current directory
genome_path=$(find . -maxdepth 1 -type f -name "*.fna" | head -n 1)

# Check if a .fna file was found
if [ -z "$genome_path" ]; then
    echo "No .fna file found in the current directory."
    exit 1
fi

# Iterate through folders ending with _follicle and _ovary
for folder in *_follicle/ *_ovary/; do
    # Check if the folder contains .map files
    if [ -n "$(find "$folder" -maxdepth 1 -type f -name "*.map")" ]; then
        # Iterate through .map files in the folder
        for map_file in "$folder"*.map; do
            echo "Processing files in folder: $folder"
            echo "Input MAP: $map_file"
            echo "Genome FNA: $genome_path"

            # Add your proTRAC_2.3.1.pl command with absolute path to genome file
            # Example:
            perl "$proTRAC_path" -map "$map_file" -genome "$genome_path"

            echo "------------------------"  # Separator line
        done
    fi
done
```

add execution permission:
```
chmod +x proTRAC_automate.sh
```

run in the back:
```
nohup ./proTRAC_automate.sh > Dyak_automate_proTRAC.log 2>&1 &
```

the one liner version for one file only
```
sudo perl proTRAC_2.4.3.pl -map ../../curana/Dana_ovaries_SRR23593057.sam -format SAM -genome ref/Dana_ref_GCA_018148915.1_ASM1814891v1_genomic.fna
```
