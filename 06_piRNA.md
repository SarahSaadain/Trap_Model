Extract the proTRAC outputs:
proTRAC created a folder for each sample and inside there is a file called results.table.  
Use this script to get the right identifiers to the results.table in each folder:  
**extract.sh**
```
#!/bin/bash

# Specify the path to the main directory containing the subdirectories
main_directory="/home/vetlinux04/Sarah/softwares/proTrac_output"

# Loop through each subdirectory
for folder in "$main_directory"/*; do
    if [ -d "$folder" ]; then
        echo "Processing directory: $folder"

        # Extract the required parts from the directory name
        prefix=$(echo "$folder" | grep -oP 'proTRAC_\K\w{4}')
        suffix=$(echo "$folder" | grep -oP 'ovaries_\KSRR\d+' | cut -d'_' -f1)

        # Find and rename .table files
        for file in "$folder"/*.table; do
            if [ -f "$file" ]; then
                # Construct new filename
                new_filename="${prefix}_${suffix}.table"

                # Rename the file
                mv "$file" "$folder/$new_filename"
                echo "Renamed $file to $new_filename"
            fi
        done
    fi
done
```

for the FC, I used this extraction.sh script and it worked nicely:
```
#!/bin/bash

# Loop through each directory
for folder in */; do
    echo "Processing directory: $folder"

    # Extract name from directory name
    name=$(echo "$folder" | grep -oP 'proTRAC_\K[^_]+(?=_FC\d+)')

    # Check if name is empty
    if [ -z "$name" ]; then
        echo "Error: Failed to extract name from directory name: $folder"
        continue
    fi

    echo "Name: $name"

    # Check if results.table exists in the directory
    if [ -f "$folder/results.table" ]; then
        echo "results.table found in $folder"
        # Construct new filename
        new_filename="${name}_$(echo "$folder" | grep -oP 'SRR\d+')"
        new_filename="${new_filename}.table"
        
        # Check if the new filename already exists
        if [ -f "$folder/$new_filename" ]; then
            echo "Error: $new_filename already exists in $folder. Cannot rename."
        else
            # Rename the file
            mv "$folder/results.table" "$folder/$new_filename"
            echo "Renamed results.table to $new_filename"
        fi
    else
        echo "results.table not found in $folder"
    fi
done
```


then copy all files to a new folder called results  
**results.sh**
```
#!/bin/bash

# Specify the path to the main directory containing the subdirectories
main_directory="/mnt/data2/sarah/proTRAC-0.05pdns"

# Create a directory named 'results' if it doesn't exist
results_directory="$main_directory/results"
mkdir -p "$results_directory"

# Loop through each subdirectory
for folder in "$main_directory"/*; do
    if [ -d "$folder" ]; then
        # Check if _results.table exists in the folder
        results_table="$folder/*_results.table"
        if [ -f $results_table ]; then
            # Move _results.table to the 'results' directory
            cp $results_table "$results_directory/"
            echo "Moved $results_table to $results_directory/"
        fi
    fi
done
```

here the same I used for the FC
```
#!/bin/bash

# Specify the path to the main directory containing the subdirectories
main_directory="/home/vetlinux04/Sarah/softwares/proTRAC_output_FC_copy"

# Create a directory named 'results' if it doesn't exist
results_directory="$main_directory/results"
mkdir -p "$results_directory"

# Loop through each subdirectory
for folder in "$main_directory"/*; do
    if [ -d "$folder" ]; then
        # Loop through files in the subdirectory
        for file in "$folder"/*; do
            if [ -f "$file" ] && [[ "$file" == *.table ]]; then
                # Copy .table to the 'results' directory
                cp "$file" "$results_directory/"
                echo "Copied $file to $results_directory/"
            fi
        done
    fi
done
```

extract all infos from the ProTRAC outputs in one table:  
**predicted_clusters.sh**
```                                                          
#!/bin/bash

# Specify the path to the results directory
results_directory="/mnt/data2/sarah/proTRAC-0.2pdns/results"

# Create an output file
output_file="$results_directory/predicted_clusters_pdens0.2.txt"
echo -e "SRRnumber\tpredicted_clusters\tCluster_bp\tCluster_%\tNonIdent_bp\tNonIdent_%\tReads_bp\tReads_%" > "$output_file"

# Loop through each file in the results directory
for file in "$results_directory"/*.table; do
    # Extract species and SRRnumber from the file name
    species_srr=$(echo "$file" | awk -F'/' '{print $NF}' | awk -F'_' '{print $1"_"$2}')

    # Extract values from the third last sentence
    cluster_bp_percent_line=$(tail -n 3 "$file" | head -n 1)  # Get the third last line
    cluster_bp=$(echo "$cluster_bp_percent_line" | awk -F': ' '{print $2}' | awk '{print $1}')  # Extract digits between ": " and "bp"
    cluster_percent=$(echo "$cluster_bp_percent_line" | awk -F'[()]' '{print $2}')  # Extract numbers between "(" and ")"
    
    # Extract values from the third last sentence for predicted_clusters
    predicted_clusters=$(echo "$cluster_bp_percent_line" | awk -F'of ' '{print $2}' | awk '{print $1}')  # Extract digits between "of" and "predicted"

    # Extract values from the second last sentence
    non_identical_bp_percent_line=$(tail -n 2 "$file" | head -n 1)  # Get the second last line
    non_identical_bp=$(echo "$non_identical_bp_percent_line" | awk -F': ' '{print $2}' | awk '{print $1}')  # Extract digits between ": " and "bp"
    non_identical_percent=$(echo "$non_identical_bp_percent_line" | awk -F'[()]' '{print $2}')  # Extract numbers between "(" and ")"

    # Extract values from the last sentence
    reads_bp_percent_line=$(tail -n 1 "$file")  # Get the last line
    reads_bp=$(echo "$reads_bp_percent_line" | awk -F': ' '{print $2}' | awk '{print $1}')  # Extract digits between ": " and "bp"
    reads_percent=$(echo "$reads_bp_percent_line" | awk -F'[()]' '{print $2}')  # Extract numbers between "(" and ")"

    # Print values to the output file
    echo -e "$species_srr\t$predicted_clusters\t$cluster_bp\t$cluster_percent\t$non_identical_bp\t$non_identical_percent\t$reads_bp\t$reads_percent" >> "$output_file"
done
```

extract the results to my computer:
```
scp -o ProxyJump=NAME@pgnport.vu-wien.ac.at sarah@10.120.3.47:/mnt/data2/sarah/proTRAC-0.05pdns/results/summary_output.txt Desktop/
```
