```
#!/bin/bash

# Full path to proTRAC_2.4.4.pl
proTRAC_path="/home/vetlinux04/Sarah/softwares/proTRAC_2.4.4.pl"

# Path to the genome directory
genome_dir="/home/vetlinux04/Sarah/trapmodel/ref"

# Iterate through .map files in ovaries_without_adapter/clipped_output
for map_file in /home/vetlinux04/Sarah/trapmodel/ovaries_without_adapter/clipped_output/*.map; do
    # Check if the .map file exists
    if [ -e "$map_file" ]; then
        echo "Processing map file: $map_file"
        
        # Extract the species prefix from the map file
        species_prefix=$(basename "$map_file" | cut -c 1-4)

        # Find the corresponding genome file in the genome directory
        matching_genome=$(find "$genome_dir" -maxdepth 1 -type f -name "${species_prefix}_*.fna" | head -n 1)

        if [ -n "$matching_genome" ]; then
            echo "Matching Genome FNA: $matching_genome"

            # Add your proTRAC_2.4.4.pl command with absolute path to genome file
            # Example:
            perl "$proTRAC_path" -map "$map_file" -genome "$matching_genome" -pdens 0.2 -clsize 1000 -clstrand 0

            echo "------------------------"  # Separator line
        else
            echo "No matching genome found for $map_file."
        fi
    fi
done
```

run it
```
nohup ./03_automate_proTRAC.sh /home/vetlinux04/Sarah/softwares > 03.log 2>&1 &
```

the one liner version for one file only
```
sudo perl proTRAC_2.4.3.pl -map ../../curana/Dana_ovaries_SRR23593057.sam -format SAM -genome ref/Dana_ref_GCA_018148915.1_ASM1814891v1_genomic.fna
```

better /newer oneliner for testinf the right pdens for Dmel (we want around 3% piRNA clusters, so we try different pdens)

```
perl proTRAC_2.4.4.pl -map ../trapmodel/ovaries_without_adapter/clipped_output/Dmel_ovaries_SRR23593026_trimmed_final.fq-collapsed.fastq.no-dust.map -genome ../trapmodel/ref/Dmel_ref_GCA_018904365.1_ASM1890436v1_genomic.fna -pdens 0.2 -clsize 1000 -clstrand 0
```
