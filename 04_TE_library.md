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

make sure to use this before:
```chmod +x index.sh```

then execute the script by
```
./index.sh
```


then use this to filter for the size:

```
#!/bin/bash

# Directory containing .fai files
input_directory="/Users/ssaadain/Desktop/Lopik"

# Check if the directory exists
if [ ! -d "$input_directory" ]; then
    echo "Directory not found: $input_directory"
    exit 1
fi

# Process all .fai files in the directory
for fai_file in "$input_directory"/*.fai; do
    if [ -e "$fai_file" ]; then
        # Get the base name of the input file (excluding the path)
        base_name=$(basename "$fai_file")
        
        # Form the output file path in the same directory
        output_file="$input_directory/${base_name%.fai}_filtered.txt"
        
        echo "Processing $fai_file:"
        awk '$2 > 1000' "$fai_file" > "$output_file"
        echo "------------------------"
    else
        echo "No .fai files found in the directory."
        exit 1
    fi
done

echo "Output files saved in the same directory as the input."

````

and again do chmod before
```
chmod +x extract_lines.sh
```

and do this to execute
```
./extract_lines.sh
```

keep only the first column
```
for file in *.fa_filtered.txt; do awk '{print $1}' "$file" > "${file%.fa_filtered.txt}_temp.txt" && mv "${file%.fa_filtered.txt}_temp.txt" "$file"; done
```
---------
I need to defragment before parsing with:

```
 python /Users/ssaadain/Downloads/defragment-fasta.py Dana.GCF_003285975.fa Dana.GCF_003285975_defrag.fa
```

how to loop it over all:
```
for file in *.fa; do python defragment-fasta.py "$file" "${file%.fa}_defrag.fa"; done
```

this is the actual defrag script:
```
import argparse

# Parse command-line arguments
parser = argparse.ArgumentParser(description="Filter FASTA file.")
parser.add_argument("fasta", help="Path to fasta file")
parser.add_argument("output", help="Path to the output file")
args = parser.parse_args()

def filter(fasta, out):
    with open(fasta, 'r') as fasta_file, open(out, 'w') as output_file:
        for i, line in enumerate(fasta_file):
            line = line.strip()
            if line.startswith(">"):
                if i>0:
                    output_file.write("\n")
                output_file.write(line + "\n")
            else:
                output_file.write(line)

filter(args.fasta, args.output)
```

then do the parsing fasta
```
python /Users/ssaadain/Downloads/filter-fasta.py Dana.GCF_003285975_defrag.fa Dana.GCF_003285975.fa_filtered.txt Dana.GCF_003285975_parsed.fa
```
loop it over all files with look_parsing.sh
```
for defrag_file in *_defrag.fa; do
    species_code="${defrag_file:0:4}"
    filtered_file="${species_code}_filtered.txt"
    parsed_file="${species_code}_parsed.fa"
    python filter-fasta.py "$defrag_file" "$filtered_file" "$parsed_file"
done
```



this is the actual (parsing) filer-faster.py
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
and then upload all references (the right ones) and the filterted TE libraries form the parsing to vetlinux27

there run repeatmasker like this, where $i is the reference and repeatlibrary/teseqs.fasta are the parsed and filtered libraries from the previous step
```
RepeatMasker -pa 20 -no_is -s -nolow -dir out -lib repeatlibrary/teseqs.fasta $i
```




