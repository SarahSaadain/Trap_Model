Extract the proTRAC outputs:
proTRAC created a folder for each sample and inside there is a file called results.table
Use this script to get the right identifiers to the results.table in ech folder:

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
