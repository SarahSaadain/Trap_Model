get the beginning and end of piRNA clusters from proTRAC output (ovaries)

first get the .gtf files and rename them to the right species and SRR number
```
#!/bin/bash

# Specify the path to the main directory containing the subdirectories
main_directory="/mnt/data2/sarah/proTRAC-0.2pdns"

# Loop through each subdirectory
for folder in "$main_directory"/*; do
    if [ -d "$folder" ]; then
        echo "Processing directory: $folder"

        # Extract the required parts from the directory name
        prefix=$(echo "$folder" | grep -oP 'proTRAC_\K\w{4}')
        suffix=$(echo "$folder" | grep -oP 'ovaries_\KSRR\d+' | cut -d'_' -f1)

        # Find and rename .table files
        for file in "$folder"/*.gtf; do
            if [ -f "$file" ]; then
                # Construct new filename
                new_filename="${prefix}_${suffix}.gtf"

                # Rename the file
                mv "$file" "$folder/$new_filename"
                echo "Renamed $file to $new_filename"
            fi
        done
    fi
done
```

moving all .gtf files to a new directory
```
#!/bin/bash

# Define source and destination directories
source_dir="/mnt/data2/sarah/proTRAC-0.2pdns"
dest_dir="/mnt/data2/sarah/proTRAC-0.2pdns/gtf_files"

# Iterate through subdirectories starting with "proTRAC" and copy .gtf files to destination directory
for dir in "$source_dir"/proTRAC*; do
    if [ -d "$dir" ]; then
        cp "$dir"/*.gtf "$dest_dir"
    fi
done
```

I ran an R script (TrapModelFigure3.R) to only extract the columns that I need and transformed them from .gtf to .bed

then I used the smRNA files from ovaries and somatic follicle cells (from the Toolbox, before the sRNA mapper) to sort the reads between 23-29nt
```
#!/bin/bash

# Define input and output directories
input_dir="/home/vetlinux04/Sarah/trapmodel/ovaries_without_adapter_old"
output_dir="/home/vetlinux04/Sarah/trapmodel/ovaries_without_adapter_old"
#input_dir="/home/vetlinux04/Sarah/trapmodel/somatic_follicle_with_adapter"
#output_dir="/home/vetlinux04/Sarah/trapmodel/somatic_follicle_with_adapter"

# Process each file in the input directory ending with ".no-dust"
for file in "$input_dir"/*.no-dust; do
    # Extract filename without extension
    filename=$(basename "$file" .no-dust)
    
    # Create output file name with the "_piRNA" suffix
    output_file="${output_dir}/${filename}_piRNA"
    
    # Filter reads between 23 and 29 nucleotides
    awk 'BEGIN {RS="@"} {if (length($2) >= 23 && length($2) <= 29) print "@"$0}' "$file" > "$output_file"
    
    # Debug: Print output file name
    echo "Output file created: $output_file"
done
```

doublecheck
```
awk '/^@/ {getline; print length}' Dana_ovaries_SRR23593056_trimmed.fq-collapsed.fastq_piRNA
```

and then I use another mapper because the .map output of sRNAmapper is not ideal

first index
```
for file in *.fna; do bwa index "$file"; done
```

then map using map.sh
```
#!/bin/bash
# Check for correct number of arguments
if [ "$#" -ne 1 ]; then
 echo "Usage: $0 <scripts_folder>"
 exit 1
fi
# Assigning arguments to variables
INPUT_DIR="/home/vetlinux04/Sarah/trapmodel/sRNA_files"
GENOME_DIR="/home/vetlinux04/Sarah/trapmodel/ref"
SCRIPTS_FOLDER="$1" # The folder containing the scripts
# Iterate through the input directory
for fastq_file in "${INPUT_DIR}"/*piRNA; do
  # Extract the species prefix from the fastq file name
  species_prefix=$(basename "$fastq_file" | cut -c 1-4)
  # Find the corresponding genome file in the genome directory with the same species prefix
  matching_genome=$(find "${GENOME_DIR}" -maxdepth 1 -type f -name "${species_prefix}_*.fna" | head -n 1)
  if [ -n "$matching_genome" ]; then
    echo "Matching Genome FNA: $matching_genome"
    # Execute BWA-MEM command
    bwa mem -t 4 "${matching_genome}" "${fastq_file}" > "${fastq_file}_aligned.sam"
    echo "------------------------" # Separator line
  else
    echo "No matching genome found for $fastq_file."
  fi
done
```

then use sort.sh (I split it up in sort.sh and sort2.sh for the sRNA_files and the gtf_files)
to convert .sam files to sorted BAM
to sort and merge BED files
```
#!/bin/bash
# Define paths to the .sam and .bed files
SAM_DIR="/home/vetlinux04/Sarah/trapmodel/sRNA_files"
BED_DIR="/home/vetlinux04/Sarah/trapmodel/gtf_files"
# Convert SAM files to sorted BAM
for sam_file in "${SAM_DIR}"/*.sam; do
    # Extract filename without extension
    filename=$(basename "$sam_file" .sam)
    # Convert SAM to sorted BAM
    samtools view -bS "$sam_file" | samtools sort -o "${SAM_DIR}/${filename}.sorted.bam"
done
# Sort and merge BED files
for bed_file in "${BED_DIR}"/*.bed; do
    # Extract filename without extension
    filename=$(basename "$bed_file" .bed)
    # Sort BED file
    bedtools sort -i "$bed_file" > "${BED_DIR}/${filename}.sorted.bed"
    # Merge sorted BED file
    bedtools merge -i "${BED_DIR}/${filename}.sorted.bed" > "${BED_DIR}/${filename}.sorted.merged.bed"
done
```
renamed the files using this:

```
#!/bin/bash
#Directory where the sorted BAM files are located
BAM_DIR="/home/vetlinux04/Sarah/trapmodel/sRNA_files"

#Loop through each BAM file
for file in "$BAM_DIR"/*_aligned.sorted.bam; do
    # Extract the filename without the directory path
    filename=$(basename -- "$file")
    
    # Extract the part of the filename before "_trimmed" and add ".bam"
    new_filename="${filename%%_trimmed*}.bam"
    
    # Copy and rename the file
    cp "$file" "$BAM_DIR/$new_filename"
done
```

indexed the ref files using samtools faidx

```
#!/bin/bash

#Directory containing the .fna files
REF_DIR="/home/vetlinux04/Sarah/trapmodel/ref"

#Loop through each .fna file in the directory
for fna_file in "${REF_DIR}"/*.fna; do
    # Check if the file exists
    if [ -e "$fna_file" ]; then
        # Run samtools faidx on the .fna file
        samtools faidx "$fna_file"
    else
        echo "File not found: $fna_file"
    fi
done
```
