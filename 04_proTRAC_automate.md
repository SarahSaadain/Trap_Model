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

new version
```
#!/bin/bash

# Full path to proTRAC_2.4.4.pl
proTRAC_path="/home/vetlinux04/Sarah/softwares/proTRAC_2.4.4.pl"

# Path to the genome directory
genome_dir="/home/vetlinux04/Sarah/trapmodel/ref"

# Iterate through ovaries_without_adapter
for folder in /home/vetlinux04/Sarah/trapmodel/ovaries_without_adapter/*/; do
    # Check if the folder contains .map files
    if [ -n "$(find "$folder" -maxdepth 1 -type f -name "*.map")" ]; then
        # Iterate through .map files in the folder
        for map_file in "$folder"*.map; do
            echo "Processing files in folder: $folder"
            echo "Input MAP: $map_file"

            # Extract the species prefix from the map file
            species_prefix=$(basename "$map_file" | cut -c 1-4)

            # Find the corresponding genome file in the genome directory
            matching_genome=$(find "$genome_dir" -maxdepth 1 -type f -name "${species_prefix}_*.fna" | head -n 1)

            if [ -n "$matching_genome" ]; then
                echo "Matching Genome FNA: $matching_genome"

                # Add your proTRAC_2.4.4.pl command with absolute path to genome file
                # Example:
                perl "$proTRAC_path" -map "$map_file" -genome "$matching_genome"

                echo "------------------------"  # Separator line
            else
                echo "No matching genome found for $map_file."
            fi
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
nohup bash ./proTRAC_automate.sh > Dyak_automate_proTRAC.log 2>&1 &
```

the one liner version for one file only
```
sudo perl proTRAC_2.4.3.pl -map ../../curana/Dana_ovaries_SRR23593057.sam -format SAM -genome ref/Dana_ref_GCA_018148915.1_ASM1814891v1_genomic.fna
```
