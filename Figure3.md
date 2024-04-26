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

then I used the smRNA files (from the Toolbox, before the sRNA mapper) to sort the reads between 23-29nt
```
#!/bin/bash

# Define input and output directories
input_dir="/home/vetlinux04/Sarah/trapmodel/ovaries_without_adapter_old"
output_dir="/home/vetlinux04/Sarah/trapmodel/ovaries_without_adapter_old"

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

and then I use another mapper because the .map output of sRNAmapper is not ideal
