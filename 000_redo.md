from the downloaded files (in mnt/data2/sarah/different_species/ovaries/march2024) I usw three steps in trim galore, following this: https://www.nature.com/articles/s41467-023-42787-1#data-availability by first doing:
_trim1.sh_
```
for file in *.fastq; do
    trim_galore --stringency 30 -e 0.1 -a TGCTTGGACTACATATGGTTGAGGGTTGTA --length 18 -q 0 --output_dir trimmed_output --basename "$output_name" "$file"
done
```

and then do the following code on the files in the trimmed_output folder
*trim2.sh*
```
for file in *_trimmed.fq; do
    # Define output filename with the _trim2 suffix
    output_name="${file%_trimmed.fastq}_trim2.fastq"

    # Run Trim Galore with specified settings and output name
    trim_galore --stringency 5 -e 0.1 --length 18 --max_length 35 -q 0 --output_dir trimmed_output2 --basename "$output_name" "$file"
done
```

and then
```
for file in *_trim2.fastq; do
    # Define output filename with the _clip suffix
    output_name="${file%_trim2.fastq}_trim3.fastq"

    # Run Trim Galore with specified settings and output name
    trim_galore --clip_R1 2 --three_prime_clip_R1 2 --output_dir clipped_output --basename "$output_name" "$file"
done



```
