##extract files from NCBI / SRA:

Go to the Project's SRA page and mark all files you want, download the metadata and you will get a file calles SraRunTable.txt
then use this code to extract all the accession names:
```
awk -F',' 'NR>1 {print $1}' SraRunTable.txt > SRR_numbers.txt
````
confirm the number of accessions is the same as on the homepage:
```
wc -l SRR_numbers.txt
```

go to ENA Browser (not NCBI, its unnecessary compliacted) and download all samples
https://www.ebi.ac.uk/ena/browser/view/PRJNA675888

go to NCBI Browser and download all assemblies  
https://www.ncbi.nlm.nih.gov/bioproject/PRJNA675888/

For the assemblies I used the 101 drosophilid genome dataset, except Dazt (where the assembly I used is GCA_005876895) and Dsuz (where there is two different assemblies vailable
GCF_000472105	GCF_013340165)  

change names
```for file in *.fastq.gz; do mv "$file" "Dana_ovaries_$file"; done```

and then move them to folder for ovaries

```mv D*.fastq.gz ../../ovaries/```

run trimgalore to remove adapters
```
find /mnt/data2/different_species/ovaries/ -type f -name "*.fastq" -exec trim_galore --small_rna --stringency 6 -e 0.1 --output_dir /mnt/data2/different_species/ovaries/trimmed/ {} \;
```
