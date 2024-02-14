download the TE libraries for the relevant species from here:
https://github.com/susbo/Drosophila_TE_libraries/tree/main/FASTA

use this script to index the files (done on my laptop)
```
        #!/bin/bash

# Check if samtools is installed
if ! command -v samtools &> /dev/null; then
    echo "samtools not found. Please install samtools."
    exit 1
fi

# Specify the directory containing the .fa.gz files
input_directory="/Users/ssaadain/Desktop/Lopik"

# Check if the directory exists
if [ ! -d "$input_directory" ]; then
    echo "Directory not found: $input_directory"
    exit 1
fi

# Unzip and index all .fa.gz files in the specified directory
for gz_file in "$input_directory"/*.fa.gz; do
    if [ -e "$gz_file" ]; then
        # Unzip the file
        gunzip -c "$gz_file" > "${gz_file%.gz}"

        # Index the uncompressed file
        samtools faidx "${gz_file%.gz}"

        # Remove the uncompressed file if you don't need it
        # rm "${gz_file%.gz}"

        echo "Indexing completed for $gz_file."
    else
        echo "No .fa.gz files found in the directory."
        exit 1
    fi
done

echo "Indexing of .fa.gz files in $input_directory is complete."

```

then use:


```
import argparse

# Parse command-line arguments
parser = argparse.ArgumentParser(description="Filter FASTA file.")
parser.add_argument("fasta", help="Path to fasta file")
parser.add_argument("selected", help="Path to the file with the fasta sequences to select")
parser.add_argument("output", help="Path to the output file")
args = parser.parse_args()

def filter(fasta, selection, out):
    with open(fasta, 'r') as fasta_file, open(selection) as selected, open(out, 'w') as output_file:
        genes = []
        for line in selected:
            gene = line[0:-1]
            genes.append(gene)
        write_line = False
        for line in fasta_file:
            line = line.strip()
            if line.startswith(">"):
                gene_name = line[1:]
                if gene_name in genes:
                    write_line = True
                    output_file.write(line + "\n")  # Write the current line to output
            elif write_line:
                output_file.write(line + "\n")  # Write the next line to output
                write_line = False

filter(args.fasta, args.selected, args.output)
```

