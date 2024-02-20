Extract the proTRAC outputs:
proTRAC created a folder for each sample and inside there is a file called results.table.  
Use this script to get the right identifiers to the results.table in each folder:  
**extract.sh**
```
#!/bin/bash

# Specify the path to the main directory containing the subdirectories
main_directory="/mnt/data2/sarah/proTRAC-0.05pdns"

# Loop through each subdirectory
for folder in "$main_directory"/*; do
    if [ -d "$folder" ]; then
        # Extract identifier and SSR number from the folder name
        identifier=$(echo "$folder" | awk -F'_' '{print substr($2, 1, 4)}')
        ssr_number=$(echo "$folder" | awk -F'_' '{for (i=1; i<=NF; i++) {if ($i ~ /^SRR/) {print $i; break}}}')

        # Check if results.table exists in the folder
        results_table="$folder/results.table"
        if [ -f "$results_table" ]; then
            # Rename results.table to include the identifier and SSR number
            new_name="$folder/${identifier}_SRR${ssr_number}_results.table"
            mv "$results_table" "$new_name"
            echo "Renamed $results_table to $new_name"
        else
            echo "results.table not found in $folder"
        fi
    fi
done
```
then move all files to a new folder called results  
(careful I moved them, I didn't copy them)
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
            mv $results_table "$results_directory/"
            echo "Moved $results_table to $results_directory/"
        fi
    fi
done
```

change the name as there are to many SRR in the name:
```
sudo rename 's/SRRSRR/SRR/' /mnt/data2/sarah/proTRAC-0.05pdns/results/*_results.table
```

extract all infos from the ProTRAC outputs in one table:
```                                                          
#!/bin/bash

# Specify the path to the results directory
results_directory="/mnt/data2/sarah/proTRAC-0.05pdns/results"

# Create an output file
output_file="$results_directory/summary_output.txt"
echo -e "SRRnumber\tCluster_bp\tCluster_percent\tNonIdentical_bp\tNonIdentical_percent\tReads_bp\tReads_percent" > "$output_file"

# Loop through each file in the results directory
#!/bin/bash

# Specify the path to the results directory
results_directory="/mnt/data2/sarah/proTRAC-0.05pdns/results"

# Create an output file
output_file="$results_directory/summary_output.txt"
echo -e "SRRnumber\tCluster_bp\tCluster_%\tNonIdent_bp\tNonIdent_%\tReads_bp\tReads_%" > "$output_file"

# Loop through each file in the results directory
for file in "$results_directory"/*_results.table; do
    # Extract species and SRRnumber from the file name
    species_srr=$(echo "$file" | awk -F'/' '{print $NF}' | awk -F'_' '{print $1"_"$2}')

    # Extract values from the file
    # ... (your existing code)

    # Extract values from the third last sentence
    cluster_bp_percent_line=$(tail -n 3 "$file" | head -n 1)  # Get the third last line
    cluster_bp=$(echo "$cluster_bp_percent_line" | awk -F': ' '{print $2}' | awk '{print $1}')  # Extract digits between ": " and "bp"
    cluster_percent=$(echo "$cluster_bp_percent_line" | awk -F'[()]' '{print $2}')  # Extract numbers between "(" and ")"

    # Extract values from the second last sentence
    non_identical_bp_percent_line=$(tail -n 2 "$file" | head -n 1)  # Get the second last line
    non_identical_bp=$(echo "$non_identical_bp_percent_line" | awk -F': ' '{print $2}' | awk '{print $1}')  # Extract digits between ": " and "bp"
    non_identical_percent=$(echo "$non_identical_bp_percent_line" | awk -F'[()]' '{print $2}')  # Extract numbers between "(" and ")"

    # Extract values from the last sentence
    reads_bp_percent_line=$(tail -n 1 "$file")  # Get the last line
    reads_bp=$(echo "$reads_bp_percent_line" | awk -F': ' '{print $2}' | awk '{print $1}')  # Extract digits between ": " and "bp"
    reads_percent=$(echo "$reads_bp_percent_line" | awk -F'[()]' '{print $2}')  # Extract numbers between "(" and ")"

    # Print values to the output file
    echo -e "$species_srr\t$cluster_bp\t$cluster_percent\t$non_identical_bp\t$non_identical_percent\t$reads_bp\t$reads_percent" >> "$output_file"
done
```

extract the results to my computer:
```
scp -o ProxyJump=NAME@pgnport.vu-wien.ac.at sarah@10.120.3.47:/mnt/data2/sarah/proTRAC-0.05pdns/results/summary_output.txt Desktop/
```
