automate the sRNAmapper for all files
02_automate_sRNAmapper.sh  

```
#!/bin/bash

# Check for correct number of arguments
if [ "$#" -ne 1 ]; then
  echo "Usage: $0 <scripts_folder>"
  exit 1
fi

# Assigning arguments to variables
SCRIPTS_FOLDER="/home/vetlinux04/Sarah/softwares"
INPUT_DIR="/home/vetlinux04/Sarah/trapmodel/ovaries_without_adapter"
GENOME_DIR="/home/vetlinux04/Sarah/trapmodel/ref"

# Iterate through the input directory
for fastq_file in "${INPUT_DIR}"/*collapsed.fastq.no-dust; do
    # Extract the species prefix from the fastq file name
    species_prefix=$(basename "$fastq_file" | cut -c 1-4)

    # Find the corresponding genome file in the genome directory with the same species prefix
    matching_genome=$(find "${GENOME_DIR}" -maxdepth 1 -type f -name "${species_prefix}_*.fna" | head -n 1)

    if [ -n "$matching_genome" ]; then
        echo "Matching Genome FNA: $matching_genome"
        # Execute sRNAmapper.pl command
        perl "${SCRIPTS_FOLDER}/sRNAmapper.pl" -input "${fastq_file}" -genome "${matching_genome}" -alignments best
        echo "------------------------"  # Separator line
    else
        echo "No matching genome found for $fastq_file."
    fi
done
```


to have executing permissions do:
```
chmod +x 02_automate_sRNAmapper.sh
```


run it
```
nohup ./02_automate_sRNAmapper.sh /home/vetlinux04/Sarah/softwares > 02.log 2>&1 &
```


one liner for trying single files and settings:
```
perl sRNAmapper.pl -input ovaries/trimmed/Dana_ovaries_SRR23593056_trimmed.fq -genome ref/Dana_ref_GCA_018148915.1_ASM1814891v1_genomic.fna -alignments best
```


